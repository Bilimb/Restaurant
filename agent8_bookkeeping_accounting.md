# Agent 8 — Bookkeeping & Accounting

## Role
Long-running agent that handles all financial recordkeeping for the restaurant. Captures every dollar in and out — from POS sales to vendor invoices to Costco receipts to payroll entries — categorizes everything automatically, and produces accurate financial statements at any time. For restaurants without QuickBooks, this is the complete accounting system. For restaurants that have QuickBooks, this syncs everything to it seamlessly.

---

## This Platform IS the Accounting System

The platform is the primary bookkeeping and accounting system — not a connector to QuickBooks. Most small restaurants do not have QuickBooks, and they do not need it. Everything lives here: chart of accounts, transactions, reconciliation, financial statements, tax reports.

For restaurants that already have QuickBooks and want to keep it for their accountant, an optional sync is available. But QuickBooks is never required — it is an add-on, not a dependency.

| Situation | How It Works |
|---|---|
| No QuickBooks (most small restaurants) | Platform is the full accounting system. CPA gets read-only access and year-end export directly from here. |
| Has QuickBooks | Platform does all the work, syncs to QB automatically so accountant's workflow is unchanged. |

The owner experience is identical in both cases.

---

## Data Sources
| Source | What It Reads |
|---|---|
| POS (Toast / Square) | Daily sales, payment types (cash, card, delivery), voids, comps, refunds |
| Plaid (bank feeds) | All bank account and credit card transactions daily |
| Agent 9 — Invoice Processing | Vendor bills coded and ready for approval |
| Agent 3 — Labor & Scheduling | Payroll entries from ADP / Gusto |
| Agent 2 — Asset Intelligence | Monthly depreciation entries |
| Agent 11 — Tips Management | Tip payouts per payroll period |
| Receipt uploads (platform) | Store visit receipts photographed by staff |
| Reservations (platform) | Catering deposits and event payments |

---

## Chart of Accounts

Pre-built for restaurants. No setup required — owner can customize but starts with a complete, correct structure from day one.

```
INCOME
  4000  Food Sales
  4010  Beverage Sales — Alcohol
  4020  Beverage Sales — Non-Alcohol
  4030  Catering & Private Events
  4040  Delivery Sales (DoorDash, UberEats, Grubhub)
  4050  Gift Card Redemptions

COST OF GOODS SOLD
  5000  Food Cost
  5010  Beverage Cost
  5020  Packaging & Disposables

LABOR
  6000  FOH Wages (servers, hosts, runners)
  6010  BOH Wages (kitchen, prep, dishwashers)
  6020  Management Salaries
  6030  Payroll Taxes (employer share)
  6040  Employee Benefits

OCCUPANCY
  6100  Rent
  6110  Common Area Maintenance (CAM)
  6120  Utilities — Electric
  6130  Utilities — Gas
  6140  Utilities — Water

OPERATING EXPENSES
  6200  Credit Card Processing Fees
  6210  Repairs & Maintenance
  6220  Cleaning & Sanitation Supplies
  6230  Small Equipment & Smallwares
  6240  Uniforms & Linen
  6250  Marketing & Advertising
  6260  Software & Platform Subscriptions
  6270  Office Supplies
  6280  Professional Services (accountant, legal)
  6290  Insurance
  6300  Licenses & Permits
  6310  Telephone & Internet
  6320  Delivery Platform Commissions
  6330  Merchant Services Fees
  6340  Miscellaneous

ASSETS
  1000  Checking Account
  1010  Savings Account
  1020  Petty Cash
  1100  Accounts Receivable (catering net-terms)
  1200  Inventory (at cost)
  1500  Equipment (gross)
  1510  Accumulated Depreciation — Equipment
  1520  Furniture & Fixtures (gross)
  1530  Accumulated Depreciation — Furniture
  1600  Leasehold Improvements (gross)
  1610  Accumulated Depreciation — Leasehold

LIABILITIES
  2000  Accounts Payable (vendor bills)
  2100  Sales Tax Payable
  2110  Illinois Sales Tax Payable
  2120  Chicago City Tax Payable
  2200  Payroll Tax Payable
  2300  Gift Card Liability
  2400  Equipment Loan
  2500  Line of Credit

EQUITY
  3000  Owner Equity
  3010  Owner Draws
  3900  Retained Earnings
```

---

## Bank Transaction Control

All bank accounts and credit cards connected via Plaid. Every transaction captured and auto-categorized.

### Auto-Categorization (AI-Powered)

Agent learns from the restaurant's own transaction history and categorizes automatically:

```
TRANSACTION FEED — June 14, 2026

✓ Sysco Chicago         -$1,840.00   → 5000 Food Cost
✓ ComEd                   -$312.40   → 6120 Utilities — Electric
✓ ADP Payroll           -$8,420.00   → 6000/6010 Labor (split FOH/BOH)
✓ Square deposit        +$3,840.00   → 4000 Food Sales
✓ DoorDash payout       +$1,240.00   → 4040 Delivery Sales
✓ Costco                  -$438.20   → 5000 Food Cost (receipt required)
? Amazon charge           -$89.00    → Uncategorized (needs review)
? Wire transfer         -$2,500.00   → Uncategorized — large, flag for review
```

- Green checkmark: auto-categorized with high confidence
- Question mark: needs owner or manager to assign category
- Once categorized manually, agent remembers for future identical transactions

### Receipt Capture for Store Visits

For cash or card purchases at Costco, Restaurant Depot, local grocery, or any store — staff photographs the receipt on their phone:

