# Agent 3 — Inventory Burn Calculator

## Overview

The Inventory Burn Calculator is an AI agent that runs nightly after close of business. It takes each day's Toast sales data, calculates the theoretical ingredient consumption based on recipes, and compares that against what was actually ordered and received from suppliers that week. The gap between theoretical and actual tells the owner exactly where product is disappearing.

---

## Problem It Solves

Small restaurants rarely do daily inventory counts — it takes too long and requires a trained person. Without it, owners discover waste, theft, or over-portioning only when food cost is already out of control. By the time the QuickBooks report shows a bad month, thousands of dollars are already gone.

---

## How It Works

1. **Pull sales data from Toast**
   Each night, the agent fetches item-level sales for the day: how many of each menu item was sold.

2. **Apply recipe logic**
   Using the recipe database (built by the Menu Cost Calculator), it multiplies units sold by ingredient quantities per dish.
   Example: 40 orders of Chicken Sandwich = 40 × 6oz chicken = 15 lbs chicken consumed.

3. **Pull purchase data from QuickBooks / invoices**
   Reads what was received from suppliers this week (from processed invoices) and any opening inventory entered by the owner.

4. **Calculate the burn gap**
   `Expected on Hand = Opening Inventory + Received − Theoretical Consumption`
   Compares expected vs actual count (owner does a quick count of high-value items once a week — takes 10 minutes).

5. **Flag anomalies**
   Any ingredient where actual is more than 8–10% below theoretical triggers an alert with the dollar value of the discrepancy.

---

## Output

- **Nightly Excel report** — one row per ingredient: theoretical use, actual use, variance %, variance in dollars
- **Weekly summary email** — top 5 problem ingredients ranked by dollar loss
- **Threshold alerts** — immediate notification when a single-day variance exceeds a set dollar amount (e.g. $50)

---

## What It Catches

| Issue | How It Shows Up |
|-------|----------------|
| Over-portioning | Protein or premium ingredients consistently burning faster than recipe spec |
| Spoilage / waste | Ingredients disappearing without matching sales |
| Employee theft | High-value items (steak, shrimp, alcohol) with no sales correlation |
| Recipe inconsistency | Variance spikes on days a specific cook is on shift |
| Receiving errors | Vendor shipped less than invoiced |

---

## Data Requirements

| Source | Data Needed | How It's Accessed |
|--------|-------------|-------------------|
| Toast POS | Item-level sales by day | Toast API |
| Recipe database | Ingredient quantities per menu item | Built by Menu Cost Calculator (Agent 1) |
| Supplier invoices | What was received and at what quantity | Invoice Processor (Agent 2) |
| Owner | Weekly spot count of top 10 high-value ingredients | Simple Excel input or text message |

---

## Integration Points

- Depends on **Menu Cost Calculator** for recipe data
- Depends on **Invoice Processor** for purchase data
- Feeds into **Vendor Price Tracker** (Agent 6) — if a variance is traced to a receiving short, it's flagged there too
- Feeds into **Daily P&L Briefing** (Agent 7) — waste dollar figure rolls up into the morning report

---

## Limitations

- Accuracy depends on recipe data being kept up to date when the menu changes
- Does not replace a full physical inventory count — it narrows where to look, not a full audit
- Combo items and specials must be mapped to ingredients manually on first setup

---

## Setup Effort

| Task | Time |
|------|------|
| Enter all recipes into the system (one-time) | 4–6 hours |
| Connect Toast API | 30 minutes |
| Verify invoice processor is running | Already done in Phase 1 |
| First useful report | Day 1 after setup |

---

## Estimated Impact

A typical 1–2 location restaurant losing 2–4% of food cost to undetected waste or theft on $800K annual food purchases loses **$16,000–$32,000 per year**. Catching and correcting even half of that variance pays for the entire agent platform many times over.
