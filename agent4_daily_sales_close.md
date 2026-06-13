# Agent 4 — Daily Sales Close & Bookkeeping

## Overview

The Daily Sales Close agent is the revenue-side twin of the Invoice Processor (Agent 2). Each business day it reads the Toast end-of-day close, drafts a single balanced **daily sales journal entry** for QuickBooks Online, and reconciles the resulting clearing accounts against the bank as deposits land. Agent 2 keeps *money out* (bills/AP) correct; Agent 4 keeps *money in* (sales, tax, tips, payment settlement) correct. Together they are what turns a restaurant's QBO file from a guess into real books.

---

## Problem It Solves

Most independent restaurants are in one of two bad states:

1. **No real books.** The owner hands a bookkeeper a single monthly sales total typed off the POS. Bookkeepers describe this constantly: *"they add up the sales from the till and give us a number for the month... almost guaranteed the numbers are incorrect... the numbers mean jack shit for operations."* The P&L every other agent depends on is built on sand.
2. **Expensive books.** They pay $500–$2,000/month for a bookkeeper to do the daily close by hand, because it's the one restaurant task generic accounting help gets wrong.

The daily close is genuinely hard: one day's takings must split into net sales (often by category), sales tax collected (a *liability*, not income), tips collected (a *liability* owed to staff), comps and discounts (contra-revenue), gift cards sold and redeemed (liability), and an offsetting clearing account for **each** way money arrived — cash, each card processor batch, gift cards, and each third-party delivery platform. QuickBooks' built-in bank reconciliation **cannot catch a miscategorized sales journal** — there's nothing for it to check against — so errors compound silently for years until an audit or a bad tax bill surfaces them. One owner discovered they had been remitting sales tax twice and lost ~$4,676 before anyone noticed.

---

## How It Works

1. **Pull the Toast daily close**
   After business date close, fetch sales by category, comps, voids, discounts, service charges, gift cards sold/redeemed, tips collected (card + declared cash), tax collected, and totals by payment type (cash, card, gift, each third-party platform). Always query by **business date**, re-pull the prior day once in the morning to catch late-syncing offline orders (per GROUNDING §3).

2. **Draft the Daily Sales Journal Entry (DSJ)**
   Build one balanced journal entry — the standard restaurant bookkeeping method — rather than importing thousands of individual Toast transactions (which bloats and breaks QBO). Revenue and liabilities on one side; a **clearing account per payment type** on the other:
   - Net sales by category → income accounts
   - Sales tax collected → tax liability
   - Tips collected → tips-payable liability *(handed to Agent 5 for distribution)*
   - Comps / discounts → contra-revenue
   - Gift cards sold → gift-card liability; redeemed → drawn down
   - Cash / Card-processor / Gift-card / DoorDash / Uber Eats / Grubhub → respective **clearing accounts**

3. **Stage for approval, then post**
   Per GROUNDING §4 write rules: nothing posts without owner approval; a week of daily entries batch-approves in one email/sheet. `DocNumber` = business date for idempotency; never modify entries the agent didn't create. No money ever moves.

4. **Reconcile the clearing accounts against the bank** *(ties into the bank-data work, GROUNDING §5)*
   As deposits arrive, clear them against the matching clearing account:
   - Card batches land net of processing fees, 1–2 business days later → match and book the fee
   - Third-party payouts land weekly, net of commission → clear that platform's account against its payout statement
   - Cash deposits are manual and irregular → flag gaps gently, never accuse
   A clearing account that doesn't return to ~zero is the signal: a short deposit, fee creep, a chargeback, or a missing cash deposit.

5. **Monthly tax true-up**
   Reconcile POS tax collected vs. the posted journals vs. what the IL ST-1 filing should show, using the layered Chicago rates (GROUNDING §9) and accounting for delivery platforms that remit marketplace-facilitator tax themselves. Surface discrepancies **before** the owner files — this is the failure mode that cost the $4,676.

---

## Output

