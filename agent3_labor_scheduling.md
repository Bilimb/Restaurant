# Agent 3 — Labor & Scheduling

## Role
Long-running agent that keeps labor cost aligned with revenue at all times. Watches scheduled hours vs actual sales, catches overtime before it happens, and prepares the weekly schedule so the manager just edits and approves rather than building from scratch. Connects payroll data from ADP or Gusto to give a complete labor cost picture.

---

## Data Sources
| Source | What It Reads |
|---|---|
| POS (Toast / Square) | Actual sales by hour, day, shift — revenue per period |
| ADP / Gusto | Employee roster, pay rates, hours worked, payroll history |
| Reservations (platform) | Upcoming covers booked — projects service demand |
| Platform schedule | Published shifts, assigned staff, roles |
| Forecasting Agent (future) | Projected revenue for upcoming week |
| Time clock (POS or ADP) | Actual clock-in / clock-out times |

---

## Monitors Continuously
- Scheduled labor hours and cost for current and upcoming week
- Actual clock-in / clock-out vs scheduled shifts
- Labor cost % against live sales throughout the day
- Overtime hours accumulating per employee per week
- Upcoming reservation load vs scheduled staffing level
- No-show and late arrivals in real-time

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Labor cost % high | Labor tracking above target % mid-week | Alert manager — still time to adjust remaining shifts |
| Overtime approaching | Employee within 4 hours of 40-hour threshold | Alert before overtime triggers |
| Understaffed shift | Reservations spike vs scheduled coverage | Suggest adding staff for that shift |
| Overstaffed shift | Slow week forecast vs heavy schedule | Suggest cutting shifts or sending staff home early |
| No-show | Staff member does not clock in within 15 min of shift | Alert manager immediately |
| Schedule not published | Next week schedule unpublished 3 days before week starts | Reminder to manager |
| Pay rate change | ADP sends updated pay rate for employee | Recalculates labor cost projections |

---

## Weekly Schedule Preparation

The agent prepares a draft schedule every week before the manager needs to publish it. Manager edits and approves — does not build from scratch.

### How It Works
1. Agent pulls next week's reservation data and sales forecast
2. Compares against historical staffing patterns for similar weeks
3. Drafts a schedule that hits the labor cost % target
4. Flags any conflicts: requested time off, availability constraints, overtime risk
5. Manager opens draft, makes edits, publishes

### Draft Schedule Format
```
DRAFT SCHEDULE — Week of June 22, 2026
Prepared by: Labor Agent | Target labor cost: 28%
Projected revenue: $28,400 | Labor budget: $7,952

            MON    TUE    WED    THU    FRI    SAT    SUN
Maria S.    OFF    11-4   11-4   OFF    5-11   5-11   11-4
James P.    5-11   OFF    5-11   5-11   5-11   OFF    OFF
Sofia R.    11-4   11-4   OFF    11-4   OFF    12-8   12-8
...

Projected labor cost: $7,840 (27.6%) ✓
⚠ James P. will hit 38 hrs by Saturday — review Sunday shift
⚠ Friday 7pm: 42 covers booked, only 3 servers scheduled — consider adding 1

[Edit Schedule] [Approve & Publish] [Request Changes]
```

---

## Labor Cost Tracking

Agent tracks labor cost % in real-time throughout the day, not just at payroll:

```
LABOR SNAPSHOT — Friday June 20, 8:00pm
Sales so far today:     $3,840
Labor cost so far:      $1,018
Labor cost %:           26.5% ✓ (target 28%)

Remaining shift cost:   $420 (estimated)
Projected end of day:   27.2% ✓

Staff currently on:     8
Busiest hour:           7pm (41 covers)
```

---

## Overtime Management

Agent tracks every employee's weekly hours in real-time and alerts before overtime triggers — not after payroll runs.

```
OVERTIME ALERT — Thursday June 19
James Park — 36.5 hrs worked this week
Scheduled Saturday: 6-hour shift = 42.5 hrs total
Overtime cost: ~$45 at 1.5x rate

Options:
  → Shorten Saturday shift to 3.5 hrs (stays under 40)
  → Swap Saturday with part-time staff
  → Approve overtime (adds $45 to labor cost)

[Adjust Schedule] [Approve Overtime]
```

---

## Actions
- Generate draft weekly schedule based on forecast and labor target
- Alert manager when labor % is tracking above target mid-shift
- Alert before overtime triggers for any employee
- Notify immediately when staff no-show
- Recommend adding or cutting shifts based on sales forecast
- Sync actual hours worked to Agent 8 (Bookkeeping) for payroll cost entries
- Feed labor cost % to Agent 9 (Financial Watchdog) for daily P&L

---

## Outputs

### Daily
- Labor cost % vs sales (included in morning briefing)
- Current day staffing snapshot (who is on, what shifts remain)
- No-show alerts in real-time

### Weekly
- Draft schedule for manager approval (published 3 days before week starts)
- Labor cost vs target for the week just closed
- Overtime report: who hit overtime, what it cost

### Monthly
- Labor efficiency report: revenue generated per labor hour by role
- Overtime cost summary
- Labor cost % trend vs prior months
- Top labor cost days and shifts

---

## Key Metrics Tracked
- **Labor cost %** (target configurable per restaurant, typically 28–32%)
- **Revenue per labor hour** by role (server, cook, dishwasher)
- **Overtime hours and cost** per week
- **Schedule accuracy** — how close projected labor was to actual
- **No-show rate** per employee
- **Labor budget variance** — actual vs budgeted weekly labor spend

---

## Role-Based Labor Cost

Agent breaks labor cost by department, not just total:

| Role | Typical Target | Why It Matters |
|---|---|---|
| FOH (servers, hosts) | 10–12% of sales | Directly tied to covers and service volume |
| BOH (kitchen, prep) | 14–16% of sales | Fixed regardless of covers — hard to flex |
| Management | 4–6% of sales | Salaried — tracks separately |
| Total | 28–32% of sales | Prime cost target with food cost combined |

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 8 — Bookkeeping & Accounting | Receives weekly labor cost for P&L and payroll entries |
| Agent 9 — Financial Watchdog | Feeds daily labor cost % for prime cost tracking |
| Agent 7 — Staff Performance | Shares individual server revenue and shift data |
| Agent 13 — Reputation Monitor | Cross-references service complaints with understaffed shifts |

---

## Human Approval Required
- Weekly schedule before publishing
- Overtime approval when flagged
- Shift cuts or additions recommended by agent
- Pay rate changes (come from ADP, confirmed in platform)
