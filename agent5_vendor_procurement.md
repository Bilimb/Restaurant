# Agent 5 — Vendor & Procurement

## Role
Long-running agent that manages the full purchasing cycle from order to delivery. Prepares purchase orders, tracks what was ordered vs what arrived, scores vendor performance over time, and makes sure the restaurant never pays for something it did not receive. Works hand in hand with Agent 1 (Inventory Guardian) which triggers the need to order, and Agent 10 (Invoice Processing) which handles the bill after delivery.

---

## Data Sources
| Source | What It Reads |
|---|---|
| Agent 1 — Inventory Guardian | Low stock triggers, draft order quantities, burn rate projections |
| Agent 10 — Invoice Processing | Invoices received, prices charged, quantities billed |
| Platform vendor register | All vendors, contacts, products, pricing history, delivery schedules |
| POS (Toast / Square) | Sales data to validate order sizing |
| Receiving log (platform) | What staff confirmed as received at the door |

---

## Vendor Register

Every vendor stored with full profile:

```
Vendor:            Sysco Chicago
Category:          Broadline distributor
Rep name:          Mike Torres
Rep phone:         312-555-0142
Rep email:         m.torres@sysco.com
Order method:      Online portal (sysco.com/order)
Order cutoff:      Tuesday 5pm for Thursday delivery
Delivery day:      Thursday 7am–10am
Min order:         $300
Payment terms:     Net 30
Account #:         SC-88421
Notes:             Call rep directly for rush orders
Performance:       94% on-time, 98% order accuracy (last 90 days)
```

---

## Monitors Continuously
- Open purchase orders and expected delivery dates
- Deliveries due today and tomorrow
- Receiving confirmations from staff
- Invoice vs PO discrepancies (handled with Agent 10)
- Vendor performance metrics accumulating over time
- Order cutoff deadlines approaching for each vendor

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Low stock signal | Agent 1 flags ingredient running low | Generate draft PO for that vendor, alert owner |
| Order cutoff approaching | Vendor order deadline within 4 hours | Reminder if no order placed yet |
| Delivery not confirmed | Expected delivery window passed, no receiving log | Alert manager to follow up with vendor |
| Short delivery | Received quantity less than ordered | Log discrepancy, flag before invoice approved |
| Wrong item delivered | Item substituted without notice | Flag for return or credit request |
| Vendor performance drop | On-time rate drops below 85% over 30 days | Performance alert, suggest review |
| New vendor needed | Agent 6 identifies better pricing | Onboard new vendor to register |

---

## Purchase Order Workflow

```
STEP 1 — Agent 1 signals low stock or upcoming demand
         ↓
STEP 2 — Agent 5 generates draft PO organized by vendor
         (quantities based on burn rate + days until next delivery)
         ↓
STEP 3 — Kitchen staff or manager reviews draft on mobile,
         edits quantities, adds items
         ↓
STEP 4 — Owner or manager approves PO
         ↓
STEP 5 — For online vendors: formatted order ready to submit
         For store purchases: printable shopping list generated
         ↓
STEP 6 — Order placed (by staff, agent does not auto-submit)
         ↓
STEP 7 — Delivery day: agent reminds staff to confirm receipt
         ↓
STEP 8 — Staff logs what was received (quantities, any issues)
         ↓
STEP 9 — Agent compares received vs ordered, flags discrepancies
         ↓
STEP 10 — Agent 10 processes the invoice and matches to this PO
```

---

## Receiving Process

Agent makes receiving fast and accurate. When a delivery is expected, staff opens the receiving screen:

```
RECEIVING — Sysco Delivery, Thursday June 19
Expected arrival: 7am–10am | Driver: TBD

CONFIRM RECEIPT

Chicken thighs         ordered: 25 lbs   received: [___] lbs
Ground beef            ordered: 15 lbs   received: [___] lbs
Heavy cream            ordered: 6 qt     received: [___] qt
Romaine hearts         ordered: 4 cases  received: [___] cases
Parmesan (grated)      ordered: 5 lbs    received: [___] lbs

ISSUES
  [ ] Item damaged
  [ ] Item substituted
  [ ] Temperature concern
  [ ] Other: _______________

[Submit Receiving Log]
```

After submission, agent immediately updates inventory in Agent 1 and sends discrepancy report if anything is off.

---

## Vendor Performance Scorecard

Agent tracks every vendor on four dimensions, updated after every delivery:

```
VENDOR SCORECARD — Sysco Chicago (last 90 days)
Deliveries:        24
On-time:           22/24 (91.7%) ✓
Order accuracy:    23/24 (95.8%) ✓
Price consistency: 3 price changes flagged
Short deliveries:  1 incident (chicken thighs, resolved with credit)
Overall score:     A-

MAIN STREET FOODS (last 90 days)
Deliveries:        12
On-time:           9/12 (75.0%) ⚠
Order accuracy:    11/12 (91.7%) ✓
Price consistency: 1 price spike flagged
Short deliveries:  2 incidents (unresolved)
Overall score:     C+  → Review recommended
```

---

## Actions
- Generate draft purchase orders from Agent 1 signals
- Send order deadline reminders before vendor cutoff times
- Generate receiving checklists for each expected delivery
- Log receiving confirmations and update Agent 1 inventory
- Flag delivery discrepancies and route to Agent 10 for invoice matching
- Maintain and update vendor performance scores
- Onboard new vendors recommended by Agent 6
- Request vendor credits for short or damaged deliveries

---

## Outputs

### Daily
- Deliveries expected today with receiving checklist
- Any overdue deliveries not yet confirmed
- Open POs pending delivery

### Weekly
- Purchase orders placed vs received summary
- Spend by vendor for the week
- Any unresolved delivery discrepancies

### Monthly
- Vendor performance scorecards for all active vendors
- Total procurement spend by category (produce, protein, dairy, dry goods)
- Order accuracy rate across all vendors
- Credits received from vendors for discrepancies

---

## Key Metrics Tracked
- **On-time delivery rate** per vendor (target: 90%+)
- **Order accuracy rate** per vendor (target: 98%+)
- **Discrepancy resolution rate** — credits received vs discrepancies flagged
- **Procurement spend by category** monthly
- **PO to delivery cycle time** per vendor
- **Stockout incidents caused by vendor failure** (separate from ordering errors)

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 1 — Inventory Guardian | Receives low stock triggers, sends confirmed received quantities |
| Agent 6 — Vendor Intelligence | Receives alternative vendor recommendations and price benchmarks |
| Agent 8 — Bookkeeping | Sends procurement spend for expense categorization |
| Agent 10 — Invoice Processing | Shares PO data for three-way match (PO → received → invoice) |

---

## Human Approval Required
- All purchase orders before placement
- Vendor credit requests above threshold
- New vendor onboarding
- Decision to stop using an underperforming vendor
