# 2026-06-07 — Invoice Parser Made Bulletproof

## Result: ALL 7 test invoice formats now pass ✅

After multiple rounds of debugging, the invoice parser handles every format in the examples folder.

## Formats Working (7/7)

| Format | Vendor | Items | Total | Status |
|--------|--------|-------|-------|--------|
| FROM:/BILL TO: + ITEMS (NovaTech) | NovaTech Solutions Inc. | 4 | $5,078.72 | ✅ |
| Table/Column (real-invoice-test) | ABC Restaurant Supply Co. | 8 | $211.21 | ✅ |
| Contractor/Messy (Mike's Repairs) | Mike's Electrical Repairs | 3 | $826.20 | ✅ |
| International VAT (Shenzhen Apex) | Shenzhen Apex Manufacturing Ltd. | 3 | $2,587.70 | ✅ |
| POS Receipt (Utopia Deli) | THE UTOPIA DELI (FOOD TRUCK) | 4 | $21.55 | ✅ |
| Professional Services (Carter & Bloom) | Carter & Bloom Consulting | 2 | $3,700.00 | ✅ |
| Subscription/SaaS (NovaCloud) | NOVACLOUD SYSTEMS | 4 | $660.43 | ✅ |

## Key Fixes Applied

1. **Format detection reordered** — specific patterns (contractor, table) before general (FROM:/BILL TO:)
2. **Multi-line vendor** — when "From:" is empty, scan next lines for company name
3. **Double-price lines** — rsplit('$', 1) always gets rightmost price (total)
4. **Non-decimal values** — regex handles "765" not just "$765.00"
5. **Tax percentages** — split on "=" or ":" and take right side, not percentage
6. **Dotted items** — strip dots from "......$450" contractor format
7. **API response shape** — merge raw fields (vendor, items, success) with normalized (direction, ledger)

## Files Modified
- `invoice_parser_production.py` — detection logic + vendor fallback + item parsing
- `invoice_api.py` — response shape: raw fields at top level for frontend compatibility
- `INVOICE-PARSER-LESSONS.md` — full lessons learned document (9 lessons)

## Live Endpoints
- API: https://invoices.systack.net/extract ✅
- Demo: https://systack.net/services/invoice-extractor.html ✅
- Health: https://invoices.systack.net/health ✅
- n8n: Workflow `IqNgw6kgIkWVCLp5` (email trigger) ✅

## Database
- 670 invoices loaded (120 synthetic, 200 clean ML, 200 OCR, 150 fraud/layout)
- AR: $1.15M | AP: $935K | Net: +$215K
- 114 anomalies in review queue

## ORACLE Design (Saved)
- Bidirectional Invoice System: `INVOICE-TRAINING-DATASET.md`
- Full schema: `wiki/ORACLE-Bidirectional-Invoice-System.md`
- 3 training datasets from Copilot on disk
