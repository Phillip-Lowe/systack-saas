# Session — 2026-06-22 ~10:00 CDT — Utopia Deli Email Campaign Complete

## Summary
Built complete weekly email campaign system for The Utopia Deli in Phillip's exact preferred format.

## What Was Built

**Master file:** `email-campaign/utopia-deli-all-days.js`
- Single n8n Function node with all 7 days
- Phillip's exact format: `images[day]` arrays, `templates[day]` lookup, same HTML structure
- Updated schedule: Meal prep closes Wed noon, reopens Thu 8 PM, pickup Thu 12:30-7:30
- Changed all "Walk up" → "Order online"

**7 daily emails:**
| Day | Subject | Focus |
|-----|---------|-------|
| Mon | 🍱 Meal Prep is Open | Opens window |
| Tue | 🎉 Planning an Event? | Catering |
| Wed | ⏰ Closes Today at Noon | Final hours |
| Thu | 🍱 Meal Prep Reopens at 8PM | Reopen + order online |
| Fri | Weekend at Utopia 🍽️ | Weekend kickoff |
| Sat | 🙌 We're Open Today! | Day-of reminder |
| Sun | 📋 This Week's Menu + Monday Lunch | Preview + lunch |

## Files Created/Updated

| File | Status |
|------|--------|
| `email-campaign/utopia-deli-all-days.js` | ✅ NEW — master file (copy into n8n) |
| `email-campaign/utopia-deli-weekly-email-campaign.json` | ✅ Updated combined workflow |
| `email-campaign/PRODUCTION-NOTES.md` | ✅ NEW — issues + weekly checklist |
| `email-campaign/monday-item-of-week.js` | ✅ Updated (split layout) |
| `email-campaign/wednesday-meal-prep-close.js` | ✅ Updated (added deli section) |
| `email-campaign/thursday-reopen.js` | ✅ Updated (clearer labels) |
| `email-campaign/sunday-preview.js` | ✅ Updated (split layout) |

## Known Issues Documented

- Many images are placeholders or wrong (documented in PRODUCTION-NOTES.md)
- Descriptions need real copy weekly
- Need actual 6-bowl lineup each week
- Tuesday catering images need real catering shots
- Thursday/Sunday hero images need updating

## Weekly Process (Documented)

1. Update `images` object URLs for current week
2. Update bowl names/descriptions in each day template
3. Verify add-ons (desserts, juices) in stock
4. Test with own email
5. Send to list

## Next Steps

- Phillip to verify images + descriptions for next week's bowls
- Consider building Google Sheets-driven system for easier weekly updates
- Test send before first real campaign

## Key Decisions

- Kept Phillip's exact format (he rejected my restructured versions)
- Single file is simpler than combined workflow
- "Order online" replaces all "Walk up" references
- Schedule reflects actual operations (meal prep ≠ walk-up)

---
**End of session.**
