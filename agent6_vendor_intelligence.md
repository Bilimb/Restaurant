# Agent 6 — Vendor Intelligence & Alternative Sourcing

## Role
Long-running agent that watches prices across all purchasing channels — formal vendors, retail stores, and local markets — and proactively finds better deals when costs rise. Never waits to be asked. When an ingredient price spikes, it already has alternatives ready. Treats Sysco and Costco and the local farmers market as equally valid sourcing options.

---

## Data Sources
| Source | What It Reads |
|---|---|
| Agent 5 — Vendor & Procurement | Invoice prices per ingredient per vendor, delivery history |
| Agent 1 — Inventory Guardian | Ingredient price spike signals |
| Agent 4 — Menu Performance | Which ingredients are most critical to high-margin dishes |
| Platform vendor register | All active vendors and retail store options |
| Web search | Current market prices, new local suppliers, commodity indices |
| Receipt logs (platform) | Store visit purchase prices (Costco, Restaurant Depot, etc.) |

---

## Purchasing Channel Types

The agent treats all purchasing channels equally — not just formal vendors:

| Channel Type | Examples | How Agent Handles |
|---|---|---|
| Broadline distributor | Sysco, US Foods, Gordon Food Service | Tracks invoice prices, formal PO process |
| Cash & carry / wholesale | Restaurant Depot, Costco, Sam's Club | Tracks receipt prices from store visits |
| Local grocery | Jewel-Osco, Mariano's (Chicago) | Tracks receipt prices, emergency sourcing option |
| Specialty suppliers | Local farms, ethnic markets, specialty importers | Tracks invoice or receipt prices |
| Farmers markets | Chicago farmers markets, local farms | Seasonal sourcing, quality and price tracked |
| Online wholesale | Amazon Business, WebstaurantStore | Tracks order confirmations and prices |

---

## Monitors Continuously
- Price per unit for every ingredient across all channels used
- Price history and trend per ingredient per channel
- Commodity index movements (beef, chicken, seafood, produce, dairy)
- Seasonal price patterns from prior years
- New suppliers and distributors in the Chicago area
- Performance scores of current vendors (from Agent 5)
- Underperforming vendors as sourcing risks

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Price spike | Ingredient cost up 10%+ vs 30-day average | Immediately search all channels for alternatives |
| Vendor reliability drop | Vendor on-time rate below 85% (from Agent 5) | Find backup source for that vendor's key items |
| New alternative found | Better price identified for a current ingredient | Present comparison to owner |
| Seasonal spike predicted | Historical data shows price rising this time of year | Proactive alert before spike hits |
| Commodity market move | Beef, chicken, or seafood index moves significantly | Flag affected dishes to Agent 4 |
| Dead vendor | Agent 5 flags vendor for removal | Find replacement source before vendor is dropped |

---

## Alternative Sourcing Workflow

When a price spike is detected:

```
PRICE SPIKE ALERT — Atlantic Salmon
Current vendor:     Sysco Chicago
Last price:         $9.20/lb
New price:          $11.40/lb (+23.9%)
Affected dishes:    Grilled Salmon, Salmon Pasta (Agent 4 flagged)

ALTERNATIVES FOUND

Option 1: Gordon Food Service
  Price:            $10.10/lb (-11.4% vs Sysco new price)
  Min order:        15 lbs
  Delivery:         Tuesday/Friday
  Note:             Already have account — can order immediately

Option 2: Chicago Fish House (local supplier)
  Price:            $9.80/lb (-14.0% vs Sysco new price)
  Min order:        10 lbs
  Delivery:         Monday/Wednesday/Friday
  Note:             New vendor — needs onboarding (est. 1 day)

Option 3: Restaurant Depot (store visit)
  Price:            $10.40/lb (-8.8% vs Sysco new price)
  Available:        In stock (verified today)
  Note:             No minimum, immediate — good for this week
        while switching vendors

Recommendation: Switch to Chicago Fish House long-term.
Use Restaurant Depot this week as bridge.

[Approve Switch] [Get More Info] [Stay with Sysco]
```