- **QBO-ready daily sales journal entries**, staged in a weekly approval packet (Excel/email)
- **Daily/weekly close report** — one line per payment type: expected vs. deposited, fees, variance in dollars
- **Open-items list** — clearing accounts not yet settled (deposits still in transit vs. genuinely missing)
- **Monthly tax reconciliation** — POS tax collected vs. booked vs. filing-ready ST-1 figure, with discrepancies flagged
- **Register anomaly flags** — void/comp/no-sale spikes by employee or shift (extends Agent 3's theft detection from the walk-in to the cash drawer)

---

## What It Catches

| Issue | How It Shows Up |
|-------|----------------|
| Sales tax double-paid or misremitted | Monthly true-up: POS tax collected ≠ what's being filed/remitted |
| Card processing fee creep | Card clearing account clears short by a growing margin |
| Short or missing deposits | Clearing account never returns to zero for a business date |
| Chargebacks | Negative adjustment against a previously cleared batch |
| Register theft / no-ring | Void, comp, or no-sale spikes correlated to an employee or shift |
| Books that don't match the POS | DSJ won't balance, or Toast totals ≠ QBO income |
| Toast misconfiguration | Payment types / tax groups / service charges that don't map cleanly surface on day one |

---

## Data Requirements

| Source | Data Needed | How It's Accessed |
|--------|-------------|-------------------|
| Toast POS | Daily sales by category, payment types, tax, tips, comps/voids/discounts, gift cards | Toast Orders + Payments API, by business date |
| QuickBooks Online | Chart of accounts (to map to); post the daily sales JournalEntry | QBO API — read COA, write `JournalEntry` (staged for approval) |
| Bank | Deposit feed for clearing-account reconciliation | Bank-data integration (GROUNDING §5) — owner CSV/OFX or aggregator |
| Owner / bookkeeper | One-time COA + payment-type mapping; ongoing batch approval | Onboarding mapping sheet; weekly approval email |

---

## Integration Points

- **Complements Invoice Processor (Agent 2):** Agent 2 writes Bills (money out, `Bill`); Agent 4 writes the sales journal (money in, `JournalEntry`). Both follow the same §4 write rules. Together they produce a complete, correct QBO file.
- **Depends on the Toast connection** (shared) and the **bank-data integration** (GROUNDING §5) for the deposit-matching half.
- **Feeds the Daily P&L Briefing (Agent 7):** correct net sales, tax, and tips are upstream of every P&L number.
- **Hands the tips-payable liability to Tip & Payroll Compliance (Agent 5)**, which computes the per-employee distribution that draws that liability down.
- **Absorbs the third-party delivery reconciliation** as clearing-account lines: each platform's payout clears against its account here. Deep per-order delivery fee auditing remains out of scope (see Limitations).

---

## Onboarding Check: Toast Configuration

The close is only as correct as the POS feeding it. Before trusting the first month, verify the Toast setup once: payment types are distinct and mapped, tax groups are correct for dine-in vs. to-go and for alcohol, service charges are configured as service charges (not tips — they're legally wages; this matters for Agent 5), and each third-party delivery integration posts to a distinct, identifiable channel. Most "delivery reconciliation" pain (surprise fees, mismatched payouts) is really a Toast-configuration problem and is resolved here, per client, at onboarding — which is why it's a check inside this agent rather than an agent of its own.

---

## Limitations

- **Garbage in, garbage out on Toast config.** A misconfigured POS produces a clean-looking but wrong close; the onboarding check above is mandatory, not optional.
- **Deep delivery-fee auditing is out of scope.** Agent 4 reconciles the *net payout* against its clearing account; it does not dispute individual platform refunds or line-item commissions order by order.
- **Does not run or replace the accountant.** It drafts journal entries and reconciliations for approval; it does not file taxes or move money.
- **Cash is only as good as it's deposited.** The agent can flag a likely missing cash deposit but cannot confirm cash that never entered the system.

---

## Setup Effort

| Task | Time |
|------|------|
| Map Toast payment types & sales categories → QBO chart of accounts (one-time) | 1–2 hours |
| Verify Toast configuration (onboarding check above) | 30–60 minutes |
| Connect Toast, QBO, and bank | 45 minutes |
| First useful close report | Day 1 after setup |

---

## Estimated Impact

Outsourced restaurant bookkeeping runs **$500–$2,000/month**; the daily sales close is the most labor-intensive, most error-prone piece of it. Automating it either removes that spend or replaces hours of weekly owner time — and the reconciliation catches real dollars: processing-fee creep, short deposits, chargebacks, and tax-remittance errors (one documented case lost ~$4,676 to double-paid sales tax alone). Beyond the direct savings, this agent is what makes the rest of the platform trustworthy: every P&L, variance, and forecast downstream is only as good as the revenue data Agent 4 keeps correct.
