# Agent 13 — Reputation Monitor

## Role
Long-running agent that starts by collecting and analyzing every historical review the restaurant has ever received across all platforms, giving the owner an immediate intelligence picture of what guests really think. From there it watches every platform 24/7, analyzes every new review the moment it posts, drafts responses within 15 minutes, identifies recurring complaint patterns before they become rating damage, and helps the restaurant generate more positive reviews from happy guests. The owner never needs to manually check Google or Yelp again — and for the first time gets a clear, data-driven view of their reputation.

---

## Data Sources
| Source | What It Reads |
|---|---|
| Google My Business API | All reviews, rating, owner responses, Q&A |
| Yelp Fusion API | All reviews, rating, check-ins, photos |
| TripAdvisor | Reviews and rating (scraping where API limited) |
| Toast Guest Surveys | Post-visit surveys sent by POS after each check |
| Agent 12 — Guest Relationship | Guest profiles — links reviewers to known guests |
| Agent 3 — Labor & Scheduling | Shift data to cross-reference complaints with staffing |
| Agent 7 — Staff Performance | Server data to cross-reference service complaints |

---

## Platforms Monitored

| Platform | Check Frequency | Response Capability |
|---|---|---|
| Google | Every 15 minutes | Yes — draft + post via GMB API |
| Yelp | Every 15 minutes | Yes — draft for owner to post manually |
| TripAdvisor | Every hour | Draft for owner to post manually |
| Toast Guest Surveys | Real-time | Direct response via platform |
| DoorDash / UberEats ratings | Daily | Flag for awareness |

---

## Historical Review Collection & Analysis

When a restaurant first onboards, agent immediately collects every available historical review from all platforms — not just future ones. This gives the restaurant a full intelligence picture from day one.

### Onboarding Collection
```
INITIAL REVIEW COLLECTION — Jibek Jolu Restaurant
Collecting all available historical reviews...

Google:        284 reviews collected (Jan 2019 – present)
Yelp:           96 reviews collected (Mar 2019 – present)
TripAdvisor:    41 reviews collected (Jun 2020 – present)
Toast Surveys:  188 surveys collected (Sep 2021 – present)

Total:         609 reviews
Processing...  Sentiment analysis complete
```

### What the Analysis Surfaces

After processing all historical reviews, agent generates a full restaurant intelligence report:

```
RESTAURANT REVIEW INTELLIGENCE REPORT
Based on 609 reviews across all platforms

OVERALL HEALTH
  Avg rating (all platforms):  4.5 ⭐
  Positive reviews:            78%
  Neutral reviews:             12%
  Negative reviews:            10%

WHAT GUESTS LOVE (most mentioned positives)
  1. Food quality / flavors         — mentioned in 68% of reviews
  2. Atmosphere / ambiance          — mentioned in 54% of reviews
  3. Specific dishes (salmon, lamb) — mentioned in 41% of reviews
  4. Friendly / warm staff          — mentioned in 38% of reviews
  5. Good value for money           — mentioned in 29% of reviews

WHAT GUESTS COMPLAIN ABOUT (most mentioned negatives)
  1. Wait times / slow service      — mentioned in 34% of negative reviews
  2. Inconsistency (varies by visit)— mentioned in 28% of negative reviews
  3. Parking / location             — mentioned in 22% of negative reviews
  4. Noise level                    — mentioned in 18% of negative reviews
  5. Limited vegetarian options     — mentioned in 14% of negative reviews

DISH MENTIONS (direct feedback from guests)
  Salmon dishes:     mentioned 142x — 96% positive
  Lamb dishes:        mentioned 88x — 91% positive
  Bread / appetizers: mentioned 64x — 84% positive
  Desserts:           mentioned 38x — 71% positive ← room to improve
  Pasta dishes:       mentioned 29x — 62% positive ← investigate

STAFF MENTIONS
  General praise for staff:   mentioned 186x — 89% positive
  Specific server praise:      mentioned 42x (learn from these)
  Service complaints:          mentioned 38x — linked to peak hours

PEAK COMPLAINT PERIODS
  Friday dinner:   highest complaint volume (28% of negatives)
  Saturday dinner: second highest (22% of negatives)
  Pattern: weekend dinner rush = service strain

UNANSWERED REVIEWS
  Google:      47 reviews with no owner response (oldest: 2021)
  Yelp:        31 reviews with no owner response
  Action: Generate responses for all unanswered — prioritize recent
```