---

## Price Benchmarking

Agent continuously benchmarks what the restaurant pays against all available channels:

```
PRICE BENCHMARK REPORT — June 2026

Chicken thighs
  Currently paying:   $3.40/lb (Sysco)
  Restaurant Depot:   $2.95/lb (-13.2%) ← better
  Gordon Food:        $3.55/lb (+4.4%)
  Recommendation:     Switch to Restaurant Depot for chicken

Romaine hearts
  Currently paying:   $18.00/case (Sysco)
  Local farm (CSA):   $14.00/case (-22.2%) ← better + fresher
  Jewel-Osco:         $22.00/case (+22.2%)
  Recommendation:     Explore local farm relationship

Olive oil (bulk)
  Currently paying:   $28.00/3L (Sysco)
  Costco:             $21.99/3L (-21.5%) ← significantly better
  Recommendation:     Buy at Costco on weekly run
```

---

## Seasonal Buying Intelligence

Agent tracks multi-year seasonal price data and alerts before spikes arrive:

```
SEASONAL FORECAST — Upcoming 60 Days

Avocados (July–August):
  Historically rise 35–50% in peak summer
  Current price: $1.20 each
  Projected peak: $1.80–$2.40 each
  Suggestion: Consider buying ahead in bulk or
              adjusting guacamole portion temporarily

Tomatoes (June–August):
  Peak season — prices historically drop 25–40%
  Current price: $18/case
  Projected: $11–$14/case by mid-July
  Suggestion: Feature tomato-forward dishes on summer menu

Dungeness Crab (November–December):
  Peak season approaches — price drops significantly
  Suggestion: Plan special menu promotion for November
```

---

## New Vendor Discovery

Agent actively searches for new suppliers the restaurant is not currently using:

- Searches for local Chicago-area food distributors, farms, and specialty suppliers
- Monitors Chicago farmers market vendor lists (Green City Market, Logan Square)
- Checks if any current vendors have expanded their product range
- Flags new ethnic specialty importers for restaurants with diverse menus
- Presents new vendor options with contact info and estimated pricing

---

## Actions
- Monitor all ingredient prices across all purchasing channels continuously
- Search for alternatives immediately when a price spike is detected
- Present ranked alternative options with clear comparison to owner
- Signal Agent 4 when commodity moves will affect dish margins
- Signal Agent 5 when a new vendor should be onboarded
- Build and maintain price history database across all channels
- Generate seasonal buying forecasts proactively

---

## Outputs

### As Needed (triggered)
- Price spike alert with ranked alternatives ready
- Vendor reliability alert with backup sourcing options
- New local vendor discovery notification

### Weekly
- Price benchmark report: what you pay vs all available channels
- Top 5 savings opportunities identified this week

### Monthly
- Full sourcing optimization report
- Seasonal forecast for next 60 days
- Vendor performance vs price summary (are you paying more for reliability?)
- Estimated savings if recommendations are acted on

---

## Key Metrics Tracked
- **Price variance** per ingredient vs 30-day average
- **Savings identified** vs savings acted on (did owner take recommendations?)
- **Channel diversity** — how many sourcing channels actively used
- **Benchmark gap** — % difference between current prices and best available
- **Seasonal forecast accuracy** — how close predictions were to actual prices

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 1 — Inventory Guardian | Receives price spike signals per ingredient |
| Agent 4 — Menu Performance | Signals commodity moves that affect dish margins |
| Agent 5 — Vendor & Procurement | Receives vendor performance data, sends new vendor recommendations |
| Agent 8 — Bookkeeping | Purchase prices across all channels for cost tracking |

---

## Human Approval Required
- Switching primary vendor for any ingredient
- Onboarding a new vendor
- Acting on any sourcing recommendation (agent finds, human decides)
