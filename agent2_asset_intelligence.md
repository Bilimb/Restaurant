# Agent 2 — Asset Intelligence

## Role
Long-running agent that tracks every physical asset the restaurant owns. Knows what everything is worth, when it needs maintenance, when warranties expire, and when it is cheaper to replace than repair. Keeps the balance sheet accurate without the owner ever touching a spreadsheet.

---

## Data Sources
| Source | What It Reads |
|---|---|
| Invoice Processing (Agent 10) | New equipment purchases auto-detected from invoices |
| Platform asset register | All assets manually onboarded at setup |
| Vendor invoices | Repair and maintenance costs per asset |
| Manufacturer data | Recommended maintenance schedules, useful life |
| Insurance policies (platform) | Current insured value per asset |
| Bookkeeping Agent (Agent 8) | Depreciation entries posted to balance sheet |

---

## Asset Categories Tracked

### Equipment
- Kitchen: ovens, ranges, fryers, grills, salamanders, steamers
- Refrigeration: walk-in coolers, walk-in freezers, reach-in fridges, ice machines
- Warewashing: dishwashers, glass washers
- Prep: slicers, mixers, food processors, blenders
- Service: POS terminals, receipt printers, payment devices
- HVAC: hood systems, exhaust fans, AC units

### Furniture & Fixtures
- Dining: tables, chairs, booths, bar stools
- Service: host stand, service stations, shelving
- Outdoor: patio furniture (if applicable)

### Smallwares
- Cookware: pots, pans, sheet trays, hotel pans
- Cutlery: knives, cutting boards
- Serviceware: plates, bowls, glasses, silverware
- Bar: cocktail equipment (tracked as group, not individual items)

### Other
- Signage
- Security cameras and systems
- Sound systems
- Leasehold improvements (tracked for depreciation)

---

## Monitors Continuously
- Depreciation accruing on every asset daily
- Warranty expiration dates approaching
- Maintenance schedule per asset per manufacturer guidelines
- Repair cost history accumulating per asset
- Total asset value on balance sheet
- Insurance coverage vs current replacement value

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Warranty expiring | Warranty ends within 60 days | Alert owner — last chance for warranty claims |
| Maintenance due | Scheduled service date approaching | Generate maintenance checklist, vendor contact info |
| Repair cost threshold | Cumulative repair costs reach 50% of replacement value | Repair vs replace analysis pushed to owner |
| Asset fully depreciated | Book value reaches zero | Alert — update insurance, consider replacement planning |
| New invoice detected | Agent 10 identifies equipment purchase | Auto-add to asset register, request confirmation |
| Insurance gap | Replacement value exceeds insured value by 15%+ | Alert owner to update policy |
| Equipment age | Asset reaches end of manufacturer useful life | Proactive replacement planning alert |

---

## Repair vs Replace Analysis

When repair costs accumulate to the threshold, agent generates a clear comparison:

```
REPAIR VS REPLACE — Walk-In Cooler Unit #1
Asset age:          7 years
Original cost:      $8,400
Book value (net):   $1,200
Total repairs:      $4,200 (50% of original cost)
Latest repair:      $680 — compressor service

Replacement cost:   $9,200 (current market)
Expected life:      10 more years
Monthly cost new:   $77/month

Recommendation: REPLACE
Continuing repairs likely to accelerate. New unit saves
estimated $340/year in energy costs. Warranty resets to 5 years.

[Approve Replace] [Continue Repairing] [Remind Me Later]
```

---

## Depreciation

Agent calculates and posts depreciation automatically to the bookkeeping module.

| Asset Type | Method | Useful Life |
|---|---|---|
| Kitchen equipment | Straight-line | 5–7 years |
| Refrigeration | Straight-line | 10 years |
| Furniture | Straight-line | 7 years |
| Smallwares | Straight-line | 3 years |
| Leasehold improvements | Straight-line | Lease term |
| POS / Technology | Straight-line | 3 years |

Depreciation entries post monthly to Agent 8 (Bookkeeping) and appear on the balance sheet automatically.

---

## Actions
- Auto-add new assets from invoices detected by Agent 10
- Post monthly depreciation entries to Agent 8 (Bookkeeping)
- Generate maintenance checklists and vendor contact details
- Push repair vs replace analysis to owner when threshold hit
- Alert owner on warranty expirations and insurance gaps
- Update asset register when repairs, disposals, or replacements occur

---

## Outputs

### Daily
- None unless a trigger fires

### As Needed (triggered)
- Warranty expiration alert with days remaining
- Maintenance due notification with checklist
- Repair vs replace analysis report
- New asset confirmation request (from invoice detection)
- Insurance gap alert

### Monthly
- Depreciation summary posted to balance sheet
- Total asset value by category
- Repair spend this month vs last month

### Annually
- Full asset register with current book values
- Insurance valuation summary (replacement cost vs book value)
- Upcoming maintenance schedule for next 12 months
- Assets reaching end of useful life in next 12 months

---

## Key Metrics Tracked
- **Total asset value** (gross and net of depreciation)
- **Monthly depreciation charge** (posted to P&L)
- **Repair spend per asset** (cumulative and monthly)
- **Assets under active warranty** (count)
- **Assets past useful life** (count — replacement risk)
- **Insurance coverage gap** ($ difference between replacement and insured value)

---

## Asset Register Format

Every asset stored with:
```
Asset ID:          EQ-001
Name:              Walk-In Cooler
Category:          Refrigeration
Vendor:            Arctic Air
Purchase date:     2019-03-14
Purchase price:    $8,400
Useful life:       10 years
Depreciation/mo:   $70
Current book value: $1,200
Warranty expires:  2022-03-14 (expired)
Last maintenance:  2025-11-08
Next maintenance:  2026-05-08 (overdue)
Total repairs:     $4,200
Notes:             Compressor serviced twice. Monitor closely.
```

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 8 — Bookkeeping & Accounting | Receives monthly depreciation entries for balance sheet |
| Agent 10 — Invoice Processing | Sends new equipment purchases for auto-registration |
| Agent 1 — Inventory Guardian | Storage capacity and temperature zone data |

---

## Human Approval Required
- New asset additions (auto-detected from invoices, confirmed by owner)
- Asset disposals or write-offs
- Repair vs replace decisions
- Insurance policy updates