### Ongoing Trend Analysis

After onboarding, agent tracks how sentiment evolves over time:

```
SENTIMENT TREND — Last 12 Months

           J   F   M   A   M   J   J   A   S   O   N   D
Positive  74% 76% 79% 81% 78% 82% --  --  --  --  --  --
Neutral   14% 13% 12% 11% 13% 10%
Negative  12% 11%  9%  8%  9%  8%

Trend: Improving ↑ — negative reviews declining over 6 months
Best month: June (82% positive)
Most improved: Food consistency complaints down 40% vs Jan
```

---

## Monitors Continuously
- New reviews posted across all platforms
- Overall rating trend on each platform (daily moving average)
- Sentiment of reviews: positive, neutral, negative
- Recurring topics and complaints across all reviews
- Review volume: are guests reviewing at all?
- Competitor ratings and review trends nearby
- Unanswered reviews that need responses

---

## Trigger Conditions

| Trigger | Condition | Action |
|---|---|---|
| Negative review | 1 or 2 star review posted | Draft response within 15 minutes, alert owner |
| Rating drop | Overall rating drops 0.1+ points in 7 days | Alert with contributing reviews identified |
| Complaint pattern | Same issue mentioned in 3+ reviews in 7 days | Operational alert — not just review alert |
| No reviews | Review volume drops significantly | Awareness alert — consider review generation push |
| Positive review | 4–5 star review posted | Notify owner, draft thank-you response |
| Known guest reviews | Reviewer linked to guest profile | Add to guest profile, personalize response |
| Staff complaint linked | Service complaint cross-referenced to shift | Alert manager with specific shift data |
| Competitor drop | Nearby competitor rating drops significantly | Opportunity flag |

---

## Review Response Drafting

### Negative Review Response
Within 15 minutes of a negative review posting:

```
NEW REVIEW ALERT — Google — 2 stars
Posted: June 14, 2026 11:48pm
Reviewer: John D.

"Waited 45 minutes for our food on a Thursday night.
The server seemed overwhelmed and we had to ask
twice for water. Food was good when it arrived but
the experience wasn't worth the price."

ANALYSIS
  Sentiment:      Negative
  Issues:         Wait time, service attentiveness, value perception
  Food quality:   Positive (noted food was good)
  Shift:          Thursday June 12, dinner service
  Cross-reference: Thursday Jun 12 had 2 servers for 38 covers
                  (Agent 3 flag: understaffed that evening)

DRAFT RESPONSE
  "John, thank you for taking the time to share your
  experience and we sincerely apologize for the wait
  and the service gaps you experienced. This is not
  the standard we hold ourselves to. You're right
  that Thursday evening was busier than expected and
  we fell short — that's on us, not you.

  We're glad the food hit the mark, and we'd love
  the opportunity to give you the full experience
  we're known for. Please reach out to us directly
  at [email] and we'll make it right.

  Thank you for your honesty — it helps us improve."

[Approve & Post] [Edit Response] [Flag for Manager]
```

### Positive Review Response

```
NEW REVIEW — Google — 5 stars
Posted: June 13, 2026
Reviewer: Sarah M.

"Best salmon I've had in Chicago. The service was
warm and attentive, and the atmosphere was perfect
for a date night. We'll definitely be back!"

DRAFT RESPONSE
  "Sarah, thank you so much — this made our day!
  Our salmon is one of our favorites to prepare and
  we're thrilled it hit the mark for you. We look
  forward to welcoming you back for date night soon!"

[Approve & Post] [Edit] [Skip]
```

---

## Complaint Pattern Detection

Agent looks across all reviews — not just individual ones:

```
PATTERN ALERT — Week of June 9–14, 2026

COMPLAINT: Slow service / long wait
  Mentioned in: 4 reviews this week
  Review dates: Jun 9, Jun 11, Jun 12, Jun 13
  All shifts: Thursday and Friday dinner

OPERATIONAL CROSS-REFERENCE
  Thursday Jun 12: 2 servers, 38 covers (Agent 3 — understaffed)
  Friday Jun 13: 3 servers, 42 covers (borderline capacity)

THIS IS NOT A REVIEW PROBLEM — THIS IS A STAFFING PROBLEM
Recommendation: Add 1 server to Thursday and Friday dinner shifts.
Alert sent to: Agent 3 (Labor & Scheduling)

Do not respond to reviews individually — fix the root cause first.
[Notify Manager] [Adjust Thursday/Friday Schedule]
```

