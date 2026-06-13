# Grounding Document — AI Agents for Local Chicago Restaurants

**Purpose:** This is the shared context document for every AI agent (and every AI coding session) working in this repo. Read it before designing or implementing anything. It defines who the customer is, the systems we integrate with (Toast, QuickBooks, bank), the two core calculation engines (menu/ingredient costing and invoice processing), the shared data model that ties them together, and the rules every agent must follow.

Individual agent specs (e.g. `agent3_inventory_burn_calculator.md`) describe *what one agent does*. This document describes *the world all agents live in*.

---

## 1. Who We Serve

Independent Chicago restaurants, typically:

- 1–3 locations, $800K–$3M annual revenue per location
- Owner-operator or a single GM doing the back office; **no IT staff, no bookkeeper on site**
- Toast POS at the counter, QuickBooks Online for accounting, **Excel for everything else**: recipe costs, inventory counts, vendor price lists, schedules
- The owner's spare attention is the scarcest resource in the building

**Design consequences:**

1. **Excel is the user interface, not the database.** Owners live in spreadsheets. Agents deliver outputs as Excel files (or simple emails/texts) and accept inputs as Excel files. Internally, agents keep a canonical structured store (SQLite/Postgres/structured workbook — see §7); never treat the owner's hand-edited sheet as the source of truth.
2. **The 10-minute rule.** Nothing an agent asks of the owner may take more than ~10 minutes per week (e.g. the weekly spot-count of 10 high-value items in Agent 3). If a workflow needs more human input than that, redesign it.
3. **Read-only first, write with approval.** Agents earn trust by delivering reports before they touch anything. Any write into a system of record (QuickBooks especially) requires explicit human approval per transaction or per batch. No agent ever moves money.
4. **Plain language.** Outputs are written for a restaurant owner, not an accountant. Dollar impact first, jargon never ("You lost ~$340 of chicken this week vs. what you sold"), with detail available below.

---

## 2. The Agent Platform

Known agents (numbering from existing specs; reconcile as new specs are written):

| # | Agent | Role | Spec |
|---|-------|------|------|
| 1 | Menu Cost Calculator | Builds and maintains the recipe database; computes plate cost and food-cost % per menu item | not yet written |
| 2 | Invoice Processor | Extracts line items from supplier invoices; normalizes prices; drafts QuickBooks bills | not yet written |
| 3 | Inventory Burn Calculator | Nightly theoretical-vs-actual ingredient consumption; flags waste/theft | `agent3_inventory_burn_calculator.md` |
| 4 | Daily Sales Close & Bookkeeping | Nightly Toast→QBO daily sales journal; clearing-account & deposit reconciliation; monthly sales-tax true-up | `agent4_daily_sales_close.md` |
| 5 | Tip & Payroll Compliance | Per-shift tip-pool distribution → payroll-ready file; FLSA/Chicago wage compliance; FICA tip credit | `agent5_tip_payroll_compliance.md` |
| 6 | Vendor Price Tracker | Watches normalized invoice prices for creep, spikes, and receiving shorts | not yet written |
| 7 | Daily P&L Briefing | Morning report rolling up sales, food cost, labor, waste | not yet written |

> **Resolved (2026-06-13):** Menu Cost Calculator is **Agent 1**; the Daily P&L Briefing is **Agent 7**. (Earlier drafts of the Agent 3 spec called both "Agent 1" — that conflict is now closed.)

**Dependency spine:** Invoice Processor (2), Menu Cost Calculator (1), and Daily Sales Close (4) are the foundation — they produce the normalized purchase data, the recipe database, and the correct revenue/tax/tips data that everything downstream (3, 5, 6, 7) consumes. Agent 4 is specifically upstream of Agent 5's tip-liability distribution and of the P&L Briefing. Build and stabilize the foundation first.

---

## 3. Toast POS Connection

Toast is the source of truth for **sales**: what was sold, when, for how much, and how it was paid.

### Access paths (pick per client at onboarding)

| Path | What it is | When to use |
|------|-----------|-------------|
| **Toast standard API access** | The restaurant requests API credentials for their own location(s) from Toast; mostly read-only REST access | Default for our model — credentials belong to the restaurant, we act on their behalf |
| **Nightly data export (SFTP)** | Toast drops CSV files (order details, item selection details, payment details, etc.) to an SFTP location nightly | Lowest-friction MVP path; no API approval, but data is T+1 and CSV schemas differ from the API |
| **Toast Partner Connect** | Formal integration-partner program | Only worth it at scale (many restaurants); long approval cycle |

> ⚠️ Toast's API access terms, pricing, and availability change. **Verify the current standard-API-access offering before committing a client** (see §10).

### API mechanics (standard API)

