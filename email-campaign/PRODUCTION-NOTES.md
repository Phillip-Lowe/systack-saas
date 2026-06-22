# Utopia Deli Weekly Email Campaign — Production Notes

**Date:** 2026-06-22  
**Status:** ✅ Base templates complete — needs weekly content updates
**File:** `email-campaign/utopia-deli-all-days.js`

---

## 📧 What's Built

Single n8n Function node with all 7 days:

| Day | Subject | Focus |
|-----|---------|-------|
| Monday | 🍱 Meal Prep is Open | Opens meal prep window |
| Tuesday | 🎉 Planning an Event? | Catering push |
| Wednesday | ⏰ Closes Today at Noon | Final hours urgency |
| Thursday | 🍱 Meal Prep Reopens at 8PM | Reopen + order online menu |
| Friday | Weekend at Utopia 🍽️ | Weekend kickoff |
| Saturday | 🙌 We're Open Today! | Day-of reminder |
| Sunday | 📋 This Week's Menu + Monday Lunch | Preview + lunch tease |

**Schedule:**
- Meal prep orders: Close Wed 12:00 PM → Reopen Thu 8:00 PM → Pickup Thu 12:30–7:30
- Order online: Monday–Saturday 12:30 PM – 7:30 PM

---

## ⚠️ KNOWN ISSUES (Needs Fixing Weekly)

### Images — Many Are Placeholders or Wrong

| Day | Image # | Current | What It Should Be |
|-----|---------|---------|-------------------|
| **Monday** | 0 | `Deli Meal Prep Plate 1.jpg` | Need actual meal prep hero shot |
| **Monday** | 1 | `mealprep-mediterranean.jpg` | Verify this is current bowl photo |
| **Monday** | 2-3 | Various meal prep | ✅ Likely OK but verify weekly |
| **Tuesday** | 0 | `Deli Catering Salad.jpg` | ✅ Catering OK |
| **Tuesday** | 1 | `Deli Catering Fruit Salad.jpg` | ✅ Catering OK |
| **Tuesday** | 2 | `cowboy_chicken.webp` | ❌ This is a menu item, not catering |
| **Wednesday** | 0-2 | `mealprep-smokey-taco.jpg`, etc. | ✅ Likely OK but verify weekly |
| **Thursday** | 0 | `meal-mediterranean-harvest.jpg` | ❌ This is a bowl photo, not "reopening" hero |
| **Thursday** | 1-3 | Menu items (Philly, fries, poppers) | ✅ These are fine for walk-up section |
| **Friday** | 0 | `Deli Happy customer lady.jpg` | ✅ Lifestyle/vibe OK |
| **Friday** | 1-5 | Menu items | ✅ Likely OK |
| **Saturday** | 0-3 | Menu items | ✅ Likely OK |
| **Sunday** | 0 | `meal-mediterranean-harvest.jpg` | ❌ Need "menu preview" hero shot |
| **Sunday** | 1-2 | Menu items for Monday lunch | ✅ Fine |

### Descriptions — Need Real Copy Weekly

| Day | Issue |
|-----|-------|
| **Monday** | "How Meal Prep Works" section — verify pickup times match current schedule |
| **Tuesday** | Catering descriptions are generic — update based on actual offerings |
| **Wednesday** | "Meal Prep Favorites" — need actual current bowls |
| **Thursday** | "Next Week's Bowls" section — need actual next week lineup |
| **Sunday** | "This Week's Menu" — need all 6 bowls with real descriptions |

---

## 📝 Weekly Update Checklist

Before sending each week, verify:

- [ ] **Images** — All 6 meal prep bowl photos match current menu
- [ ] **Bowl names** — Match what's actually being offered this week
- [ ] **Descriptions** — Accurate ingredients/flavors for each bowl
- [ ] **Add-ons** — Desserts and juices in stock
- [ ] **Schedule** — Pickup times, order deadlines haven't changed
- [ ] **Hero images** — Monday, Thursday, Sunday need fresh/relevant shots
- [ ] **Links** — URLs still work (catering page, pickup-order page)

---

## 📂 File Locations

| File | Purpose |
|------|---------|
| `email-campaign/utopia-deli-all-days.js` | **Master file** — copy into n8n Function node |
| `email-campaign/utopia-deli-weekly-email-campaign.json` | Combined workflow JSON (if using routing) |
| `email-campaign/monday-item-of-week.js` | Standalone Monday (backup) |
| `email-campaign/tuesday-catering.js` | Standalone Tuesday (backup) |
| `email-campaign/wednesday-meal-prep-close.js` | Standalone Wednesday (backup) |
| `email-campaign/thursday-reopen.js` | Standalone Thursday (backup) |
| `email-campaign/friday-weekend.js` | Standalone Friday (backup) |
| `email-campaign/saturday-weekend.js` | Standalone Saturday (backup) |
| `email-campaign/sunday-preview.js` | Standalone Sunday (backup) |

---

## 🔄 How to Use

1. **Open** `utopia-deli-all-days.js`
2. **Edit** the `images` object at the top — swap URLs for current week
3. **Edit** each day's template body — update descriptions, bowl names
4. **Copy entire file** into n8n Function node
5. **Test** with your own email first
6. **Send**

---

## 🗓️ Future Improvements

- Build a proper CMS/sheet-driven system where Phillip updates a Google Sheet and the email auto-pulls from it
- Set up image hosting with consistent naming (e.g., `bowls/week-of-06-22/...`)
- Create a simple "weekly update" form that generates the code automatically

---

**Saved by:** SOL  
**Session:** 2026-06-22 ~10:00 CDT  
**Status:** Ready for testing, needs weekly content updates