```
RECEIPT CAPTURE
  [Photo uploaded by: James, Kitchen Manager]
  [Costco — June 14, 2026 — $438.20]

  Agent extracted:
    Olive oil 3L x4      $87.96  → Food Cost
    Paper towels x2      $42.80  → Cleaning Supplies
    Chicken thighs 20lb  $59.00  → Food Cost
    Trash bags            $28.40  → Cleaning Supplies
    Mixed items          $220.04  → Food Cost (bulk)

  Total matched: $438.20 ✓
  [Approve] [Edit line items]
```

Agent splits the receipt across multiple expense categories automatically based on item types. Staff do not need to categorize — just photograph and submit.

---

## Financial Statements

Generated automatically at any time. No manual work required.

### Daily P&L Flash
```
DAILY P&L — Friday June 13, 2026

REVENUE
  Dine-in sales:        $3,840
  Delivery sales:         $640
  Total Revenue:        $4,480

COST OF GOODS
  Food cost:            $1,394  (31.1%) ✓
  Beverage cost:          $182   (4.1%)
  Total COGS:           $1,576  (35.2%)

  Gross Profit:         $2,904  (64.8%)

LABOR
  FOH wages:              $820
  BOH wages:              $640
  Total Labor:          $1,460  (32.6%)

  Prime Cost:           $3,036  (67.8%) ✓

OTHER EXPENSES (estimated daily allocation)
  Rent:                   $283
  Utilities:               $70
  Processing fees:         $90
  Other:                   $85
  Total Other:            $528

  NET INCOME:           $916    (20.4%)
```

### Monthly P&L
Full income statement generated on the 1st of each month for the prior month. Includes year-to-date column and prior year comparison where data exists.

### Balance Sheet
Generated monthly and on demand. Asset values pulled from Agent 2 (depreciation), liabilities from accounts payable and loan balances, equity calculated automatically.

### Cash Flow Statement
Generated monthly. Operating, investing, and financing activities broken out clearly for the owner and CPA.

---

## Accounts Payable

Every vendor bill processed by Agent 9 (Invoice Processing) lands here for approval:

```
BILLS PENDING APPROVAL — June 14, 2026

Sysco Chicago        $1,840.00   due Jun 28   [Approve] [Hold] [Dispute]
Main Street Foods      $340.00   due Jun 21   [Approve] [Hold] [Dispute]
ComEd                  $312.40   due Jun 30   [Approve] [Hold] [Dispute]

Total pending:       $2,492.40
Cash available:     $28,400.00  (11.4x coverage ✓)
```

---

## Sales Tax Tracking

Automatically calculated and tracked — no manual work:

- Every POS sale tagged as taxable or non-taxable (food vs alcohol vs non-taxable items)
- Illinois state tax (6.25%), Chicago city tax (1.25%), Cook County tax (1.75%), other local rates applied automatically
- Monthly filing report generated with amounts owed by jurisdiction
- Reminder sent before filing deadline

---

## Payroll Entries

When ADP or Gusto runs payroll, agent records the entries automatically:

```
PAYROLL ENTRY — Week ending June 14, 2026

Gross wages:         $8,420.00
  FOH wages:         $3,840.00  → 6000
  BOH wages:         $3,180.00  → 6010
  Management:        $1,400.00  → 6020

Employer taxes:        $644.13  → 6030
  Social Security:     $522.04
  Medicare:            $122.09

Net payroll:         $7,012.40  (cash out of bank)

[Posted to books ✓]
```

---

## Accountant Access

Every restaurant still has a CPA for tax filing. The platform supports them:

- **Read-only portal** — CPA logs in, sees all transactions, reports, and statements
- **Year-end export** — full P&L, balance sheet, all transactions as Excel or CSV
- **QuickBooks export** — full data export in QB format if restaurant ever migrates
- **Tax summary packet** — one PDF with everything the CPA needs for annual filing

---

## Outputs

### Daily
- P&L flash (in morning briefing)
- Uncategorized transactions flagged for review
- Bills pending approval

### Monthly
- Full P&L with YTD and prior year comparison
- Balance sheet
- Cash flow statement
- Sales tax filing report with deadline reminder
- Accounts payable aging report

### Quarterly
- Estimated income tax calculation
- Payroll tax summary (941 data)

### Annually
- Full year financial package for CPA
- Year-over-year comparison report
- Asset register with book values (from Agent 2)

---

## Key Metrics Tracked
- **Food cost %** (target: under 32%)
- **Labor cost %** (target: under 30%)
- **Prime cost %** (food + labor, target: under 62%)
- **Net profit margin %**
- **Cash runway** (days of operating expenses covered by current cash)
- **Accounts payable aging** (anything overdue)
- **Revenue per day / week / month** trending

---

## Dependencies
| Agent | Relationship |
|---|---|
| Agent 2 — Asset Intelligence | Receives monthly depreciation entries |
| Agent 3 — Labor & Scheduling | Receives payroll cost entries per period |
| Agent 5 — Vendor & Procurement | Receives purchase orders for AP matching |
| Agent 9 — Invoice Processing | Receives coded vendor bills for approval |
| Agent 10 — Tips Management | Receives tip payout entries per payroll period |
| Agent 11 — Financial Watchdog | Provides all financial data for daily P&L and alerts |

---

## Human Approval Required
- All vendor bill payments
- Journal entries above threshold
- Manual category overrides on transactions
- Any balance sheet adjustments
- Year-end closing entries
