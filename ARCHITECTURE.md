# Platform Architecture

## Vision

One platform that replaces the fragmented tool stack small and medium restaurants cobble together.
AI is not bolted on — it is the core of every module.

Restaurants connect their existing POS and payroll tools. Everything else lives here.

---

## What We Own vs What We Connect To

### Platform Owns
| Module | Replaces |
|---|---|
| Inventory + Recipe Costing | Xtrachef, MarketMan |
| Reservations + Waitlist | OpenTable, Resy |
| CRM + Loyalty | Toast Loyalty, Mailchimp |
| Menu Management | Scattered across tools |
| Bookkeeping + Accounting | QuickBooks (for restaurants without it) |
| Reviews Hub | Manual Yelp/Google checking |
| Staff Scheduling | 7shifts, Homebase |
| Asset Management | Spreadsheets |
| Tip Management | Manual tip sheets |
| Staff Performance Tracking | Manager gut feel |

### Platform Connects To (Read / Sync)
| System | Role |
|---|---|
| Toast / Square / Clover | POS — primary sales and transaction data source |
| QuickBooks Online | Accounting sync for restaurants that already have it |
| ADP / Gusto | Payroll data — read only |
| Bank accounts + cards | Cash flow via Plaid — read only |
| Google / Yelp / TripAdvisor | Review monitoring |
| DoorDash / UberEats / Grubhub | Delivery channel sales data |
| Vendor email inboxes | Invoice ingestion via OCR |

### Out of Scope
- POS replacement (Toast, Square stay)
- Bar / liquor inventory management
- Payroll processing (ADP, Gusto handle compliance and filing)

---

## Architecture Diagram

```
                    ┌─────────────────────┐
                    │   OWNER DASHBOARD   │
                    │   Morning Brief     │
                    │   Alerts + Actions  │
                    └──────────┬──────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
   OPERATIONS             FINANCIAL          GUEST & REPUTATION
   ├─ Inventory Guardian  ├─ Bookkeeping &   ├─ Guest Relationship
   ├─ Asset Agent         │  Accounting      │  Manager (CRM)
   ├─ Labor &             │  (Chart of       ├─ Reputation Monitor
   │  Scheduling          │  Accounts, Bank  ├─ Loyalty Program
   ├─ Menu Performance    │  Transactions,   └─ Reservations
   ├─ Vendor &            │  P&L, Balance         Module
   │  Procurement         │  Sheet)
   ├─ Vendor Intel &      ├─ Financial
   │  Alt. Sourcing       │  Watchdog
   └─ Staff Performance   ├─ Invoice
                          │  Processing
                          └─ Tips
                             Management
        │                      │                      │
        └──────────────────────┼──────────────────────┘
                               │
                    ┌──────────┴──────────┐
                    │   CORE DATA LAYER   │
                    │   Single source     │
                    │   of truth          │
                    └──────────┬──────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
  Toast / Square /      QuickBooks + Bank      Google / Yelp /
  Clover (POS)          Plaid / Vendor         TripAdvisor
  ADP / Gusto           Invoices / OCR         OpenTable / Resy
  DoorDash / UberEats   Native Financials      Reservations Data
```

---

## The 13 Agents

### OPERATIONS PILLAR
| # | Agent | Core Responsibility |
|---|---|---|
| 1 | Inventory Guardian | Stock levels, burn rate, purchase orders, waste tracking |
| 2 | Asset Intelligence | Equipment, furniture, depreciation, maintenance schedules |
| 3 | Labor & Scheduling | Staffing vs sales forecast, labor cost %, overtime alerts |
| 4 | Menu Performance | Profitability per item, menu engineering, price optimization |
| 5 | Vendor & Procurement | Purchase orders, delivery tracking, vendor management |
| 6 | Vendor Intelligence & Alt. Sourcing | Price monitoring, alternative vendor search, market benchmarking |
| 7 | Staff Performance | Revenue per server, upsell rates, voids, training flags |

### FINANCIAL PILLAR
| # | Agent | Core Responsibility |
|---|---|---|
| 8 | Bookkeeping & Accounting | Chart of accounts, bank transactions, P&L, balance sheet, cash flow |
| 9 | Financial Watchdog | Daily P&L flash, food cost %, cash flow forecast, anomaly detection |
| 10 | Invoice Processing | OCR invoice capture, QB coding, three-way match, payment tracking |
| 11 | Tips Management | Tip distribution, payroll sync, IRS compliance |