- Auth: OAuth2 client-credentials → bearer token from the Toast authentication endpoint; pass the restaurant's GUID in the `Toast-Restaurant-External-ID` header on every call.
- Key surfaces: **Orders API** (bulk orders by business date — the workhorse), **Menus/Configuration API** (menu items, GUIDs, prices, modifier groups), **Labor API** (shifts, if we ever do prime cost), **Stock API**.
- Item-level sales = orders → checks → selections. Each selection carries a menu-item GUID, quantity, price, and nested modifier selections.

### Gotchas (every Toast-touching agent must handle these)

- **Business date ≠ calendar date.** A 1:30 AM Saturday order belongs to Friday's business date. Always query and aggregate by business date.
- **Voids and refunds.** Exclude voided selections; handle refunds as negative adjustments, not deletions.
- **Modifiers are separate selections** nested under the parent. "Add bacon" consumes bacon — recipe mapping must cover modifiers, not just base items.
- **Comps/discounts** apply at check or selection level and affect revenue, not ingredient consumption — burn math uses quantities, P&L math uses net revenue. Don't conflate.
- **Late-syncing offline orders.** Don't pull "final" numbers at midnight sharp; pull in the early morning and re-pull the prior day once.
- **Menu item GUIDs change** if staff delete and recreate an item. Detect unmapped GUIDs and queue them for recipe mapping rather than silently dropping their sales.

---

## 4. QuickBooks Import

QuickBooks Online (QBO) is the source of truth for **money**: what was purchased, what was paid, and the P&L.

"QuickBooks import" in our products means two directions:

1. **Read** — chart of accounts, vendor list, historical bills, P&L reports (for the Daily Briefing and for baselining food cost).
2. **Write** — two entity types, both staged for human approval (below): **Bills** from processed supplier invoices (Agent 2, with the invoice PDF attached) and the **daily sales JournalEntry** from the Toast close (Agent 4), plus creating Vendors that don't exist yet. These are the highest-trust operations we perform.

### API mechanics

- REST + JSON, OAuth2 authorization-code flow; each client company = one `realmId`. Intuit provides sandbox companies — develop against sandbox, always.
- Key entities: `Vendor`, `Bill` (line items via `AccountBasedExpenseLineDetail`), `JournalEntry` (the daily sales journal from Agent 4), `Attachable` (attach the source invoice PDF to the Bill), `Account` (chart of accounts), `Item`, `Class`/`Department` (multi-location tracking), Reports API (`ProfitAndLoss`).
- Rate limits exist per realm (hundreds of requests/minute); use the batch endpoint for bulk writes.

### Write rules (non-negotiable)

- **A Bill — and a JournalEntry — posts the moment it's created; there is no draft state in QBO.** Therefore: every Bill and every daily sales journal is staged for human approval *outside* QBO (approval email / review sheet) and only written after the owner approves. Per-vendor auto-approve thresholds may be added later, opt-in only.
- **Idempotency:** set `DocNumber` = vendor invoice number (Bills) or business date (the daily sales JournalEntry) and query for it before creating. Never create a duplicate bill for the same vendor + invoice number, or a second sales journal for the same business date.
- **Never delete or modify** existing QBO transactions the agent didn't create.

### Chart-of-accounts reality

