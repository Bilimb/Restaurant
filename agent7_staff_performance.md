# Agent 7 — Staff Performance

## Role
Long-running agent that tracks how every front-of-house staff member is performing using real POS data. Knows who is driving revenue, who is upselling, who has excessive voids, and where training is needed — without the manager having to pull reports manually. Gives the owner and manager an objective, data-driven picture of their team, not based on gut feel or favoritism.

---

## Data Sources
| Source | What It Reads |
|---|---|
| POS (Toast / Square) | Every transaction per server: sales, check size, items ordered, voids, comps, tips |
| Agent 3 — Labor & Scheduling | Shift assignments, hours worked per staff member |
| Platform staff register | Employee profiles, roles, hire date, pay rate, training status |
| Reservations (platform) | Table assignments, party size, occasion notes |

---

## Staff Roles Tracked

### Front of House (primary focus)
- Servers / Waitstaff
- Bartenders (sales side, not bar inventory)
- Food runners and bussers (efficiency metrics only)
- Host / Reservations (covers seated, wait time, no-show handling)

### Back of House (limited metrics)
- Kitchen speed: ticket times per cook (if POS provides kitchen display data)
- BOH performance tracked at team level, not individual, unless KDS data available

---

## Monitors Continuously
- Revenue generated per server per shift
- Average check size per server vs restaurant average
- Upsell rate: appetizers, desserts, and add-ons sold per table
- Void and comp rate per server
- Tip percentage received (guest satisfaction proxy)
- Table turn time per server
- Hours worked vs revenue generated (efficiency)

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Underperformance | Server revenue consistently 25%+ below team average over 2 weeks | Flag for manager review with data |
| High void rate | Server voids exceed 3% of sales over trailing 7 days | Investigate — mistakes or training gap |
| Upsell opportunity | Server upsell rate below 20% of tables | Training suggestion: appetizers, desserts |
| Top performer | Server consistently in top 3 for 4+ weeks | Recognition alert to manager |
| New hire tracking | Staff member in first 30 days | Weekly performance check vs team baseline |
| Tip anomaly | Server tips significantly below team average | Flag — may indicate guest experience issue |
| Excessive comps | Server comp rate above threshold | Manager review required |

---

## Performance Metrics Per Server

Agent tracks every server on a consistent scorecard:

```
STAFF SCORECARD — Maria Santos (Server)
Period: June 1–14, 2026

Shifts worked:        11
Total hours:          88
Revenue generated:    $14,240
Revenue per hour:     $161.82 ✓ (team avg: $148.00)
Average check:        $52.40 ✓ (team avg: $48.20)

UPSELL PERFORMANCE
  Appetizers sold:    68% of tables ✓ (team avg: 54%)
  Desserts sold:      41% of tables ✓ (team avg: 28%)
  Drink upsells:      72% of tables ✓ (team avg: 61%)

ACCURACY
  Void rate:          0.8% ✓ (threshold: 3%)
  Comp rate:          0.4% ✓ (threshold: 1%)

GUEST SATISFACTION
  Avg tip %:          21.4% ✓ (team avg: 18.9%)

Table turn time:      52 min avg (target: 55 min) ✓

Classification:       TOP PERFORMER ⭐
Recommendation:       Consider for training role / mentor new hires
```

---

## Team Leaderboard

Agent generates a team view so manager can see performance at a glance:

```
TEAM LEADERBOARD — Week of June 9, 2026

SERVERS          REVENUE    AVG CHECK   UPSELL%   VOID%   TIP%
Maria Santos     $6,840 ⭐   $52.40      68%       0.8%    21.4%
James Park       $6,210      $49.80      61%       1.1%    19.8%
Sofia Rivera     $5,940      $47.60      55%       1.4%    18.6%
Alex Kim         $5,120      $44.20      48%       2.1%    17.2%
Tom Walsh        $3,980 ⚠    $38.40      31%       4.2%    14.8%

⚠ Tom Walsh: below average on all metrics for 3 consecutive weeks.
  High void rate (4.2%) and low upsell rate (31%) suggest training needed.
  Recommend: manager conversation + floor coaching this week.
```

