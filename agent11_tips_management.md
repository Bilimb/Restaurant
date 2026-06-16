# Agent 11 — Tips Management

## Role
Long-running agent that tracks every dollar of tips across every shift, calculates distributions correctly based on the restaurant's configured rules, prepares payroll tip summaries, and keeps the restaurant compliant with IRS tip reporting requirements. Eliminates the manual tip sheet calculation that most restaurants still do by hand at the end of every shift.

---

## Data Sources
| Source | What It Reads |
|---|---|
| POS (Toast / Square) | Credit card tips per check, per shift — who collected them |
| Staff clock-in / clock-out (POS or ADP) | Hours worked per employee per shift |
| Agent 3 — Labor & Scheduling | Shift assignments, roles, tenure per employee |
| Agent 8 — Bookkeeping | Tip payout entries for payroll records |
| Platform tip structure config | How this restaurant distributes tips |

---

## How Tips Actually Work

### Cash Tips
Server keeps 100% of cash tips they receive directly from the table. No pooling, no sharing. These are logged by the server at end of shift and go directly to them.

### Card Tips
Card tips do not go 100% to the server who collected them. All card tips collected across the shift are pooled together and divided among all staff who worked that shift — based on the restaurant's configured distribution rules.

This is the standard reality in most restaurants and what the agent calculates automatically.

---

## Tip Distribution Configuration

Every restaurant sets their own rules. The platform supports any structure. Common configurations:

### Seniority-Based (Most Common)
Staff receive a share of the card tip pool weighted by their seniority tier and hours worked:

```
EXAMPLE CONFIGURATION — Seniority Tiers
  Tier 1: Worked 12+ months  → weight: 50 points/hr
  Tier 2: Worked 6–12 months → weight: 35 points/hr
  Tier 3: New (0–6 months)   → weight: 20 points/hr
```

### Hours-Based (Equal Split)
All staff in the same role share equally by hours worked — no seniority weighting.

### Role-Based Split
Pool split by role: servers get X%, bussers get Y%, runners get Z%.

### Flat Equal Split
All staff on the shift split the card tip pool equally regardless of hours or seniority.

Platform is configured per restaurant at setup. Rules can be updated by the owner at any time.

---

## Shift-End Tip Calculation

### Example: Seniority-Based Configuration

```
SHIFT TIP SUMMARY — Friday June 13, Dinner Service

CARD TIPS COLLECTED (from POS)
  Maria Santos      $186.00   (Tier 1 — 2 yrs, worked 6 hrs)
  James Park        $154.00   (Tier 1 — 18 mos, worked 6 hrs)
  Sofia Rivera      $142.00   (Tier 2 — 8 mos, worked 5 hrs)
  Alex Kim          $128.00   (Tier 3 — 3 mos, worked 5 hrs)
  Total card tips:  $610.00   → goes into pool

CASH TIPS (each server keeps their own — no pooling)
  Maria Santos       $40.00   → keeps directly
  James Park         $22.00   → keeps directly
  Sofia Rivera       $18.00   → keeps directly
  Alex Kim            $0.00   → keeps directly

CARD TIP POOL DISTRIBUTION
  Calculating points per person (tier weight × hours worked):

  Maria Santos:   50pts × 6hrs = 300 pts
  James Park:     50pts × 6hrs = 300 pts
  Sofia Rivera:   35pts × 5hrs = 175 pts
  Alex Kim:       20pts × 5hrs = 100 pts
  Total points:                  875 pts

  Maria share:   300/875 = 34.3%  → $610 × 34.3% = $209.20
  James share:   300/875 = 34.3%  → $610 × 34.3% = $209.20
  Sofia share:   175/875 = 20.0%  → $610 × 20.0% = $122.00
  Alex share:    100/875 = 11.4%  → $610 × 11.4% =  $69.60

FINAL TAKE-HOME PER EMPLOYEE
  Maria Santos:   $209.20 (card) + $40.00 (cash) = $249.20
  James Park:     $209.20 (card) + $22.00 (cash) = $231.20
  Sofia Rivera:   $122.00 (card) + $18.00 (cash) = $140.00
  Alex Kim:        $69.60 (card) +  $0.00 (cash) =  $69.60

[Approve Distribution] [Edit] [Flag Issue]
```