### GUEST & REPUTATION PILLAR
| # | Agent | Core Responsibility |
|---|---|---|
| 12 | Guest Relationship Manager | Guest profiles, visit tracking, lapsed guest detection, campaigns |
| 13 | Reputation Monitor | Review monitoring across all platforms, response drafting, pattern detection |

---

## Core Data Layer

Single source of truth for all modules. No agent reads directly from an external API — all data flows through the core layer first.

### Central Entities
```
Restaurant (tenant)
    ├── Menu Items
    ├── Recipes (items → ingredients mapping)
    ├── Inventory (ingredients + assets)
    ├── Guests (unified profile)
    │     ├── Visits (from POS)
    │     ├── Reservations
    │     └── Reviews written
    ├── Orders / Transactions (from POS)
    ├── Reservations
    ├── Staff / Shifts
    ├── Vendors / Suppliers
    ├── Invoices / Bills
    └── Financial Transactions
```

### Storage
| Data Type | Technology | Reason |
|---|---|---|
| Core operational data | PostgreSQL | Relational, multi-tenant, reliable |
| Sales + inventory timeseries | TimescaleDB | Fast time-based queries |
| Reviews + menu text + notes | PostgreSQL JSONB | Flexible document storage |
| Semantic search (reviews, guest notes) | pgvector | AI-powered similarity search |
| Analytics + reporting | ClickHouse | Fast aggregations across history |

---

## Ingestion Layer

How data enters the platform from external systems:

| Source | Method | Frequency |
|---|---|---|
| Toast / Square | Webhooks + REST API | Real-time |
| QuickBooks | API + Webhooks | Real-time |
| ADP / Gusto | API polling | Daily |
| Bank / Cards | Plaid API | Daily |
| Google Reviews | Google My Business API | Every 15 min |
| Yelp | Yelp Fusion API | Every 15 min |
| TripAdvisor | Scraping | Hourly |
| DoorDash / UberEats | Merchant API | Real-time |
| Vendor Invoices | Email monitoring + OCR | On receipt |

---

## AI Intelligence Layer

### Event-Driven (Real-Time Triggers)
Fires immediately when something happens:
```
New review posted → sentiment analysis → draft response → owner approval queue
Order closes on POS → inventory burn updated → food cost % recalculated
Invoice received → OCR extracts data → QB bill created → approval queue
Regular guest not visited in 6 weeks → re-engagement campaign drafted
```

### Scheduled (Batch Intelligence)
Runs on a fixed schedule regardless of events:
```
7am daily     → Morning briefing compiled from all agents
Weekly Monday → Schedule recommendation, vendor POs, menu engineering update
Monthly 1st   → Full P&L, balance sheet, cash flow, vendor scorecards
Quarterly     → Tax estimate, asset depreciation summary, license renewal check
```

---

## Multi-Tenancy

Every restaurant is an isolated tenant. Adding a new restaurant is configuration, not custom code.

```
Restaurant Config:
  - POS: Toast | Square | Clover
  - Accounting: QuickBooks | Native (platform)
  - Payroll: ADP | Gusto
  - Modules enabled: [list of active agents]
  - Alert thresholds: food_cost_pct > 32, labor_cost_pct > 28
  - Timezone: America/Chicago
  - Currency: USD
```

---

## Delivery Layer

How the owner interacts with the platform:

| Channel | Purpose |
|---|---|
| Web dashboard | Primary interface, real-time data, all modules |
| Mobile app | Approvals on the go, urgent alerts, morning brief |
| Daily SMS / email | One morning briefing, key numbers + actions needed |
| Excel export | For accountants, old-school owners |
| Accountant portal | Read-only CPA access, year-end export |

---

## Key Design Principles

1. **Read-first always** — never write to POS; never write to QB without human approval
2. **One source of truth** — all agents read from the core data layer, not raw external APIs
3. **Event-driven for urgent** — reviews, stockouts, cash anomalies trigger immediately
4. **Scheduled for analytics** — P&L, trends, reports run on a fixed cadence
5. **AI suggests, human approves** — all financial writes and guest communications require owner sign-off
6. **Single owner experience** — 13 agents, one morning briefing, minimal noise
7. **Configuration not code** — onboarding a new restaurant requires no custom development
