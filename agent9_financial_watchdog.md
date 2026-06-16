# Agent 9 — Financial Watchdog

## Role
Long-running agent that watches the financial health of the restaurant in real-time and alerts the owner before problems become crises. While Agent 8 records and organizes every financial transaction, Agent 9 reads that data and acts as the owner's financial early warning system. Tracks food cost, labor cost, cash position, and revenue trends continuously — and surfaces what matters in a single daily briefing so the owner always knows where they stand without opening a spreadsheet.

---

## Data Sources
| Source | What It Reads |
|---|---|
| Agent 8 — Bookkeeping & Accounting | All financial transactions, categorized expenses, revenue |
| POS (Toast / Square) | Real-time sales as they happen throughout the day |
| Agent 1 — Inventory Guardian | Food cost data per ingredient, theoretical vs actual |
| Agent 3 — Labor & Scheduling | Labor hours and cost per shift |
| Agent 5 — Vendor & Procurement | Upcoming bills and payment obligations |
| Bank feeds (Plaid) | Live cash balance across all accounts |
| Reservations (platform) | Future revenue visibility from bookings |

---

## Bank Transaction Reconciliation

Agent connects to all bank accounts and credit cards via Plaid and reconciles every transaction daily — the same way QuickBooks bank feeds work, but automatic.

### How It Works
1. Bank transactions import daily (or near real-time where supported)
2. Agent matches each bank transaction to a recorded entry in Agent 8
3. Matched transactions are marked reconciled automatically
4. Unmatched transactions are flagged for owner review
5. End-of-month balance confirmed against bank statement

### Daily Bank Feed View
```
BANK RECONCILIATION — June 14, 2026
Account: Chase Business Checking (...4821)

MATCHED TODAY (auto-reconciled)
  ✓ Sysco Chicago debit      -$1,840.00  matched to Bill #SYS-8821
  ✓ Square deposit           +$3,840.00  matched to POS sales Jun 13
  ✓ ADP payroll debit        -$8,420.00  matched to payroll entry Jun 13
  ✓ DoorDash payout          +$1,240.00  matched to delivery sales

NEEDS REVIEW
  ? Wire transfer            -$2,500.00  no matching bill or PO found
  ? Cash withdrawal            -$200.00  petty cash? needs label

RECONCILED BALANCE
  Book balance:   $43,400.00
  Bank balance:   $43,400.00
  Difference:     $0.00 ✓

[Review Unmatched Items]
```

### Month-End Reconciliation
At end of each month, agent produces a full reconciliation statement:
- Opening balance confirmed against prior month closing
- All transactions matched to bank statement line by line
- Any unresolved differences flagged with detail
- Reconciliation signed off by owner with one click
- Statement saved to records for CPA access

### Multiple Accounts
Agent reconciles all accounts simultaneously:
- Business checking (primary operating account)
- Business savings
- Business credit card(s)
- Petty cash (manual entry)
- Merchant settlement accounts (Square, Toast)

---

## Monitors Continuously
- Revenue vs same day / same week last year
- Food cost % as each sale closes on POS
- Labor cost % against live sales throughout the day
- Prime cost % (food + labor combined)
- Cash balance across all bank accounts
- Accounts payable due in next 7, 14, 30 days
- Any transaction anomalies in bank feed
- Revenue trends: daily, weekly, monthly trajectories

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Food cost spike | Food cost % exceeds target for 3 consecutive days | Alert owner with breakdown of which items driving it |
| Labor cost high | Labor tracking above target % mid-week | Alert — still time to adjust shifts |
| Cash low | Cash balance projected to drop below 2 weeks of operating expenses | Urgent cash flow warning |
| Revenue drop | Revenue down 20%+ vs same day last year | Alert with context (weather, event, competition) |
| Large unexpected charge | Bank transaction above threshold not matching a PO or known bill | Flag immediately — possible error or fraud |
| Duplicate payment | Same vendor paid twice in same period | Flag before second payment clears |
| Slow week incoming | Forecast shows next week 25%+ below typical | Proactive alert — time to run a promotion |
| Tax payment due | Sales tax, payroll tax, or estimated income tax due within 14 days | Reminder with amount owed |
| Prime cost threshold | Food + labor combined exceeding 65% | Priority alert — profitability at risk |

---

## Daily Morning Briefing

Every morning at 7am, agent compiles the financial section of the owner briefing:

```
FINANCIAL SNAPSHOT — Saturday June 14, 2026

YESTERDAY (Friday June 13)
  Revenue:          $4,480  ↑ +8.2% vs last Friday ($4,140)
  Food cost:        31.1%  ✓  (target 32%)
  Labor cost:       32.6%  ✓  (target 33%)
  Prime cost:       63.7%  ✓  (target 65%)
  Net income:       $916   (20.4%)

CASH POSITION
  Checking:         $28,400
  Savings:          $15,000
  Total cash:       $43,400
  Runway:           47 days ✓

BILLS DUE THIS WEEK
  Main Street Foods   $340    due Mon Jun 16
  ComEd               $312    due Wed Jun 18
  Total due:          $652    (well within cash position)

THIS WEEK SO FAR
  Revenue (Mon–Fri):  $19,840
  vs last week:       ↑ +6.1%
  On track for:       ~$28,000 week total

NO ALERTS TODAY ✓
```

