# 2026-06-08 — Invoice Parser Build Complete (Except Email Trigger)

## Time: 00:17 CDT
## Status: Parser working, email blocked by Gmail auth

## What Was Accomplished

### Parser (9 Formats)
1. All 7 synthetic PDFs pass ✅
2. AT&T utility bill (real PDF from iCloud) passes ✅
3. Scanned/image PDF with OCR fallback passes ✅

### Infrastructure
- API server: localhost:9001 via launchd
- Cloudflare tunnel: invoices.systack.net
- Database: 119 records, backup saved
- OCR: Tesseract + pytesseract installed

### n8n
- Logged into n8n UI via API (plowe95@yahoo.com / 123GreeN23!)
- Created IMAP credential: xBT92arTjBY66ccE
- Updated workflow qnsBnLIWQ1Sky68D with IMAP credential
- Activation FAILED: Gmail app password revoked

### Key Lesson
- App passwords stored in keychain can be revoked by Google
- Always test auth credentials before assuming they work
- n8n cookie auth is more reliable than API keys

## Blocker
- Gmail app password `sacn gdyi nrqw otnx` for theutopiadelilittlerock@gmail.com is REVOKED
- Need new app password from Google account
- Cannot complete email trigger testing until this is fixed

## Next Steps
1. Generate new Gmail app password
2. Update n8n credential
3. Activate workflow
4. Test full email→parser flow