---

## Cash Tip Reporting

Agent prompts each server to report cash tips at the end of their shift:

```
CASH TIP REPORT — Maria Santos
Shift: Friday Jun 13, Dinner

Please enter your cash tips for this shift:
Cash tips received: $ [____]

[Submit]
```

Cash tips are self-reported. Agent logs the amount and includes in payroll records for IRS compliance. If a server reports zero cash tips consistently, flagged for manager awareness — not accusatory, but tracked.

---

## Payroll Sync

Every pay period, agent compiles the complete tip summary for ADP or Gusto:

```
PAYROLL TIP SUMMARY — Pay Period June 9–22, 2026

EMPLOYEE         CARD TIPS   CASH TIPS   TOTAL TIPS
Maria Santos      $892.40     $124.00     $1,016.40
James Park        $892.40      $86.00       $978.40
Sofia Rivera      $520.00      $74.00       $594.00
Alex Kim          $296.80      $42.00       $338.80

Ready to sync to ADP: [Approve & Sync] [Review First]
```

---

## IRS Compliance

### 8% Minimum Threshold
IRS requires total reported tips equal at least 8% of gross food and beverage sales. Agent checks this monthly:

```
IRS COMPLIANCE CHECK — May 2026
Gross food & beverage sales:   $142,000
8% minimum threshold:           $11,360
Total tips reported by staff:   $13,240  ✓ (9.3% — above threshold)
```

### FICA Tip Credit
Agent calculates the employer FICA tip credit (IRS Form 8846) — a tax credit restaurants receive for paying employer FICA taxes on employee tips above minimum wage. Most small restaurant owners are unaware of this credit:

```
FICA TIP CREDIT — Q2 2026
Total tips reported:           $38,420
Tips above federal minimum wage threshold: $32,180
Employer FICA rate:             7.65%
Estimated credit this quarter: $2,462  → sent to Agent 8 for tax filing
```

---

## Tip Trends

Agent surfaces patterns over time:

```
TIP ANALYSIS — June 2026

Average card tip pool by day:
  Friday dinner:    $684  (highest volume)
  Saturday dinner:  $612
  Thursday dinner:  $480
  Tuesday lunch:    $182  (lowest)

Average tip % left by guests:
  Friday dinner:    21.4%
  Saturday dinner:  20.8%
  Tuesday lunch:    14.8%

Insight: Tuesday lunch guests tip significantly less.
         Factor into scheduling — senior staff may prefer
         not to work this shift. Consider discussing.
```

---

## Actions
- Calculate card tip pool and distribution automatically at end of every shift
- Prompt staff to report cash tips via mobile or POS
- Generate payroll tip summary each pay period for ADP / Gusto sync
- Track IRS 8% minimum compliance monthly
- Calculate FICA tip credit quarterly and send to Agent 8
- Feed tip % trends to Agent 7 (Staff Performance)
- Post all tip entries to Agent 8 (Bookkeeping)

---

## Outputs

### Per Shift
- Card tip pool distribution ready for manager approval
- Cash tip reporting prompts to each server

### Per Pay Period
- Full tip summary by employee ready for payroll sync

### Monthly
- IRS 8% threshold compliance check
- Tip trend analysis by day and shift

### Quarterly
- FICA tip credit calculation for tax filing

### Annually
- Full year tip summary for CPA
- FICA tip credit total

---

## Key Metrics Tracked
- **Card tip pool total per shift**
- **Cash tips reported per employee per shift**
- **Average guest tip %** by day and shift
- **IRS 8% compliance ratio** monthly
- **FICA tip credit** quarterly savings

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 3 — Labor & Scheduling | Hours worked and tenure per employee per shift |
| Agent 7 — Staff Performance | Tip % data as guest satisfaction signal |
| Agent 8 — Bookkeeping & Accounting | Receives tip entries and FICA credit for records |

---

## Human Approval Required
- Card tip distribution each shift before payouts finalized
- Payroll tip summary before syncing to ADP / Gusto
- Any tip structure configuration changes
- Dispute resolution when a staff member questions their distribution
