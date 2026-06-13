# Agent 5 — Tip & Payroll Compliance

## Overview

The Tip & Payroll Compliance agent automates the single most universally manual task in restaurant accounting: calculating tip distribution every pay period and getting it into payroll correctly and legally. Each pay period it reads tips and hours per shift from Toast, applies the restaurant's tip-pool policy, and produces a payroll-ready file for the owner to approve and import. Alongside the math, it audits the result for the wage-and-hour rules that carry six-figure penalties — and surfaces the FICA tip credit most small operators never claim.

---

## Problem It Solves

Tips are calculated by hand at nearly every independent restaurant. Someone exports Toast, builds a spreadsheet, works out each server's tip-out to the bar, bussers, and kitchen, then keys the totals into Gusto or ADP — every two weeks, forever. *"I need to manually track all of the tips and enter them into Gusto every 2 weeks. Again not terrible, but it's work."* It's hours of fiddly, repetitive accounting time per period.

It's also a legal minefield the owner usually doesn't know they're standing in:

- The **DOL recovered $274M in restaurant back wages in 2024**; tip-pool violations are a leading category, with settlements *routinely exceeding $100,000* (one local case clawed back $85K).
- A pool that includes a manager, owner, or supervisor is **illegal under the FLSA**; back-of-house may only share a pool if no tip credit is taken.
- **Overtime** must be paid on a regular rate that correctly includes service charges (which are wages, not tips) — restaurants get this wrong constantly.
- The **80/20 dual-jobs rule** limits how much non-tipped work a tipped employee can do at the tipped wage.
- Chicago is **phasing out the tip credit** on a known annual schedule (GROUNDING §9), so the compliance math changes every year — and the owner has to make up any shortfall when tips don't bring an employee to full minimum wage.

And the **FICA tip credit** (IRS Form 8846) — often worth thousands of dollars a year — goes unclaimed because nobody assembles the numbers for it.

---

## How It Works

1. **Pull tips and hours per shift from Toast**
   For each pay period, fetch credit-card tips, declared cash tips, hours worked, and role per employee per shift (Toast Labor + Payments). Service charges are read separately and treated as **wages, not tips** (a critical legal distinction carried over from Agent 4's configuration check).

2. **Apply the house tip-pool policy**
   Using the policy configured once at onboarding — pooled vs. retained, tip-out percentages by role, and distribution basis (by hours worked, by net sales, or by points) — compute each employee's tip allocation for the period.

3. **Run compliance checks** *(report, don't block — the owner decides)*
   - **Tip-credit floor:** every tipped employee reaches full minimum wage after tips, per the *current* Chicago schedule; flag shortfalls the owner must top up.
   - **Pool legality:** no managers/owners/supervisors in the pool; BOH only if no tip credit is taken.
   - **Overtime rate:** correct regular rate including service charges.
   - **80/20 exposure:** tipped employees over the non-tipped-work threshold.
   - **Cash-tip sanity:** declared cash tips implausibly low vs. the card-tip ratio (flag for the owner, not an accusation).

4. **Produce the payroll-ready file**
   Output the per-employee tip allocation (and any flagged top-ups) in the import format the owner's payroll processor accepts (Gusto / ADP / Paychex). The owner reviews, approves, and imports. Minutes instead of hours; **no money moves through the agent** — it produces the file the owner's payroll system runs.

5. **Year-end FICA tip credit summary**
   Assemble the figures the owner's CPA needs for Form 8846 — recovering money that's otherwise left on the table.

---

## Output

- **Payroll-ready tip allocation file** per pay period, in the processor's import format
- **Tip-out breakdown** — per employee: card tips, cash tips, pool in/out, net allocation
- **Compliance report** — flagged shortfalls, illegal-pool risks, OT-rate issues, 80/20 exposure, cash-tip anomalies, each with the dollar/legal impact in plain language
- **Annual FICA tip-credit (Form 8846) summary** for the CPA

---

## What It Catches

| Issue | How It Shows Up |
|-------|----------------|
| Tip-out math errors | Allocations that don't reconcile to tips collected |
| Sub-minimum wage after tips | A shift where wage + tips < current Chicago minimum → owner must top up |
| Illegal tip pool | Manager/owner/supervisor receiving pool dollars |
| Wrong overtime rate | OT paid without service charges in the regular rate |
| 80/20 dual-job exposure | Tipped employee over the non-tipped-work threshold |
| Service-charge-as-tip misclassification | Service charges routed as tips instead of wages |
| Unclaimed FICA tip credit | Year-end credit never filed |

---

## Data Requirements

| Source | Data Needed | How It's Accessed |
|--------|-------------|-------------------|
| Toast POS | Card tips, declared cash tips, hours, role per shift; service charges | Toast Labor + Payments API |
| Owner | Tip-pool policy (one-time); pay-period approval | Onboarding config; per-period approval |
| Payroll processor | Import file format | Gusto / ADP / Paychex import template |
| Chicago wage law | Current minimum wage + tip-credit phase-out schedule | Encoded (GROUNDING §9, kept current per §10) |

---

## Integration Points

- **Draws down the tips-payable liability posted by the Daily Sales Close (Agent 4)** — Agent 4 records what was collected; Agent 5 computes who gets it; the distribution reconciles against that liability and the bank (the cash side of the bank-data work, GROUNDING §5).
- **Depends on the Toast connection** with Labor scope (shared credential).
- **Feeds the payroll processor** (export) and the **Daily P&L Briefing (Agent 7)** with accurate labor/tip cost.
- Relies on Agent 4's **service-charge-vs-tip configuration** being correct at the POS.

---

## Limitations

- **Does not run payroll.** It produces the import file; the owner's processor runs the actual payroll. By design, no money moves through the agent.
- **Flags illegality; doesn't fix the policy.** If the configured tip pool is itself unlawful, the agent surfaces the risk in plain language — it is not legal advice, and the owner must change the policy.
- **Cash-tip accuracy depends on honest declaration.** The agent can flag implausible declared cash tips but cannot verify cash it never sees.
- **Chicago-specific schedule must be kept current** (GROUNDING §10 staleness checklist); the phase-out changes the math annually.

---

## Setup Effort

| Task | Time |
|------|------|
| Configure tip-pool policy (roles, percentages, distribution basis) | ~1 hour |
| Connect Toast (Labor scope) and map roles | 30 minutes |
| Map payroll-processor import format | 30 minutes |
| First tip run | Next pay period |

---

## Estimated Impact

Tip calculation runs roughly **1–3 hours every pay period** — call it **26–78 hours a year** of owner or bookkeeper time, on the most tedious recurring task in the back office. Automating it removes that entirely. The compliance layer avoids real money: DOL recovered **$274M** in restaurant back wages in 2024, with individual tip-pool cases settling north of **$100K**. And the FICA tip credit the agent assembles for the CPA can be worth **thousands per year** — often enough on its own to pay for the platform.
