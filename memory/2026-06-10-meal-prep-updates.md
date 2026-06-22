# Meal Prep System Updates — 2026-06-10

## Changes Made Today

### 1. Catering Page Fixes (catering/index.html, catering/catering-form.js)
- **Logo path**: Fixed from `images/logo.png` → `../images/logo.png` (catering is subdirectory)
- **Meal grid rendering**: Added `initMealPrep()` call so meal cards actually render
- **Meal card images**: Changed from `display:none` to `display:block` with lazy loading
- **Meal image paths**: Fixed from `images/` to `../images/` for all 6 meal photos
- **Order link removed**: Removed "← Order Online" from header (catering page is standalone)

### 2. Order Page Fix (pickup-order/index.html)
- **Removed catering link**: "🎉 Catering" link removed from header (standalone pages)

### 3. Meal Prep Images Added (images/)
- 6 meal prep photos renamed from IMG_2724-2729.JPG:
  - `mealprep-coconut-chickpea.jpg`
  - `mealprep-mediterranean.jpg`
  - `mealprep-bbq-mac.jpg`
  - `mealprep-chili-noodles.jpg`
  - `mealprep-peanut-tofu.jpg`
  - `mealprep-smokey-taco.jpg`

### 4. Meal Prep Success State Updated (catering/index.html)
New confirmation text for meal prep redirect page:

```
Thank you for ordering meal prep from The Utopia Deli.
Your payment was successful and your order is officially locked in.

Order Status
Status: Paid & Received
Your order has been received by The Utopia Deli system and queued for preparation.
No further action is required from you at this time.

Pickup Information
Thursday {DATE} — 12:30 PM – 7:30 PM
The Utopia Deli — 801 S. Chester St., Little Rock, AR

Meals are prepared fresh Thursday morning.
Have your name ready when approaching the window.

If you're early, your order may not be ready yet.
If you're late, we'll hold it until close.

Your Receipt
A payment receipt has been sent to {EMAIL}.
If you don't see it, check your spam or promotions folder.
Receipts are sent automatically by our payment processor.

Order Policy
Because all meals are prepared fresh, paid orders cannot be modified or canceled once submitted.
If you believe there is an error with your order, contact us immediately.

Need Help With Your Order?
Email: theutopiadelilittlerock@gmail.com
Phone: +1 (501) 551‑5944
```

### 5. Catering Success State Text
For catering redirect page:

```
Thank you for submitting your catering request to The Utopia Deli.
Your event inquiry has been received.

Request Status
Status: Pending Review
Your event details have been received by The Utopia Deli team and queued for review.
No further action is required from you at this time.

Review Process
Our team reviews your event details
We check availability for your requested date
You'll hear back within 24 hours via email
If approved, we'll send a menu proposal and quote
50% deposit holds your date

Event Summary
Event: {EVENT_NAME}
Date: {EVENT_DATE}
Guests: {HEADCOUNT}
Venue: {VENUE_NAME}

Your Confirmation
A confirmation has been sent to {EMAIL}.
If you don't see it, check your spam or promotions folder.

Event Policy
All catering bookings require a 50% deposit to hold your date.
Events within 2 weeks require full payment upfront.
Because all food is prepared fresh, confirmed events cannot be canceled within 48 hours.

Need Help With Your Request?
Email: theutopiadelilittlerock@gmail.com
Phone: +1 (501) 551‑5944
```

## Current Meal Data (6 meals)

| ID | Name | Calories | Price | Description |
|----|------|----------|-------|-------------|
| coconut-chickpea | Coconut Chickpea & Lentil Curry | 480 | $12.00 | Creamy coconut curry with chickpeas, lentils, and rice |
| mediterranean | Mediterranean Bowl | 510 | $12.00 | Rice, roasted cauliflower, cucumber-tomato salad, chickpeas, hummus, feta |
| bbq-chikn-mac | BBQ Chik'n Mac Bowl | 520 | $12.00 | BBQ glazed chik'n with creamy mac & cheese |
| chili-garlic-noodles | Chili Garlic Protein Noodles | 490 | $12.00 | Wheat noodles with peppers, tofu, and chili-garlic sauce |
| peanut-ginger-tofu | Peanut Ginger Tofu Bowl | 500 | $12.00 | Crispy tofu with peanut-ginger sauce, cabbage slaw, and rice |
| smokey-taco | Smokey Taco Bowl | 470 | $12.00 | Rice, beans, corn, peppers, and seasoned plant-based crumble |

## Pricing Structure
- Each meal: $12.00
- Labor/Packaging: $50.00
- Tax: 6.5%
- Total = (meals × $12) + $50 + tax

## Deadlines
- Orders due: Wednesday at 12:00 PM (noon)
- Pickup: Thursday 12:30 PM – 7:30 PM
- Portal closes Wednesday noon, reopens Friday noon

## Git Status
- All changes committed and pushed to GitHub
- GitHub Pages rebuilding
- Live URL: https://order.theutopiadeli.com/catering/

---

## UPDATE: New Weekly Menu (6/11–6/18) — Deployed 2026-06-10 08:12 CDT