---

## Cash Flow Forecast

Agent maintains a rolling 30/60/90 day cash flow projection updated daily:

```
CASH FLOW FORECAST — as of June 14, 2026
Current cash: $43,400

NEXT 30 DAYS (Jun 14 – Jul 14)
  Expected revenue:       +$112,000  (based on reservations + historical)
  Expected COGS:           -$39,200
  Expected labor:          -$33,600
  Expected rent:            -$8,500
  Expected utilities:       -$2,100
  Expected vendor bills:   -$18,400
  Other operating:          -$4,200
  Net cash change:         +$6,000
  Projected cash Jul 14:   $49,400 ✓

NEXT 60 DAYS (through Aug 14)
  Projected cash:          $54,800 ✓

NEXT 90 DAYS (through Sep 14)
  Projected cash:          $58,200 ✓
  Note: August historically 12% above average — summer dining uptick

⚠ IF revenue drops 15% from forecast:
  30-day cash: $41,600 (still safe)
  Stress-test result: healthy buffer exists
```

---

## Anomaly Detection

Agent flags transactions and patterns that do not fit the normal operating picture:

```
ANOMALY ALERT — June 14, 2026

Unusual bank charge: $2,500 wire transfer to unknown payee
  Date: June 13, 11:42pm
  No matching PO or approved bill found
  Action required: Verify with owner immediately

---

Pattern alert: Tuesday revenue has dropped 3 consecutive weeks
  Jun 3:  $1,840  (was avg $2,400)
  Jun 10: $1,720
  Jun 17: $1,680 (projected)
  Possible cause: New competitor opened on Tuesday soft-launch?
  Recommendation: Review and consider targeted Tuesday promotion
```

---

## Food Cost Deep Dive

When food cost % is trending above target, agent breaks down exactly where it is coming from:

```
FOOD COST ALERT — June 9–13, 2026
Average food cost this week: 34.8% ⚠ (target 32%)

TOP CONTRIBUTORS TO OVERAGE:
  Salmon dishes:    38.2%  ← Atlantic salmon price up 22%
  Ribeye steak:     36.4%  ← portion size check needed
  Pasta primavera:  28.1%  ✓ no issue

Root cause: 80% of overage from salmon pricing spike
Action: Agent 4 has flagged repricing options
        Agent 6 has found alternative suppliers

Remaining overage: Ribeye portion — recommend spot check
```

---

## Weekly Financial Summary

Every Monday morning, a full week summary:

```
WEEKLY SUMMARY — Week of June 9–13, 2026

REVENUE
  Dine-in:          $18,240
  Delivery:          $3,840
  Catering:          $2,400
  Total:            $24,480  ↑ +5.2% vs prior week

KEY RATIOS
  Food cost:        32.1%  ✓
  Labor cost:       29.4%  ✓
  Prime cost:       61.5%  ✓
  Net margin:       21.8%  ✓

CASH MOVEMENT
  Starting cash:    $39,100
  Net cash in:      +$4,300
  Ending cash:      $43,400

BEST DAY:   Friday $4,480
WORST DAY:  Tuesday $1,720 ⚠ (3rd consecutive slow Tuesday)

ACTION ITEMS
  1. Address slow Tuesday trend — consider promotion
  2. Salmon repricing or supplier switch (Agent 4 + 6 have options)
  3. Ribeye portion check recommended
```

---

## Actions
- Compile and send daily financial section of morning briefing
- Alert owner immediately when any urgent threshold is breached
- Maintain rolling 30/60/90 day cash flow forecast
- Flag anomalous bank transactions for owner review
- Surface food cost breakdowns when overages occur
- Send tax payment reminders with amounts due
- Feed financial health data to owner dashboard in real-time

---

## Outputs

### Daily (7am briefing)
- Yesterday's revenue, food cost %, labor cost %, prime cost %, net income
- Cash position and runway
- Bills due this week
- Any active alerts

### Real-Time (immediate alerts)
- Cash dropping below safety threshold
- Anomalous bank transaction
- Prime cost breaching target
- Large unexpected expense

### Weekly (Monday morning)
- Full week performance summary
- Revenue vs prior week and prior year
- Key ratio trends
- Action items with links to relevant agent recommendations

### Monthly (1st of month)
- Month-over-month and year-over-year revenue comparison
- Full ratio trend analysis
- Cash flow forecast updated with actuals
- Tax obligations for the coming month

---

## Key Metrics Tracked
- **Food cost %** — daily, trailing 7-day, trailing 30-day
- **Labor cost %** — daily, weekly
- **Prime cost %** — food + labor combined (target: under 65%)
- **Net profit margin %** — daily and monthly
- **Cash runway** — days of operating expenses covered
- **Revenue trend** — vs prior week, prior month, prior year
- **AP aging** — bills overdue or coming due

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 1 — Inventory Guardian | Food cost data per ingredient |
| Agent 3 — Labor & Scheduling | Daily labor cost per shift |
| Agent 4 — Menu Performance | Dish-level food cost when investigating overages |
| Agent 8 — Bookkeeping & Accounting | All categorized transactions and financial records |
| Agent 11 — Tips Management | Tip payout impact on labor cost |

---

## Human Approval Required
- No financial writes — this agent reads and alerts only
- Owner decides all actions based on alerts and recommendations