Owners' QBO files are messy. At onboarding, **read the actual chart of accounts** — don't assume textbook restaurant COA. Our canonical expense categories (which invoice lines map to, then to whatever the client's COA actually has):

`Food–Meat & Poultry · Food–Seafood · Food–Produce · Food–Dairy · Food–Dry/Grocery · Beverage–Alcohol · Beverage–NA · Paper & Disposables · Chemicals & Cleaning · Smallwares & Equipment · Other`

> ⚠️ If a client is on **QuickBooks Desktop** (not Online), none of the above applies — different integration entirely (Web Connector/QBXML). Identify this at onboarding before promising anything.

---

## 5. Bank Data

The bank account is the **ground truth check** on both Toast and QuickBooks. Primary use case: **deposit reconciliation** — confirming the money Toast says was earned actually landed.

### Access paths

| Path | Notes |
|------|-------|
| **Owner-uploaded CSV/OFX export** | Zero-integration MVP; every bank can export; owner does it weekly (fits the 10-minute rule) |
| **Plaid (or similar aggregator)** | Automated daily transaction feed; per-connection cost; verify current pricing |
| **Via QBO bank feed** | If the client's bank is already connected to QBO; API visibility into unmatched feed transactions is limited — treat as a fallback read, not a primary source |

### Reconciliation logic (the math agents must get right)

- **Card deposits arrive net of processing fees**, batched by business date, typically 1–2 business days later. Weekend batches often land together Monday/Tuesday. Match: `sum(card payments for business date b) − processing fees ≈ deposit on b+1..b+3`.
- **Third-party delivery** (DoorDash, Uber Eats, Grubhub) pays out weekly, net of commission, in separate deposits. Match against the platform's payout statement period, not daily sales.
- **Cash deposits** are manual and irregular — flag gaps gently (missing cash deposits are sensitive; report, don't accuse).
- What this catches: missing/short deposits, processing-fee creep, chargebacks, delivery-platform commission errors.

> **Owned by:** card-batch and clearing-account reconciliation lives in the Daily Sales Close (Agent 4); tip-payout reconciliation against the tips-payable liability lives in Tip & Payroll Compliance (Agent 5). This section is the shared logic both rely on.

---

## 6. Menu Item Calculation (Ingredient Breakdown)

The recipe database is the platform's most valuable asset — it's what turns sales data into ingredient consumption and invoice data into plate costs. Owned by the Menu Cost Calculator (Agent 1); consumed by Agents 3, 6, and the P&L Briefing.

### Data model

- **Ingredient** — canonical name, canonical unit (weight preferred: oz/lb/g), current EP cost per canonical unit, yield %, category (maps to §4 expense categories).
- **Recipe line** — (menu item or sub-recipe) × ingredient × quantity in recipe units.
- **Sub-recipes** (sauces, marinades, prep items) are first-class: a sub-recipe is an ingredient whose cost derives from its own recipe and batch yield. Cost rolls up recursively.
- **Modifier mappings** — modifiers that consume ingredients ("add bacon", "sub avocado") get their own recipe lines (see §3 gotchas).

### Core formulas

```
EP unit cost      = AP unit cost / yield%          (as-purchased → edible-portion)
Plate cost        = Σ (line qty × EP unit cost) + Q-factor
Food cost %       = plate cost / menu price
Theoretical use   = Σ over items sold (qty sold × recipe qty per item)   ← Agent 3's input
```

- **Yield (AP vs EP):** a $3.00/lb whole chicken at 70% yield costs $4.29/lb as servable meat. Use measured yields when the kitchen can provide them; published standards as defaults. Costing on AP prices without yield is the single most common error in restaurant Excel sheets — it understates plate cost.
- **Q-factor:** a small allowance (typically 2–4% of plate cost) for garnish, cooking oil, condiments — things too small to line-item.
- **Unit conversion:** every ingredient carries a conversion chain `purchase unit → inventory unit → recipe unit` (e.g. case `6/#10` → can → oz). Volume↔weight conversions need a density entry. Conversions live in one table; no agent hardcodes them.
- **Price updates:** when the Invoice Processor records a new price, ingredient cost updates to **latest price paid**, with an alert when a price moves enough to shift any plate's food cost % by ≥1 point. (Latest-price beats moving-average for alerting purposes — owners need to feel spikes, not have them smoothed away.)
- Target benchmarks for context in reports: food cost 28–35% of menu price; prime cost (food + labor) under ~60–65% of sales.

### Setup reality

Initial recipe entry is the big onboarding lift (4–6 hours, per the Agent 3 spec). Agents can draft recipes from menu descriptions to accelerate this, but **every drafted recipe is marked unverified until the owner confirms quantities** — burn variance math on guessed recipes produces false theft alarms, which destroys trust faster than anything.

---

## 7. Invoice Calculation

The Invoice Processor (Agent 2) turns supplier invoices (PDF, photo, email) into normalized, validated purchase data. Everything downstream — recipe costs, burn gaps, price tracking, QBO bills — depends on its accuracy.

### Extraction targets per invoice

Vendor, invoice number, invoice date, delivery date, line items (vendor item code, description, pack size, quantity, unit price, extended price, catch-weight if present), credits, taxes, total.

### Validation (hard gate)

```
Σ(line extended prices) + tax − credits == invoice total   (within $0.02)
```

If it doesn't balance, the invoice goes to a human-review queue. **Never silently pass an unbalanced invoice downstream** — a misread quantity poisons recipe costs and burn math simultaneously.

### Pack-size grammar (broadliner conventions)

| Notation | Meaning |
|----------|---------|
| `6/#10` | 6 × #10 cans (~109 oz each) per case |
| `4/5 LB` | 4 bags × 5 lb per case |
| `CW`, `AVG`, `CATCH WT` | Catch-weight: priced per lb, actual shipped weight on the invoice — use actual weight, not nominal |
| `EA / DZ / CS / FL` | each / dozen / case / flat |

Normalize every line to **cost per canonical unit** of the mapped ingredient (e.g. $/lb, $/oz, $/each). The mapping `(vendor, vendor item code) → ingredient` is persistent and grows over time; unmapped codes queue for one-time human mapping.

### Common vendors (Chicago)

Broadliners: Sysco Chicago, US Foods (Rosemont), Gordon Food Service, Performance Food Group. Local/specialty: Testa Produce, Fortune Fish & Gourmet, Midwest Foods. Plus Restaurant Depot / Costco / Jewel **receipts** — same pipeline, but expect terse receipt formats rather than line-item invoices.

### Outputs

1. Normalized purchase lines → ingredient price updates (§6), burn calculator received-quantities (Agent 3), price tracker (Agent 6)
2. GL-coded QBO Bill draft + attached PDF → owner approval → write (§4)
3. Credit/short detection: invoiced-but-not-delivered flags feed Agent 6 and the burn calculator's receiving-error checks

---

## 8. Shared Data Model & Join Keys

The joins that make the platform coherent — every agent reads/writes through these:

```
toast_item_guid        ↔  menu_item        (sales → recipes)
menu_item              ↔  recipe lines      ↔  ingredient
(vendor, item_code)    ↔  ingredient        (invoices → costs & received qty)
business_date              keys all sales, consumption, and receiving facts
qbo: vendor_ref, account_ref, bill DocNumber = vendor invoice number
```

- One canonical store per client (SQLite or Postgres; a rigorously structured workbook is acceptable only for the earliest MVP). Excel artifacts are generated *from* it and parsed *into* it — never used as the live database.
- All quantities stored in the ingredient's canonical unit; all money in cents; all dates as business dates where applicable.
- Unmapped anything (Toast GUID, vendor item code) goes to a visible mapping queue — silent drops are bugs.

---

## 9. Chicago Operating Context

Facts that affect the math; **rates change — verify current values before encoding** (§10):

- **Sales tax on prepared food** is layered: Illinois/Cook/Chicago base (~10.25%) + Chicago restaurant tax (0.50%) ≈ 10.75%, plus an additional ~1.00% MPEA food & beverage tax in the downtown zone (≈11.75% total). Agents reading Toast revenue must separate net sales from tax collected; agents validating invoices should expect tax-exempt resale purchases for food.
- **Tipped-wage phase-out:** Chicago's ordinance is phasing out the tip credit (stepping annually toward full minimum-wage parity, ~2028), and the city minimum wage adjusts every July 1. Labor cost is rising on a known schedule — relevant to any prime-cost or P&L reporting.
- **Seasonality:** strong summer/patio peak, deep January–February trough, Restaurant Week bump (late Jan–Feb). Variance and trend alerts should compare against seasonal baselines, not flat averages.

---

## 10. Verify Before Building (staleness checklist)

This document was written 2026-06-11 from training knowledge, not live verification. Before building on any of these, confirm current state:

- [ ] Toast standard API access: availability, pricing, scopes, rate limits (Toast changes this program)
- [ ] Toast nightly export: current CSV file set and schemas
- [ ] Toast Labor API: scopes for shifts/hours/tips (needed by Agents 4–5)
- [ ] QBO API: current minor version, rate limits, any Bill/Attachable/JournalEntry changes
- [ ] Plaid (or chosen aggregator): pricing and bank coverage
- [ ] Chicago/Cook/IL tax rates and MPEA zone boundaries — current rates
- [ ] Chicago minimum wage and tipped-wage (tip-credit phase-out) schedule — current figures
- [ ] DOL/FLSA tip-pool, 80/20 dual-jobs, and overtime regular-rate rules — current (Agent 5)
- [ ] Payroll processor import formats (Gusto/ADP/Paychex) — current templates (Agent 5)
- [ ] Delivery-platform marketplace-facilitator sales-tax remittance rules by state/platform (Agent 4 tax true-up)
- [ ] Per client: QuickBooks **Online vs Desktop**; Toast plan tier; which bank; payroll processor

---

## 11. Glossary

| Term | Meaning |
|------|---------|
| AP / EP | As-purchased / edible-portion (post-yield) quantity or cost |
| Yield % | Usable fraction of an ingredient after trim/cook loss |
| Q-factor | Small plate-cost allowance for garnish, oil, condiments |
| Plate cost | Total ingredient cost to produce one menu item |
| Food cost % | Plate cost ÷ menu price |
| Prime cost | Food cost + labor cost |
| Theoretical consumption | Ingredient use implied by sales × recipes |
| Burn gap | Theoretical vs actual inventory variance (Agent 3) |
| Catch-weight | Item priced per lb with actual weight determined at shipping |
| Broadliner | Full-line food distributor (Sysco, US Foods, GFS) |
| Business date | POS day that late-night sales roll into (≠ calendar date) |
| Realm ID | QBO's identifier for one company file |
