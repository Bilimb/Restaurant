# Agent 12 — Guest Relationship Manager

## Role
Long-running agent that builds and maintains a profile for every guest who has ever visited the restaurant, tracks their behavior over time, detects when regulars go quiet, and helps the restaurant reach the right guest with the right message at the right moment. Turns anonymous POS transactions and reservations into real guest relationships — without the owner manually managing a contact list.

---

## Data Sources
| Source | What It Reads |
|---|---|
| POS (Toast / Square) | Every transaction — what was ordered, how much spent, payment method, date and time |
| Reservations (platform) | Booking history, party size, special occasion notes, dietary notes |
| Agent 13 — Reputation Monitor | Reviews written by guests — links review to guest profile where possible |
| Agent 11 — Tips Management | Tip % per guest visit (proxy for satisfaction) |
| Agent 4 — Menu Performance | Which menu items are top performers — used for targeted announcements |
| Loyalty program (platform) | Points balance, rewards redeemed, tier status |

---

## Guest Profile

Every guest builds a profile automatically over time. No manual data entry required.

```
GUEST PROFILE — Maria Chen
First visit:        March 4, 2024
Total visits:       24
Last visit:         June 10, 2026
Visit frequency:    Every 10–14 days (regular)
Avg spend/visit:    $68.40
Total lifetime spend: $1,641.60
Loyalty tier:       Gold

ORDERING PATTERNS
  Always orders:    Salmon dishes, Chardonnay
  Never orders:     Red meat
  Special requests: Gluten-free pasta when available
  Typical party:    2 people (usually same companion)

OCCASIONS
  Birthday:         September 12
  Anniversary noted: March 4 (first visit was anniversary dinner)

RESERVATIONS
  Prefers:          Table 8 (corner, noted by host)
  Booking pattern:  Always books Thursday or Friday evening

REVIEWS
  Left 5-star Google review: April 2025
  Mentioned: "Salmon is always perfect"

LOYALTY
  Points balance:   840 pts
  Last reward:      Free dessert — May 2026
```

---

## Guest Segmentation

Agent automatically segments all guests into actionable groups:

| Segment | Definition | Size (typical) |
|---|---|---|
| VIP Regulars | 10+ visits, high spend, recent | 5–10% of guests |
| Active Regulars | Visit every 2–4 weeks consistently | 15–20% |
| Occasionals | Visit 2–5x per year | 30–40% |
| Lapsed Regulars | Were regular, haven't visited in 6+ weeks | 10–15% |
| One-Time Guests | Visited once, never returned | 25–35% |
| New Guests | First visit within last 30 days | Varies |

Segments update automatically as guest behavior changes.

---

## Monitors Continuously
- Every new POS transaction — updates guest profile, visit frequency, spend
- Visit cadence per guest — detects when a regular breaks their pattern
- Birthday and anniversary dates approaching
- Loyalty points accumulating toward rewards
- New menu items launching that match a guest's known preferences
- Guests who left reviews — links to profile and tracks sentiment

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Lapsed regular | Regular guest (5+ visits) not seen in 6 weeks | Draft re-engagement offer |
| Birthday approaching | Guest birthday within 14 days | Draft personalized birthday offer |
| Anniversary approaching | Noted anniversary within 14 days | Draft personalized message |
| Loyalty milestone | Guest reaches reward threshold | Trigger reward notification |
| New menu item | Item matches guest's known preferences | Draft targeted announcement |
| VIP arriving tonight | Regular with 10+ visits has reservation | Alert FOH manager before service |
| One-time guest | Guest who visited once, 30 days passed | Draft win-back offer |
| Guest left bad review | Negative review linked to guest profile | Flag if they visit again — handle with care |

---

## Re-Engagement Campaign

When a lapsed regular is detected:

```
LAPSED GUEST ALERT — James Park
Profile: Regular, 8 visits over 14 months
Last visit: April 28, 2026 (47 days ago)
Avg visit frequency: every 15 days
Current gap: 47 days — 3x their normal frequency

DRAFT MESSAGE (SMS or Email)
  "Hi James, it's been a while since we've seen you
  at Jibek Jolu! We'd love to have you back.
  As our thanks, enjoy a complimentary appetizer
  on your next visit — just mention this message.
  Hope to see you soon."

Offer: Complimentary appetizer (cost: ~$8, avg spend if returns: $68)
Discount code: WELCOME-JAMES-2026
Expiry: 30 days

[Approve & Send] [Edit Message] [Edit Offer] [Skip]
```

