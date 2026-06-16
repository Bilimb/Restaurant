# Agent 10 — Invoice Processing

## Role
Long-running agent that captures every vendor bill the restaurant receives — whether it arrives by email, PDF, text message, WhatsApp, or photographed paper invoice — extracts the data automatically, matches it against what was ordered and received, codes it to the right expense category, and sends it to Agent 8 for payment approval. No manual data entry. No lost invoices. No paying for things that were not delivered.

---

## Data Sources
| Source | What It Reads |
|---|---|
| Dedicated invoice email inbox | PDF and image attachments from vendors |
| Mobile app (staff upload) | Photos of paper invoices, delivery receipts, store receipts |
| WhatsApp / SMS integration | Invoice photos sent by drivers or vendors via messaging |
| Vendor portals (where available) | Auto-downloaded invoices from Sysco, US Foods portals |
| Agent 5 — Vendor & Procurement | Purchase orders to match against invoices |
| Agent 5 receiving logs | What was confirmed as received at delivery |

---

## Invoice Sources Handled

Small restaurants receive invoices in every format imaginable. Agent handles all of them:

| Source | Format | How Captured |
|---|---|---|
| Sysco, US Foods | PDF email attachment | Auto-monitored inbox |
| Local vendors | Paper invoice left with delivery | Staff photos via mobile app |
| Drivers via text | Photo of handwritten invoice | WhatsApp / SMS integration |
| Vendor portals | PDF download | Auto-download where portal allows |
| Costco / Restaurant Depot | Receipt at register | Staff photo via mobile app |
| Email invoices | Inline email or PDF | Auto-monitored inbox |
| Handwritten invoices | Paper, any format | Staff photo → OCR |

---

## Monitors Continuously
- Dedicated invoice email inbox for new attachments
- Expected invoices from scheduled deliveries not yet received
- Open purchase orders awaiting invoice match
- Bills approaching payment due date
- Duplicate invoice patterns across vendors
- Invoices where price differs from purchase order

---

## Invoice Processing Workflow

```
STEP 1 — CAPTURE
  Invoice arrives via any channel (email, photo, portal)
  Agent detects and queues for processing

STEP 2 — EXTRACT (OCR + AI)
  Agent reads the invoice and extracts:
    - Vendor name
    - Invoice number
    - Invoice date
    - Due date / payment terms
    - Line items (description, quantity, unit price)
    - Total amount
    - Tax (if applicable)

STEP 3 — MATCH
  Agent attempts three-way match:
    Purchase Order → Receiving Log → Invoice
  If all three match: auto-codes and sends to approval
  If discrepancy found: flags for review before approval

STEP 4 — CODE
  Agent assigns expense category from Chart of Accounts:
    Sysco food items     → 5000 Food Cost
    Cleaning supplies    → 6220 Cleaning & Sanitation
    Paper goods          → 6220 Cleaning & Sanitation
    Equipment repair     → 6210 Repairs & Maintenance
  Line items split across multiple categories where needed

STEP 5 — SEND FOR APPROVAL
  Coded bill sent to Agent 8 (Bookkeeping)
  Owner sees: vendor, amount, due date, category, [Approve] [Hold] [Dispute]

STEP 6 — RECORD
  After approval, bill recorded in accounts payable
  Payment scheduled based on due date and cash position
```

---

## Three-Way Match

The most important control — ensures the restaurant never pays for something it did not order and did not receive:

```
THREE-WAY MATCH — Sysco Invoice #SYS-8821

PURCHASE ORDER (what was ordered)
  Chicken thighs         25 lbs    @ $3.40/lb    $85.00
  Ground beef            15 lbs    @ $4.20/lb    $63.00
  Heavy cream             6 qt     @ $4.80/qt    $28.80
  PO Total:                                     $176.80

RECEIVING LOG (what arrived — logged by staff)
  Chicken thighs         25 lbs    ✓
  Ground beef            13 lbs    ⚠ short by 2 lbs
  Heavy cream             6 qt     ✓

INVOICE (what vendor is charging)
  Chicken thighs         25 lbs    @ $3.40/lb    $85.00
  Ground beef            15 lbs    @ $4.20/lb    $63.00  ⚠
  Heavy cream             6 qt     @ $4.80/qt    $28.80
  Invoice Total:                                $176.80

DISCREPANCY DETECTED
  Ground beef: received 13 lbs, charged for 15 lbs
  Overcharge: $8.40

Action: Flag for dispute before payment. Draft credit request to Sysco.
[Dispute $8.40] [Approve Full Amount] [Hold Invoice]
```

---

## OCR Extraction Example

