# 2026-06-08 — Postgres Status Investigation

## Summary

**Postgres IS running** on localhost:5432, but the n8n credential is misconfigured.

## What I Found

### Postgres Status
- **Running:** YES
- **Port:** 5432 (accepting connections)
- **Process:** `/opt/homebrew/opt/postgresql@15/bin/postgres`
- **Owner:** philliplowe

### Databases
| Database | Owner | Status |
|----------|-------|--------|
| postgres | philliplowe | System |
| template0 | philliplowe | System |
| template1 | philliplowe | System |
| utopia_deli | philliplowe | Active |
| **crm** | **—** | **MISSING** |

### Users
| User | Status |
|------|--------|
| philliplowe | ✅ Exists (superuser) |
| **systack** | **❌ MISSING** |

## Why the Invoice Pipeline Failed on Postgres

The n8n workflow node "Log to Postgres" tried to connect with:
- **Database:** `crm` ❌ Doesn't exist
- **User:** `systack` ❌ Doesn't exist

But Postgres actually has:
- **Database:** `utopia_deli` ✅ (or `postgres`)
- **User:** `philliplowe` ✅

## Options to Fix

### Option 1: Create Missing Database/User (Recommended)
```sql
CREATE USER systack WITH PASSWORD 'your_password';
CREATE DATABASE crm OWNER systack;
GRANT ALL PRIVILEGES ON DATABASE crm TO systack;
```

### Option 2: Update n8n Credential
Change the Postgres credential to use:
- **Database:** `utopia_deli` (or `postgres`)
- **User:** `philliplowe`

### Option 3: Use SQLite (Current Working Solution)
The parser API already saves to SQLite (`invoice_data.db`). No need for Postgres in the n8n workflow.

## Current Pipeline Status

The simplified workflow (without Postgres) is **working**:
1. ✅ IMAP trigger fires
2. ✅ PDF downloaded
3. ✅ Parser API extracts data
4. ✅ SQLite saves everything
5. ❌ Email notification (Gmail app password revoked)

## Recommendation

Since SQLite handles all data storage and the pipeline works without Postgres:
1. **Keep SQLite** for now — it's simpler and works
2. **Optionally fix Postgres** for future use if you need multi-user concurrent access
3. **Regenerate Gmail app password** to restore email notifications

---

**Investigated:** 2026-06-08 08:12 CDT
**Status:** Postgres running, misconfigured credential
**Solution:** Use SQLite (working) OR fix Postgres config
