# 2026-06-16 — Invoice Web Dashboard Built + Auto-Release Deployed

**Date:** 2026-06-16 ~03:00 CDT  
**Session:** SOL  
**Status:** Two major builds completed

---

## Build 1: T-30min Auto-Release Workflow ✅

**Workflow ID:** `KSmNNiADmPDOwJq0`  
**Status:** ✅ Active (every 5 minutes)  
**File:** `n8n-workflows/systack-auto-release-unconfirmed.json`

### What It Does
- Runs every 5 minutes
- Queries `systack_noshow.bookings` for unconfirmed appointments within 30 minutes
- Marks `status = 'released'` and sets `released_at`
- Notifies customer via HTTP webhook

### DB Changes
```sql
ALTER TABLE bookings ADD COLUMN release_notified BOOLEAN DEFAULT FALSE;
ALTER TABLE bookings ADD COLUMN released_at TIMESTAMP WITH TIME ZONE;
```

### Complete No-Show Chain Now:
1. Booking created → DB + confirmation email ✅
2. Customer confirms → `confirmed=TRUE` ✅
3. T-24h reminder → friendly email ✅
4. T-2h reminder → urgent email ✅
5. **T-30min auto-release → check + release if unconfirmed** 🆕 ✅

---

## Build 2: Invoice Pipeline Web Dashboard ✅

### Files Created
| File | Purpose |
|------|---------|
| `invoice_dashboard_api.py` | REST API serving SQLite data (port 8766) |
| `invoice-dashboard.html` | Single-page web dashboard (served on port 8768) |

### API Endpoints
| Endpoint | Description |
|----------|-------------|
| `/api/summary` | Dashboard metrics (AR/AP/net/review/weekly) |
| `/api/invoices` | Paginated list with search, status filter, sorting |
| `/api/invoices/{id}` | Full detail with line items, normalized data |
| `/api/aging` | AR aging report (current/30-60/60-90/90+) |
| `/api/vendors` | Vendor directory with stats |
| `/api/export/csv` | CSV download of all invoices |
| `/health` | Health check |

### Dashboard Features
- **3 tabs:** Invoices, Aging Report, Vendors
- **Search:** Vendor, invoice #, file name
- **Filter:** By payment status (Paid/Unpaid)
- **Sort:** Click any column header
- **Detail modal:** Click any row for full invoice detail with line items
- **CSV export:** One-click download
- **Auto-refresh:** Summary metrics refresh every 60 seconds
- **SyStack branded:** Navy/cyan palette, professional design

### Data
- 95 invoices in SQLite
- 650 normalized records
- AR: $1,174,555.38 (344 invoices)
- AP: $951,819.74 (326 invoices)
- Net position: +$222,735.64
- 114 pending review

### How to Access
- **Dashboard:** `http://localhost:8768/invoice-dashboard.html`
- **API:** `http://localhost:8766`
- Both servers running in background

---

## Updated Priority List

| # | Task | Status | Next |
|---|------|--------|------|
| 1 | T-30min auto-release | ✅ DONE | — |
| 2 | Invoice web dashboard | ✅ DONE | — |
| 3 | Find 3 beta testers | ⏳ NEXT | Post in Little Rock business groups |
| 4 | Set up Stripe billing | ⏳ After #3 | Create products in Stripe |
| 5 | QuickBooks/Xero export | ⏳ After #4 | CSV first, API later |
| 6 | Smart Rebooking Engine | 📋 Queued | Phase 1 build |
| 7 | Review System | 📋 Queued | Phase 1 build |

---

**Saved:** 2026-06-16 ~03:00 CDT  
**Builder:** SOL