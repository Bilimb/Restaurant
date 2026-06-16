# Agent 1 — Inventory Guardian

## Role
Long-running agent that tracks every ingredient and supply item in real-time. Knows what is in stock, what is burning through sales, what needs to be ordered, and where waste or theft may be occurring. Never lets the kitchen run out or overpay for supplies.

---

## Data Sources
| Source | What It Reads |
|---|---|
| POS (Toast / Square) | Every sale → which menu items sold → which ingredients consumed |
| Recipes (platform) | Maps each menu item to its ingredients and quantities |
| Vendor invoices (Agent 10) | Deliveries received, quantities, prices paid |
| Purchase orders (platform) | What was ordered vs what arrived |
| Manual stock counts | Staff inputs via mobile app or tablet |
| Asset register (Agent 2) | Equipment that affects inventory (storage capacity, temperature zones) |
| Reservations (platform) | Upcoming covers to project future burn |

---

## Monitors Continuously
- Current stock level per ingredient
- Burn rate per ingredient based on live POS sales
- Theoretical usage vs actual usage (variance = waste or theft signal)
- Vendor delivery schedule and expected arrivals
- Ingredient price per invoice over time
- Upcoming reservation load and projected ingredient demand
- Expiration dates on perishable stock

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Low stock | Ingredient will hit zero before next scheduled delivery | Draft purchase order, alert owner |
| Price spike | Vendor charges 10%+ more than last invoice | Alert owner, signal Agent 6 (Vendor Intel) to find alternatives |
| Variance alert | Actual usage exceeds theoretical by 8%+ over 3 days | Flag for waste or theft investigation |
| Expiring stock | Ingredient expires within 48 hours | Push to Agent 4 (Menu Performance) for daily special suggestion |
| Delivery mismatch | Delivered quantity differs from PO quantity | Flag discrepancy before invoice is approved |
| Dead stock | Ingredient unused for 14+ days | Flag for menu review or write-off |
| Reservation spike | Upcoming reservations 30%+ above typical | Proactive reorder suggestion with projected quantities |

---

## Ordering Workflow

In most small restaurants the person doing ordering is a kitchen manager or head cook — not the owner. They may order online to large vendors (Sysco, US Foods, Restaurant Depot) or physically visit a store (Costco, local market). The agent supports both workflows.

### How It Works
1. Agent continuously monitors stock levels and burn rates
2. Before the usual ordering day (configurable per restaurant), agent generates a **draft order list** organized by vendor
3. The kitchen staff member opens the list on their phone or tablet, reviews it, edits quantities, adds items, removes what they don't need
4. For **online vendors** — edited list becomes a formatted order ready to submit through vendor portal
5. For **store visit purchases** — edited list becomes a printable / mobile shopping list they take to the store
6. After purchase, staff logs what was actually bought (quantities, prices) — agent updates inventory and flags any differences from the draft

### Order List Format
```
ORDER LIST — Thursday June 18
Prepared by: Inventory Guardian
Last edited by: [staff name]

SYSCO (online order)
  Chicken thighs         25 lbs    [edit]
  Ground beef            15 lbs    [edit]
  Heavy cream             6 qt     [edit]

COSTCO (store visit — shopping list)
  Olive oil               4 x 3L   [edit]
  Canned tomatoes        12 cans   [edit]
  Paper towels            2 cases  [edit]

ADD ITEM: _______________
```

All items editable before the order is placed. Agent learns from edits over time to improve future draft accuracy.

---

## Quick Inventory Count Tool

Physical inventory counting is one of the most painful tasks in a restaurant — staff walking around with clipboards, then manually entering numbers. This agent makes it fast.

### How It Works
1. Agent generates a **count sheet** pre-populated with every ingredient, organized by storage location (walk-in cooler, dry storage, freezer, bar back)
2. Staff opens the count sheet on a phone or tablet, walks through each area, enters quantities as they count
3. Agent immediately compares entered counts against expected quantities
4. Flags any large discrepancies in real-time as staff are still counting (so they can recount suspicious items on the spot)
5. Count is saved, inventory position updated, variance report generated instantly

### Count Sheet Format
```
INVENTORY COUNT — Sunday June 15, 9:00am
Started by: [staff name]

WALK-IN COOLER
  Chicken thighs         expected: 18 lbs   counted: [___]
  Ground beef            expected: 8 lbs    counted: [___]
  Salmon fillets         expected: 6 lbs    counted: [___]
  Heavy cream            expected: 4 qt     counted: [___]

DRY STORAGE
  All-purpose flour      expected: 20 lbs   counted: [___]
  Canned tomatoes        expected: 8 cans   counted: [___]
  Olive oil              expected: 2 x 3L   counted: [___]

FREEZER
  Beef stock             expected: 10 qt    counted: [___]
  ...

⚠ FLAG: Salmon fillets — entered 2 lbs, expected 6 lbs. Recount?
```

### Count Frequency
- Full count: configurable (weekly recommended)
- Spot count: agent can generate a partial list for high-value or high-variance items only
- Pre-delivery count: auto-generated before a large delivery arrives so receiving is accurate

---

## Actions

All purchase orders require owner approval before submission to vendor.

- Generate and maintain draft order lists organized by vendor
- Generate quick count sheets organized by storage location
- Send low stock alerts to owner dashboard and mobile
- Flag variance reports for manager review
- Push expiring ingredient data to Agent 4 (Menu Performance)
- Notify Agent 6 (Vendor Intel) when price spike detected
- Update core data layer after every delivery received and every count completed

### Daily
- Morning inventory position: current stock levels, days remaining per key ingredient
- Burn rate summary: top 10 ingredients by consumption yesterday
- Variance report: theoretical vs actual usage

### As Needed
- Draft purchase orders ready for one-click approval
- Low stock alerts (pushed in real-time, not held for morning brief)
- Delivery discrepancy reports
- Expiring ingredient alerts with suggested specials

### Weekly
- Waste analysis: what was thrown away, cost of waste
- Ingredient cost trend: price changes per vendor over trailing 4 weeks

### Monthly
- Food cost accuracy report: how close theoretical food cost matched actual
- Top waste contributors: ingredients with highest loss rates
- Reorder pattern summary: are order quantities optimized

---

## Key Metrics Tracked
- **Stock days remaining** per ingredient
- **Theoretical vs actual usage variance** (target: under 5%)
- **Food cost %** contribution from inventory (feeds Agent 9)
- **Waste cost $** per week
- **Order accuracy rate** per vendor (feeds Agent 5)
- **Stockout incidents** per month (target: zero)

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 4 — Menu Performance | Sends expiring ingredient data to suggest daily specials |
| Agent 5 — Vendor & Procurement | Passes low stock triggers to generate purchase orders |
| Agent 6 — Vendor Intel | Signals price spikes to trigger alternative vendor search |
| Agent 9 — Financial Watchdog | Feeds real-time food cost % data |
| Agent 10 — Invoice Processing | Receives confirmed delivery quantities after invoice match |

---

## Human Approval Required
- All purchase orders before submission
- Variance investigations (agent flags, manager investigates)
- Ingredient write-offs above threshold