### New Meals (No Images Yet — Will Add As Made)
| # | Meal | Calories | Description |
|---|------|----------|-------------|
| 1 | Buffalo Chickpea Ranch Bowl | 490 | Crispy buffalo chickpeas with ranch drizzle over rice and greens |
| 2 | Teriyaki Tofu Bowl | 480 | Sweet teriyaki glazed tofu with steamed broccoli and rice |
| 3 | Red Lentil Masala | 510 | Spiced red lentil curry with tomatoes, ginger, and basmati rice |
| 4 | Baked Potato Protein Bowl | 520 | Loaded baked potato bowl with plant-based crumble, cheese, and chives |
| 5 | Cajun White Bean & Rice | 470 | Smoky Cajun-spiced white beans with peppers and seasoned rice |
| 6 | Korean BBQ Bowl | 500 | Sweet and savory Korean-style BBQ with kimchi and steamed rice |

### Changes Made
- Replaced old 6 meals with new weekly rotation
- Added week label: "📅 This Week: June 11–18"
- Changed image rendering from `<img>` to CSS placeholder with 🍽️ emoji
- No image files needed — real photos will be added as meals are prepared/photographed
- Ready for customers starting Thursday 6/11

### Process Going Forward
- Each week: swap meal data in `catering-form.js`
- Add real photos to `images/` when available
- Update week label with new date range

---

## UPDATE: Hero Text + Disclaimer (2026-06-10 09:09 CDT)

### Changes
- **Hero title**: "Catering & Events" → "Catering, Events & Meal Prep"
- **Hero subtitle**: Now mentions "Or grab healthy meal prep for the week"
- **Hero badge**: Added 🍱 Weekly Meal Prep
- **Deadline section**: Added cancellation disclaimer:
  > "Because all orders are prepared fresh, paid orders cannot be modified or canceled once submitted."

### Files Changed
- `catering/index.html`

---

## UPDATE: Payment Flow Fixed (2026-06-10 09:20 CDT)

### Problem
Frontend was showing success immediately without collecting payment.

### Solution
Implemented full Square payment flow:

**Frontend Changes:**
1. `submitMealPrep()` now waits for webhook response with `payment_link`
2. Redirects browser to Square payment page (not showing success yet)
3. Handles `?mp_success=1&order=UMP-xxx` on page load to show success
4. Displays Order ID in styled box on success page

**n8n Changes:**
1. Fixed `ReferenceError: body is not defined` — changed to `input` variable
2. Added "MP - Send Payment Response to Frontend" node (Respond to Webhook)
3. Returns `{ payment_link, order_id, total_cents, pickup_date }` to browser
4. Square redirect URL: `https://order.theutopiadeli.com/catering/?mp_success=1&order={{ $json.order_id }}`

**Payment Flow:**
```
Customer clicks "Pay & Place Order"
  → Frontend sends data to n8n webhook
  → n8n validates, creates Square payment link
  → n8n returns payment_link to browser
  → Browser redirects to Square checkout
  → Customer pays on Square
  → Square redirects back to catering page with ?mp_success=1
  → Page shows success with order ID
```

### Files Changed
- `catering/catering-form.js` — payment redirect + return handling
- `catering/index.html` — order ID display box + CSS
- `utopia-deli-revamp/meal-prep-n8n-nodes.json` — complete n8n workflow

### n8n Node Sequence
1. Switch - Source Router (meal-prep vs regular)
2. MP - Validate (deadline, fields, pricing)
3. MP - Build Square Line Items (convert to Square format)
4. MP - Create Square Payment Link (HTTP Request to Square API)
5. MP - Extract Payment Link (parse Square response)
6. MP - Send Payment Response to Frontend (Respond to Webhook)
7. MP - Email Customer Payment Link (SMTP)
8. MP - Save to SQLite (database logging)

### Commit
`f49dfe4` — fix(meal-prep): payment flow — redirect to Square, handle return

---

## UPDATE: Payment Flow Working + ORACLE Restructure (2026-06-10 11:58 CDT)

### Payment Link Finally Working
**Root cause:** Merge2 was losing the `payment_link` object from Square response due to node name mismatches and chained merges.

### ORACLE Restructure Applied
Per ORACLE handoff spec, simplified MP branch to match pickup pattern:

```
mp compute totals → Square HTTP
mp compute totals → MP Merge (input 0)
Square HTTP → MP Merge (input 1)
MP Merge → Save to SQLite2 → MP Format Response
```

**Key changes:**
- Removed Merge3, Merge4, Extract Payment Link nodes
- Single MP Merge Code node (not n8n merge node)
- SQLite2 references `$("MP Merge")` directly
- Format Response returns `square_link` field matching pickup pattern
- DB save uses `source: 'meal-prep'`

### Files
- `utopia-deli-revamp/mp-nodes-v2.json` — Final working node definitions
- `utopia-deli-revamp/meal-prep-n8n-nodes.json` — Complete workflow spec
- `catering/index.html` — Frontend with meal grid, CTA, success state
- `catering/catering-form.js` — Payment redirect flow

### Verified Working
- Square payment link: `https://square.link/u/jq6ij198`
- Order created in Square with correct line items + tax
- Frontend redirects to Square payment page
- SQLite save with source: 'meal-prep'