---

## Birthday & Anniversary Outreach

```
BIRTHDAY ALERT — Maria Chen
Birthday: September 12 (14 days away)
Profile: Gold tier, 24 visits, loves salmon

DRAFT MESSAGE
  "Maria, your birthday is coming up and we'd love
  to celebrate with you! Book a table for your
  special day and enjoy a complimentary dessert
  on us. We'll have your favorite corner table ready.
  Reserve: [booking link]"

Timing: Send September 5 (7 days before)

[Approve & Schedule] [Edit] [Skip]
```

---

## New Menu Announcement Targeting

When a new dish launches, agent identifies which guests to tell based on order history:

```
NEW ITEM LAUNCH — Pan-Seared Halibut (added June 15)

TARGETED GUEST LIST
  Guests who frequently order fish dishes: 84 guests
  Guests who ordered salmon 3+ times: 62 guests
  Combined unique list: 104 guests

DRAFT ANNOUNCEMENT
  "We just added something we think you'll love —
  Pan-Seared Halibut with Lemon Caper Butter.
  Made with fresh Great Lakes halibut, it's on the
  menu now. Come try it this week."

[Approve & Send to 104 guests] [Preview List] [Edit]
```

---

## VIP Pre-Service Alert

When a high-value regular has a reservation tonight, agent alerts FOH before service:

```
VIP ALERT — Tonight's Reservations

7:00pm — Maria Chen, party of 2, Table 8
  ⭐ 24 visits, Gold loyalty member
  Prefers: Table 8 (corner), Chardonnay on arrival
  Special: Gluten-free options when available
  Note: Left 5-star review mentioning salmon
  Action: Greet by name, note preference to server

8:30pm — David Kim, party of 4
  ⭐ 11 visits, Silver loyalty member
  Last visit: Birthday dinner — had a great experience
  Action: Greet by name
```

---

## Loyalty Program

Built into the platform — no third-party tool needed:

```
LOYALTY CONFIGURATION (configurable per restaurant)
  Points earned:    1 point per $1 spent
  Bronze tier:      0–199 pts    → welcome gift on 5th visit
  Silver tier:      200–499 pts  → free dessert every 10 visits
  Gold tier:        500+ pts     → priority reservations + birthday gift
  Birthday reward:  Free dessert (all tiers)
  Referral reward:  50 pts for referring a new guest
```

Points accumulate automatically from POS transactions. Guest sees their balance via SMS or email — no app download required.

---

## Actions
- Build and continuously update guest profiles from POS and reservation data
- Detect lapsed regulars and draft re-engagement messages
- Draft birthday and anniversary outreach timed 7–14 days before
- Generate targeted new menu announcements based on order history
- Send VIP pre-service alerts to FOH manager before service
- Trigger loyalty rewards when thresholds are reached
- Flag guests who left negative reviews if they visit again

---

## Outputs

### Before Each Service
- VIP guest alert for tonight's reservations

### Weekly
- Lapsed guest list with draft re-engagement messages
- New loyalty milestones reached this week
- Guest acquisition summary: new guests, returning guests, one-time visit rate

### Monthly
- Guest retention report: how many regulars are still active vs lapsed
- Campaign performance: re-engagement offers sent vs redeemed
- Loyalty program summary: points outstanding, rewards redeemed, tier breakdown
- Lifetime value report: top 20 guests by total spend

---

## Key Metrics Tracked
- **Active regular retention rate** — are regulars staying regular?
- **Lapsed guest recovery rate** — % who return after re-engagement offer
- **Average visit frequency** per segment
- **Guest lifetime value** — total spend over relationship
- **Campaign redemption rate** — offers sent vs used
- **Loyalty program engagement** — % of guests enrolled and active

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 4 — Menu Performance | New menu items for targeted announcements |
| Agent 7 — Staff Performance | Server assigned to VIP table (ensure best server) |
| Agent 11 — Tips Management | Tip % as visit satisfaction signal |
| Agent 13 — Reputation Monitor | Review data linked to guest profiles |

---

## Human Approval Required
- All outreach messages before sending (agent drafts, owner or manager approves)
- Loyalty structure changes
- Discount offers and their value
- VIP handling decisions
