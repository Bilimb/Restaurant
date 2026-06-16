# Agent 4 — Menu Performance

## Role
Long-running agent that knows exactly which dishes make money and which ones do not. Tracks every item on the menu by profitability and popularity, flags when food cost crosses thresholds, suggests pricing changes, and turns expiring ingredients into daily specials before they become waste. Gives the owner and chef a clear picture of what to promote, what to reprice, and what to cut.

---

## Data Sources
| Source | What It Reads |
|---|---|
| POS (Toast / Square) | Sales count and revenue per menu item, per day and shift |
| Recipes (platform) | Ingredient quantities and prep steps per dish |
| Agent 1 — Inventory Guardian | Current ingredient costs, price changes, expiring stock |
| Agent 8 — Bookkeeping | Actual food cost from invoices for cost validation |
| Reservations (platform) | Upcoming covers to project expected sales mix |
| Digital Menu (platform) | Current published menu, prices, active items |

---

## Monitors Continuously
- Sales volume per menu item (daily, weekly, rolling 30-day)
- Food cost % per dish (recalculated every time an ingredient price changes)
- Contribution margin per dish (selling price minus food cost)
- Menu mix: which items are being ordered together
- Items not sold within a defined period
- Incoming expiring ingredient signals from Agent 1

---

## Menu Engineering Matrix

Every dish is plotted on a 2x2 matrix by profitability and popularity. Agent updates this automatically as sales and costs change.

```
                    LOW POPULARITY    HIGH POPULARITY
                  ┌─────────────────┬─────────────────┐
   HIGH PROFIT    │    PUZZLES      │     STARS       │
                  │  High margin,   │  High margin,   │
                  │  few orders     │  many orders    │
                  │  → Reposition   │  → Protect &    │
                  │    or promote   │    promote      │
                  ├─────────────────┼─────────────────┤
   LOW PROFIT     │     DOGS        │  PLOWHORSES     │
                  │  Low margin,    │  Low margin,    │
                  │  few orders     │  many orders    │
                  │  → Cut or       │  → Reprice or   │
                  │    rework       │    reformulate  │
                  └─────────────────┴─────────────────┘
```

Agent categorizes every dish and updates classification weekly. Owner sees which dishes have moved categories and why.

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Food cost spike | Dish food cost % crosses 35% | Alert owner with repricing suggestion |
| Ingredient price change | Key ingredient price changes | Recalculate food cost % for all affected dishes immediately |
| Dead item | Dish not ordered in 14 days | Flag for removal review |
| Expiring ingredient | Signal from Agent 1 | Generate daily special suggestion using that ingredient |
| Star dish at risk | Top-selling dish food cost rising toward threshold | Priority alert — this dish drives significant revenue |
| New item underperforming | New dish in bottom 20% after 30 days | Review or reposition alert |
| Menu category imbalance | One category dominates orders, another ignored | Menu structure suggestion |

---

## Daily Specials Engine

When Agent 1 signals an expiring ingredient, Agent 4 generates a special that uses it profitably.

```
DAILY SPECIAL SUGGESTION — Thursday June 19
⚠ Expiring: Salmon fillets (4 lbs, expires Friday)

Suggested special:
  Pan-Seared Salmon with Lemon Butter Risotto
  Ingredient cost:  $8.40
  Suggested price:  $26.00
  Food cost %:      32.3% ✓
  Estimated margin: $17.60 per cover

Pairs with: Chardonnay (current slow mover — bundle opportunity)

[Approve Special] [Edit] [Skip]
```

---

## Price Optimization

When food cost on a dish crosses threshold, agent generates a clear recommendation:

```
PRICE ALERT — Grilled Salmon
Current price:      $24.00
Current food cost:  $8.90 (37.1%) ⚠ above 35% target
Ingredient spike:   Atlantic salmon +22% this week

Option 1: Raise price to $27.00 → food cost 33.0% ✓
Option 2: Reduce portion by 1oz → food cost 33.8% ✓
Option 3: Substitute salmon variety → estimated 28.0% ✓
Option 4: Remove from menu temporarily

Competitor avg price for salmon: $25–$28 (market check)

[Choose Option] [Flag for Chef Review]
```

---

## Menu Item Report

Agent generates a clear performance report per item:

```
MENU ITEM REPORT — Chicken Marsala
Category:           STAR ⭐
Sales (last 30d):   187 orders (#2 most ordered)
Revenue:            $5,049
Food cost %:        29.4% ✓
Contribution margin: $19.28 per order
Total margin (30d): $3,606

Trend:              ↑ +12% vs prior 30 days
Pairs with:         House Salad (ordered together 68% of time)

Recommendation: Protect. Feature in marketing. Consider prix fixe inclusion.
```

---

## Actions
- Update menu engineering matrix weekly
- Alert when dish food cost crosses threshold with pricing options
- Generate daily special suggestions from expiring ingredients
- Flag dead items for removal review
- Recalculate food cost % across all dishes when any ingredient price changes
- Push top-margin items to Guest Relationship Agent for promotion targeting
- Sync active menu and prices to Digital Menu module

---

## Outputs

### Daily
- Expiring ingredient special suggestions (from Agent 1 signals)
- Any food cost threshold alerts

### Weekly
- Menu engineering matrix update — full menu classification
- Top 5 and bottom 5 dishes by contribution margin
- Items that changed category this week (e.g., Star → Plowhorse)
- Pricing alerts for dishes above food cost target

### Monthly
- Full menu performance report
- Revenue and margin by category (appetizers, mains, desserts, beverages)
- Dead items list with removal recommendations
- New item performance review (items launched in past 60 days)
- Seasonal menu planning input based on ingredient cost trends

---

## Key Metrics Tracked
- **Food cost % per dish** (target: under 32% average, alert at 35%)
- **Contribution margin per dish** ($)
- **Sales mix %** — each dish as % of total orders
- **Menu engineering classification** per item (Star / Puzzle / Plowhorse / Dog)
- **Daily special conversion** — how many specials get approved and sold
- **Average menu food cost %** across full menu

---

## Seasonal Menu Planning

Agent tracks ingredient cost trends over time and proactively flags seasonal opportunities:

```
SEASONAL ALERT — June
Tomatoes:     peak season, cost down 28% vs January → feature in new dishes
Asparagus:    end of season, cost rising → phase out of menu
Zucchini:     peak season, underused → suggest new preparation
Berries:      peak season → dessert and cocktail opportunity
```

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 1 — Inventory Guardian | Receives expiring ingredient signals, current ingredient costs |
| Agent 5 — Vendor & Procurement | Ingredient price data from vendor invoices |
| Agent 6 — Vendor Intel | Market price benchmarks for ingredients |
| Agent 8 — Bookkeeping | Actual food cost validation against invoices |
| Agent 12 — Guest Relationship | Sends top-margin items for targeted guest promotions |

---

## Human Approval Required
- Price changes before updating menu
- Daily special before it goes live
- Dead item removal from menu
- Seasonal menu additions or removals