---

## Training Recommendations

Agent does not just flag underperformance — it suggests specific actions:

```
TRAINING RECOMMENDATION — Tom Walsh

Issue 1: Low upsell rate (31% vs 54% team average)
Specific gap: Rarely suggests appetizers or desserts
Suggested action: Shadow Maria Santos for 2 shifts —
                  she upsells appetizers at 68% rate

Issue 2: High void rate (4.2%)
Pattern: Most voids occur on weekend dinner shifts
         (high volume, likely rushing)
Suggested action: Review order-taking process for
                  multi-table management

Issue 3: Below average check size
Likely cause: Not suggesting add-ons or premium items
Suggested action: Review menu knowledge —
                  test on specials and high-margin items
```

---

## New Hire Tracking

For staff in their first 30 days, agent watches closely and gives weekly check-ins:

```
NEW HIRE REPORT — Week 2 — Sofia Rivera
Start date: June 1, 2026

vs team average:
  Revenue per hour:   $122 (team avg $148) — expected for new hire
  Avg check:          $42.80 — improving from $38.20 week 1
  Upsell rate:        38% — below avg but trending up
  Void rate:          2.8% — watch closely

Progress: On track. Check again at 30-day mark.
Highlight: Tip % already at 18.2% — guests responding well.
```

---

## Shift-Level Insights

Agent tracks performance at shift level, not just aggregate, to catch patterns:

```
SHIFT PATTERN ALERT — Alex Kim
Friday dinner shifts: avg revenue $680/shift
Saturday lunch shifts: avg revenue $310/shift

Gap is 2.2x — unusual for same server.
Possible causes:
  → Different table section assigned?
  → Lower energy on Saturday mornings?
  → Fewer covers scheduled on Saturday?

Recommend: Manager observation on next Saturday lunch shift.
```

---

## Actions
- Generate individual staff scorecards after every shift
- Update team leaderboard weekly
- Flag underperformers with specific data and suggested actions
- Identify top performers for recognition and mentoring roles
- Generate training recommendations based on specific metric gaps
- Track new hire progress weekly for first 60 days
- Feed labor efficiency data to Agent 3 (Labor & Scheduling)

---

## Outputs

### Daily
- Shift performance summary per server (available on demand)
- Any immediate flags (excessive voids, no-shows cross-referenced with Agent 3)

### Weekly
- Team leaderboard
- Underperformer flags with training recommendations
- Top performer recognition alerts to manager
- New hire progress reports

### Monthly
- Full staff performance report
- Revenue per labor hour by role
- Upsell trend across team (is training working?)
- Void and comp cost summary (what underperformance is costing in lost revenue)

---

## Key Metrics Tracked
- **Revenue per labor hour** per server
- **Average check size** vs team average
- **Upsell rate** by category (appetizers, desserts, drinks)
- **Void rate %** (target: under 2%)
- **Comp rate %** (target: under 1%)
- **Average tip %** (guest satisfaction proxy)
- **Table turn time** (target: configurable per restaurant)

---

## Privacy and Fairness Principles
- Data used for coaching and development, not punishment
- Manager sees individual data, staff members can see their own scorecard only
- Recommendations are always framed as development opportunities
- Agent flags patterns, manager makes all people decisions

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 3 — Labor & Scheduling | Shares shift data, hours worked per staff member |
| Agent 12 — Guest Relationship | Cross-references server with guest return rates (do certain servers bring guests back?) |
| Agent 13 — Reputation Monitor | Cross-references service complaints in reviews with server schedules |

---

## Human Approval Required
- All staff conversations and coaching actions (agent flags, manager acts)
- Any formal performance documentation
- Schedule changes based on performance data (recommended by agent, decided by manager)
