# LinkedIn Post — June 8, 2026 (Combined/Final Version)

**Status:** Final draft — user reviewed and approved
**Platform:** LinkedIn (Systack business account)
**Tone:** Build day + pipeline pitch combined
**Length:** ~150 words (good for LinkedIn)
**Saved:** 2026-06-08 08:56 CDT

---

## Final Post Text

Today was a build/test/fix day.

Fixed the email-to-invoice pipeline:
→ IMAP trigger properly downloading PDF attachments
→ Binary data passing through n8n correctly
→ Parser API extracting vendor, items, totals
→ SQLite database logging everything
→ Postgres database created for future scaling

Also set up TablePlus for database management.

An email comes in with a PDF invoice. Before any human touches it:

→ PDF gets extracted automatically
→ Vendor name pulled out
→ Line items identified with prices
→ Total calculated
→ Everything saved to a database

No manual data entry. No copying and pasting. No "I'll get to it later."

Tested it end-to-end with a real invoice and it worked.

The pipeline:
1. Email arrives at dedicated address
2. IMAP trigger fires
3. PDF downloaded and parsed
4. Structured data extracted
5. SQLite database updated
6. Notification sent

Now I need 3 businesses willing to beta test this for 30 days free.

If you get invoices and hate data entry, DM me.

#InvoiceAutomation #SmallBusiness #AI #Automation #DataEntry #Systack
