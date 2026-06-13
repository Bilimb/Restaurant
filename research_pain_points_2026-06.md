# Pain-Point Research & Proposed Agents 4–9 (June 2026)

**Status: DECIDED (2026-06-13).** Two agents accepted and specced; three deferred/reclassified. This document is retained as the research record; the roster in `GROUNDING.md` now reflects the decision below.

## DECISION (2026-06-13)

| Research proposal | Outcome | Final number | Spec |
|-------------------|---------|--------------|------|
| B — Daily Sales Close & Tax Guard (bookkeeping, #2) | **Accepted** | **Agent 4** | `agent4_daily_sales_close.md` |
| D — Tip & Payroll Compliance (#4) | **Accepted** | **Agent 5** | `agent5_tip_payroll_compliance.md` |
| A — Delivery Platform Auditor (#1) | **Reclassified** — not its own agent. Mostly a Toast-configuration problem solved per client at onboarding; folded into Agent 4 as (a) the onboarding **Toast Configuration Check** and (b) per-platform **clearing-account reconciliation** in the daily close. Deep per-order fee/refund auditing deferred. | — | — |
| C — Labor Cost Guardian (#3) | **Deferred** — useful but smaller; Toast already surfaces labor cost. Possible future lightweight feature: flag **labor-vs-sales patterns by hour/daypart**. Not built now. | — | — |
| E — Cash-Flow Forecaster (#5) | **Deferred** — the data already lives in Toast/QBO and is simply underused; this is an adoption gap, not a build priority right now. | — | — |
| Runner-up — Review & Reputation | **Deferred** (unchanged) — v2 candidate; needs data outside the Toast/QBO/bank spine. | — | — |

Numbering resolved at the same time: Menu Cost Calculator = Agent 1, Daily P&L Briefing = Agent 7 (closes the open issue that had previously called both "Agent 1"). Both accepted agents lean on the bank-data work (GROUNDING §5): Agent 4 reconciles deposits/clearing accounts, Agent 5 reconciles tip payouts against the tips-payable liability Agent 4 posts.

---

Research method: four parallel research passes — (1) r/restaurantowners + r/restaurateur, (2) r/KitchenConfidential, r/BarOwners, r/Bookkeeping, r/QuickBooks, r/smallbusiness, (3) non-Reddit sources (NRA, Independent Restaurant Coalition, Federal Reserve SBCS, DOL enforcement data, Harvard Business School, BlackBox Intelligence, 86 Repairs, practitioner forums), (4) competitor landscape + negative-review mining (G2/Capterra/Reddit). ~35 full threads read; vendor marketing excluded or labeled. A pain point qualified only if it appeared independently in at least two owner-voice sources **and** one disinterested source, and applies across restaurant types.

---

## 1. The strategic finding

Every product that delivers real back-office intelligence to restaurants costs **$300–$480+/month** (MarginEdge $330–480, Restaurant365 ~$10.9K/yr for 2 locations, MarketMan $199 + $500 setup, Craftable ~$300–500). Owners at our target size refuse, loudly and repeatedly:

> "MarginEdge looks great but I can't justify the $330–$480/month for our 51-seat bar/restaurant that averages $50k in sales a month." — r/restaurantowners, Feb 2025

xtraCHEF — the only cheap Toast+QBO-native option — has effectively collapsed since the Toast acquisition (G2 2.4/5, "I have Excel sheets that work better"). MarginEdge is testing a ~$100/mo small-operator tier, confirming they see the same gap.

**Our structural advantage:** every proposal below runs on data the owner already pays for (Toast, QBO, bank, payroll exports) and delivers through Excel/email. No new system of record, no per-seat SaaS. This is the wedge.

---

## 2. Validated universal pain points (cross-source triangulation)

| # | Pain point | Owner-voice evidence | Disinterested evidence | Covered today? |
|---|-----------|---------------------|----------------------|----------------|
| 1 | Third-party delivery: fees, opaque payouts, surprise "marketing fees," refund fraud, double-paid sales tax | 5+ dedicated threads, 200+ commenters; documented $4,676 tax double-payment; "campaigns running that I knew absolutely nothing about" | IRC 2025: ~9 in 10 independents cite surging app fees; FTC $25M Grubhub settlement (Dec 2024) | **No** (GROUNDING §5 mentions payout matching in passing) |
| 2 | POS→QBO daily close: tips/voids/comps/payment-type mapping, sales-tax reconciliation | Bookkeepers call it the hardest restaurant accounting problem; "errors just kept piling up undetected"; r/Bookkeeping threads describe owners handing over unusable monthly totals | Sales-tax audit cases; CA post-COVID audits on gross-vs-payout reporting | **No** (Agent 2 covers AP side only) |
| 3 | Labor cost: visibility and control (not scheduling UI) | "Really struggling to keep kitchen labor in check"; call-out threads are the highest-engagement topic in r/restaurantowners | NRA 2025: 96% cite rising labor cost; profitable vs unprofitable FSRs differ by 6.4 pts of labor % (34.2 vs 42.9) | **No** (Toast Labor API noted in GROUNDING as "if we ever do prime cost") |
| 4 | Tip pooling / payroll compliance | "I need to manually track all of the tips and enter them into Gusto every 2 weeks"; $85K back-wage case thread | DOL recovered **$274M** in restaurant back wages in 2024; tip-pool violations a leading FLSA category | **No** |
| 5 | Cash-flow fragility, no forward visibility | "My accountant said to hold out for 3 more months — I am going to beg my landlord to lower my rent" | NRA: 39% of operators unprofitable in 2024, 53% carry pandemic debt; Fed SBCS: 51% cite uneven cash flow; Cornell: 26% of failures = poor financial management | **Partial** (Daily P&L is backward-looking only) |
| 6 | Reviews / reputation | Multiple threads on fake reviews, Yelp distrust, response time-sink | Harvard: +1 Yelp star → 5–9% revenue, **independents only** | **No** |
| 7 | Prime-cost squeeze (food side) | Constant; highest-engagement venting threads | NRA: food costs +30% since 2019 | **Yes** — Agents 1/2/3/6 + P&L. Research strongly validates the existing roster. |
| 8 | Inventory shrinkage / theft | "Employees eating profits" — 205 comments | — | **Yes** — Agent 3 validated. |
| 9 | Hiring no-shows / turnover | 1,000+ commenters across threads | BlackBox: 96% annualized hourly turnover; $2,305/hourly replacement cost | No — but poor software fit (see §5) |
| 10 | Equipment breakdown costs | Recurring | 86 Repairs: $26B/yr industry cost, avg emergency call $754 | No — niche fit (see §5) |

---

## 3. Proposed agents (the 5)

Numbering fills the existing 4–5 gap and continues after Agent 6. Final numbers to be agreed before any spec is written.

### Proposal A — Agent 4: Delivery Platform Auditor & Reconciler

**Pain:** #1 above. Platforms net-settle weekly with no line-item transparency; commissions 15–30%; self-serve "marketing campaigns" appear unasked; refund fraud is robo-approved against the merchant; marketplace-facilitator sales tax differs by platform and state, causing documented double remittance.

**What it does:**
- Ingests DoorDash/Uber Eats/Grubhub payout reports (CSV/portal exports — owner forwards weekly, fits 10-minute rule) plus the matching orders from Toast.
- Reconciles three-way: platform gross orders ↔ payout deposit in bank feed ↔ Toast records. Flags: effective take-rate drift, new/unauthorized marketing fees, refunds/chargebacks worth disputing (with an evidence pack: order, timestamp, items), missing payouts.
- Sales-tax guard: determines per-platform who remitted tax (marketplace facilitator rules) and outputs the correct taxable-sales number for the IL ST-1 — directly preventing the double-payment failure mode.
- Output: weekly Excel "Delivery Scorecard" — net margin per platform per order, dollars lost to fees/refunds, recommended disputes.

**Why it wins:** Otter/Cuboh aggregate *orders in* (saturated, ~$99/mo); nobody reconciles *money out* for small operators. Extends GROUNDING §5 bank-reconciliation logic. Read-only — zero trust barrier.

### Proposal B — Agent 5: Daily Sales Close & Tax Guard

**Pain:** #2 above. The Toast daily summary → QBO journal entry (cash vs card vs gift card, tips liability, comps, voids, service charges, tax collected) is the single workflow bookkeepers say owners always get wrong; QBO's bank reconciliation cannot catch a miscategorized sales journal, so errors compound silently for years.

**What it does:**
- Nightly: pulls Toast sales by business date, drafts a balanced daily sales journal entry for QBO (staged for approval per write rules — batch-approve a week in one email).
- Continuously: matches expected card batches (net of processing fees) to bank deposits; flags drawer/cash variances and no-ring risk patterns (void/comp spikes by employee/shift — extends Agent 3's theft coverage to the cash register).
- Monthly: reconciles POS tax collected vs QBO vs what the ST-1 filing should say (layered Chicago rates per GROUNDING §9), flags gaps before filing.

**Why it wins:** This is the missing twin of Agent 2 — Agent 2 automates money-out (AP), Agent 5 automates money-in (revenue close). Together they make the client's QBO actually correct, which every other agent's P&L math depends on. xtraCHEF's collapse left no affordable tool here.

### Proposal C — Agent 7: Labor Cost Guardian (prime-cost completion)

**Pain:** #3 above. Owners learn their labor % from the P&L weeks late. Profitable vs unprofitable restaurants differ by ~6 points of labor — the most consequential controllable number after food cost.

**What it does:**
- Pulls Toast Labor API (shifts, clock-ins, wages): daily labor $ and labor % of sales by daypart, in the morning briefing — completing **prime cost**, the metric the whole industry manages by.
- Flags: overtime trajectory by mid-week ("Maria hits OT Thursday at current pace"), missed clock-outs (documented payroll inflation pain), scheduled-vs-actual hours, labor % vs same-daypart seasonal baseline (GROUNDING §9 seasonality).
- Chicago-specific: encodes the tipped-wage phase-out schedule and July-1 minimum-wage steps so owners see next year's labor cost on this year's hours.

**Explicitly NOT a scheduler.** 7shifts/Homebase are free at our customers' size — that space is a commodity (competitor research, §4 below). We do the intelligence layer they don't: cost control against sales.

### Proposal D — Agent 8: Tip & Payroll Compliance Agent

**Pain:** #4 above. Tip-pool math is done by hand weekly and keyed into Gusto; FLSA tip-pool violations carry six-figure settlement risk; the FICA tip credit (often worth thousands/yr) goes unclaimed; Chicago's tip-credit phase-out makes every year's rules different.

**What it does:**
- Pulls tips and hours from Toast per shift; applies the house tip-pool policy (configured once at onboarding); outputs a payroll-ready Excel per pay period (Gusto/ADP import format). Owner approves, uploads — minutes instead of hours.
- Compliance checks each period: tipped employees actually reaching minimum wage after tips (per current Chicago schedule), managers/owners not in the pool (FLSA), 80/20 dual-job exposure, overtime on the correct regular rate (tipped OT is computed wrong constantly).
- Year-end: FICA tip credit (Form 8846) summary for the owner's CPA — found money.

**Why it wins:** Payroll processors compute paychecks; nobody computes the *tip pool* or audits compliance proactively at this price point. Pure read-and-report — no money moves. The Chicago wage-law angle makes this locally differentiated and annually sticky.

### Proposal E — Agent 9: Cash-Flow Forecaster (13-week)

**Pain:** #5 above. Strongest disinterested evidence of the whole study: 39% of operators unprofitable, 53% carrying debt, uneven cash flow the #1 Fed-surveyed challenge, and poor financial management implicated in 26% of failures. Owners discover a crunch the week rent is due.

**What it does:**
- We already integrate every input: Toast (revenue, with seasonal baseline per §9), QBO (AP due dates from Agent 2's bills, payroll history), bank (actual balance, deposit timing including delivery-platform lag from Agent 4).
- Produces a rolling 13-week cash projection: payroll Fridays, rent, sales-tax remittance dates, vendor terms, known seasonality (January–February trough called out explicitly).
- Output: one Excel tab + a plain-language line in the morning briefing — "Cash dips to ~$4,200 the week of Aug 18 (sales-tax payment + slow week). Consider shifting the Sysco payment."

**Why it wins:** Highest universality of any candidate; zero competitors at this size (cash-flow tools are generic-SMB and don't know restaurant rhythms); near-zero marginal data cost since it's pure synthesis of the existing spine. Read-only forever.

---

## 4. Runner-up

**Review & Reputation Agent** — universal pain, Harvard-validated revenue impact specific to independents, and owners describe the response workflow as a daily time sink. Held out of the top 5 because (a) the space has existing tools at $200–400/mo (a price gap, not a structural void, unlike A–E), and (b) it's the only candidate requiring entirely new data sources (Google Business Profile, Yelp, platform reviews) instead of the Toast/QBO/bank spine. Strong v2 candidate: weekly digest + AI-drafted responses for approval + review-bomb anomaly detection fits the 10-minute rule perfectly.

## 5. Validated but rejected

- **Hiring / no-show / turnover** (rank-2 pain by volume): a people-market problem, not a data problem; we hold none of the relevant data; ATS space is crowded.
- **Shift-coverage / call-out coordination** (most emotionally charged threads): real-time group-messaging product, conflicts with our async Excel/email architecture; 7shifts/Homebase free tiers own the adjacent ground.
- **Equipment maintenance** ($26B/yr industry cost): validated but the data lives in invoices we'd see only after the breakage; a preventive-maintenance calendar is a commodity checklist app.
- **Marketing attribution** ("can't tell what's driving traffic"): genuinely unsolved but requires ad-platform + foot-traffic data we don't have, and honest attribution at one-restaurant scale may be statistically impossible — high risk of selling noise.
- **Scheduling UI, delivery order aggregation, static recipe costing**: explicitly saturated at small-operator price points (7shifts/Homebase free; Otter/Cuboh ~$99/mo; Meez free tier). Avoid.

## 6. New data sources this adds to the platform

| Source | Access path | Used by |
|--------|------------|---------|
| Delivery platform payout reports (DD/UE/GH) | Owner forwards weekly CSV/statement export (10-min rule); portals have no useful public APIs | A (Agent 4), E |
| Toast Labor API | Same Toast credential, additional scope | C (Agent 7), D (Agent 8) |
| Toast voids/comps/no-sale + drawer events | Already in Orders/Cash data | B (Agent 5) |
| Payroll provider export/import formats (Gusto, ADP) | File-based; no API integration needed for MVP | D (Agent 8) |
| IL ST-1 filing parameters (rates, marketplace-facilitator rules) | Encoded + §10 staleness checklist | A, B |
| Chicago wage ordinance schedule | Encoded + §10 staleness checklist | C, D |
| Review platforms (Google, Yelp) | Only if runner-up is built | runner-up |

## 7. Roster impact if accepted (GROUNDING.md changes to make)

- Add Agents 4, 5, 7, 8, 9 to the §2 roster table; resolve the Agent 1 / Daily P&L numbering open issue at the same time.
- §5 (Bank Data): delivery-payout matching moves from a bullet into Agent 4's spec; Agent 5 owns card-batch deposit matching.
- §2 dependency spine: Agent 5 (sales close) joins Agents 1–2 as foundation — correct QBO revenue data is upstream of the P&L briefing and Agent 9.
- §10 staleness checklist: add delivery-platform fee/tax-remittance policies, DOL/FLSA tip rules, Gusto/ADP import formats.
