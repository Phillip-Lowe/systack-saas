# BBQ Mac & Cheese Added as 7th Meal — 2026-06-16

## What Changed
Added "BBQ Chik'n Mac & Cheese" as the 7th meal to the Utopia Deli meal prep page.

## Problem
- `MEALS_PER_PACKAGE = 7` was set during the June 15 redesign
- But the `MEALS` array only had 6 entries
- HTML said "6 healthy meals" and "$72.00/set" (6 × $12)
- Package pricing was inconsistent — 7 meals per set but only 6 defined

## Changes Made

### `catering/catering-form.js`
- Added 7th meal to MEALS array:
  ```js
  { id: 'bbq-mac', name: 'BBQ Chik'n Mac & Cheese', calories: 520, price: 1200, photo: 'images/meal-bbq-mac.jpg', desc: 'BBQ glazed chik'n with creamy mac & cheese' }
  ```
- `MEALS.length` now returns 7 (matches `MEALS_PER_PACKAGE`)
- Weekly subtotal: `weeklySets * 7 * $12 = $84/set` ✅

### `catering/index.html`
- "6 healthy" → "7 healthy, ready-to-eat meals"
- "$72.00/set" → "$84.00/set"

### `catering/images/meal-bbq-mac.jpg`
- Copied from `images/mealprep-bbq-mac.jpg` (original photo from June 10)

## Git
- Commit: `4c60f7f` on `Phillip-Lowe/utopia-deli-order`
- Pushed and deployed via GitHub Pages

## Current Meal Lineup (7 meals)
1. Buffalo Chickpea Ranch Bowl (490 cal)
2. Teriyaki Tofu Bowl (480 cal)
3. Red Lentil Coconut Masala (510 cal)
4. Peanut Ginger Bowl (500 cal)
5. Cajun Northern Beans & Rice (470 cal)
6. Rainbow BBQ Tofu Wild Rice (520 cal)
7. **BBQ Chik'n Mac & Cheese (520 cal)** ← NEW

## Pricing (unchanged)
- Weekly Set: 7 meals = $84.00
- Dessert Set: 7 desserts = $42.00
- Labor: $50.00 per set
- Tax: 9.52%

## Live URL
https://order.theutopiadeli.com/catering/