---

## Rating Trend Tracking

```
RATING DASHBOARD — June 14, 2026

GOOGLE
  Current rating:     4.6 ⭐ (stable)
  Reviews this month: 12
  Trend (30 days):    ↔ stable
  Response rate:      91% ✓

YELP
  Current rating:     4.3 ⭐
  Reviews this month: 6
  Trend (30 days):    ↓ -0.1 from 4.4 (watch)
  Response rate:      67% — needs attention

TRIPADVISOR
  Current rating:     4.5 ⭐
  Reviews this month: 3
  Trend:              ↔ stable

OVERALL HEALTH:  Good ✓
Watch item:      Yelp trending slightly down — 3 service complaints
```

---

## Review Generation

Agent identifies the right moment to ask happy guests for a review — not blasting everyone, targeting guests most likely to leave a positive one:

```
REVIEW GENERATION — Weekly Candidates

Criteria for outreach:
  ✓ Visited in last 7 days
  ✓ Avg spend above $60 (engaged guest)
  ✓ No complaints noted on POS or survey
  ✓ Has not left a review before
  ✓ Not already contacted in last 60 days

This week's candidates: 14 guests

DRAFT MESSAGE (SMS, 24 hrs after visit)
  "Hi [Name], thank you for dining with us last night!
  We hope you had a wonderful experience. If you have
  a moment, we'd love it if you shared your thoughts
  on Google — it means the world to a small restaurant
  like ours. [Google review link]
  Thank you!"

[Approve & Send to 14 guests] [Preview List] [Edit Message]
```

---

## Competitive Monitoring

Agent watches nearby competitors to surface opportunities:

```
COMPETITIVE ALERT — June 12, 2026

Nearby restaurant: The Corner Bistro (0.4 miles)
Recent reviews: 3 complaints this week about "rude staff"
Rating drop: 4.2 → 4.0 in 14 days

Opportunity: Guests searching for alternatives in the area.
Consider: A targeted promotion this week to capture walk-in traffic.

[Note this] [Create Promotion] [Dismiss]
```

---

## Actions
- Monitor all review platforms every 15–60 minutes around the clock
- Draft responses to all new reviews within 15 minutes of posting
- Detect complaint patterns and escalate to operations (not just PR)
- Track rating trends and alert on drops
- Generate targeted review outreach to happy recent guests
- Link reviewers to guest profiles in Agent 12 where identifiable
- Monitor competitor ratings and flag opportunities
- Post approved responses to Google automatically; queue others for manual posting

---

## Outputs

### Real-Time (immediate)
- New negative review alert with draft response
- Rating drop alert
- Complaint pattern alert (escalated to operations)

### Daily (in morning briefing)
- New reviews overnight
- Current ratings across all platforms
- Any unanswered reviews needing attention

### Weekly
- Reputation health report: ratings, review volume, response rate
- Complaint pattern summary
- Review generation campaign results
- Competitive snapshot

### Monthly
- Full reputation report: rating trends, sentiment analysis, top complaints, top praise
- Response rate by platform
- Review generation ROI: outreach sent vs reviews received

---

## Key Metrics Tracked
- **Overall rating per platform** (daily)
- **Review volume per month** per platform
- **Response rate** — % of reviews that receive an owner response (target: 100%)
- **Response time** — average time from review posting to response
- **Sentiment ratio** — positive vs negative vs neutral
- **Complaint pattern recurrence** — is the same issue appearing again after being flagged?
- **Review generation conversion** — outreach sent vs reviews received

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 3 — Labor & Scheduling | Cross-references service complaints with shift staffing data |
| Agent 7 — Staff Performance | Cross-references service complaints with server assignments |
| Agent 12 — Guest Relationship | Links reviewers to guest profiles, adds review to guest history |

---

## Human Approval Required
- All review responses before posting
- Review generation campaign before sending
- Any public-facing communication decisions
- Decisions based on competitive intelligence
