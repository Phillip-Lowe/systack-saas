# PLAN-n8n-audit-cleanup-2026-05-20

## Bug Fix: Constraint Evaluator Backend

**Status:** DONE ✅

**Completed:** 2026-05-20 06:?? CDT

---

## Issues Fixed

### 1. sqlite3.OperationalError: no such table: invoices
- **Root cause:** `TestDatabase.setUp` reassigned `DB_PATH` in the test module namespace, but `init_db()` reads `constraint_evaluator.DB_PATH` (the module's global), so the test DB was never initialized.
- **Fix:**
  - Added `import constraint_evaluator` to test file
  - Changed `setUp`/`tearDown` to modify `constraint_evaluator.DB_PATH` directly
  - Changed `init_db()` to accept optional `db_path` parameter
  - Updated `test_db_insert_and_query` to connect via `constraint_evaluator.DB_PATH`

### 2. AssertionError: 'INVOICE' != 'Acme Corporation'
- **Root cause:** Vendor regex patterns used `\s` in character classes, which matched newlines, causing multi-line matches starting from "INVOICE" header. The alternation `Corp\.?` also matched "Corp" inside "Corporation", truncating the vendor name.
- **Fix:**
  - Replaced `\s` with literal space in vendor pattern capture groups to prevent multi-line matching
  - Added "Corporation" before "Corp" in the alternation so full word is matched
  - Added skip filter for common header words (INVOICE, BILL, etc.) as safety net

---

## Verification

- All 11 tests pass ✅
- Server starts successfully ✅
- `GET /health` returns `{"status": "ok", "port": 9002}` ✅

## Files Modified

- `~/Documents/SOL-System/03-Code/constraint-eval/constraint_evaluator.py`
- `~/Documents/SOL-System/03-Code/constraint-eval/test_evaluator.py`