When a staff member photographs a handwritten or paper invoice:

```
INVOICE RECEIVED — Photo upload by: James (Kitchen Manager)
Source: Local produce vendor, paper invoice

OCR EXTRACTED:
  Vendor:         Chicago Fresh Farms
  Date:           June 14, 2026
  Invoice #:      CFR-2241

  Tomatoes        4 cases    $14.00/case    $56.00
  Zucchini        2 cases    $18.00/case    $36.00
  Romaine hearts  3 cases    $16.00/case    $48.00
  Basil (fresh)   2 lbs       $8.00/lb      $16.00

  Total:                                   $156.00

Confidence: 94% ✓
Matched to PO #PO-0441 ✓
Category: 5000 Food Cost ✓

[Confirm & Send for Approval] [Edit] [Reject]
```

---

## Duplicate Detection

Agent catches duplicate invoices before they are paid:

```
DUPLICATE ALERT — June 14, 2026

Invoice received: Sysco #SYS-8821  $176.80  Jun 14
Possible duplicate: Sysco #SYS-8821 was already processed Jun 12

Same invoice number and amount. Possible duplicate submission.
Do NOT approve until verified with vendor.

[Mark as Duplicate] [It's Different — Process] [Contact Vendor]
```

---

## Missing Invoice Chase

When a delivery was received but no invoice has arrived after 48 hours, agent follows up:

```
MISSING INVOICE ALERT — June 16, 2026

Delivery received: Main Street Foods, June 14
Receiving log confirmed: $340 worth of goods received
Invoice expected: not received after 48 hours

Draft email to vendor:
  To: orders@mainstreetfoods.com
  Subject: Invoice needed — delivery June 14

  Hi, we received our delivery on June 14 but have not
  yet received the invoice. Could you please send it at
  your earliest convenience? Thank you.

[Send Email] [Mark as Received] [Dismiss]
```

---

## Payment Scheduling

Once invoices are approved, agent tracks payment due dates and alerts before they are late:

```
PAYMENT SCHEDULE — Week of June 16, 2026

DUE MONDAY Jun 16
  Main Street Foods     $340.00    Net 7     [Pay Now]

DUE WEDNESDAY Jun 18
  ComEd                 $312.40    Due date  [Pay Now]

DUE FRIDAY Jun 20
  Chicago Fresh Farms   $156.00    Net 7     [Pay Now]

DUE NEXT WEEK
  Sysco Chicago       $1,840.00    Net 30 (due Jun 28)

Total due this week:    $808.40
Cash available:       $43,400.00  ✓ no cash concern
```

---

## Actions
- Monitor invoice email inbox continuously
- Process OCR extraction on all invoice formats
- Run three-way match on every invoice against PO and receiving log
- Flag discrepancies before payment with draft dispute messages
- Detect and block duplicate invoices
- Chase missing invoices after 48 hours automatically
- Code all invoices to correct expense categories
- Send approved bills to Agent 8 for accounts payable recording
- Maintain payment schedule with due date alerts

---

## Outputs

### Real-Time (as invoices arrive)
- Extracted invoice data for review and approval
- Three-way match result with any discrepancies highlighted
- Duplicate alerts

### Daily
- Bills approved and pending payment
- Invoices in queue awaiting review
- Missing invoice chase log

### Weekly
- Total invoices processed
- Discrepancies found and resolved
- Credits received from vendors
- Payment schedule for upcoming week

### Monthly
- Invoice processing summary: volume, total spend, discrepancies
- Vendor accuracy scorecard (feeds Agent 5 and Agent 6)
- Accounts payable aging report

---

## Key Metrics Tracked
- **Invoice processing time** — from receipt to approval queue
- **Three-way match rate** — % of invoices matching PO and receiving log cleanly
- **Discrepancy rate per vendor** — how often each vendor overbills or short-delivers
- **Duplicate invoices caught** — $ value protected
- **Missing invoices chased** — response rate from vendors
- **On-time payment rate** — % of bills paid by due date

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 5 — Vendor & Procurement | Receives PO data and receiving logs for three-way match |
| Agent 6 — Vendor Intelligence | Sends invoice price data for price benchmarking |
| Agent 8 — Bookkeeping & Accounting | Sends coded bills for accounts payable recording |
| Agent 2 — Asset Intelligence | Flags equipment purchases for asset registration |

---

## Human Approval Required
- Every invoice before payment — no auto-pay
- Dispute decisions on discrepancies
- Missing invoice chase emails (drafted by agent, sent by owner or manager)
- Any invoice above a configurable high-value threshold gets additional review flag
