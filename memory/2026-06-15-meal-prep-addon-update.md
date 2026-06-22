# Meal Prep Page Update — Single Add-Ons + Juice

**Date:** 2026-06-15 09:04 CDT
**Status:** ✅ Code committed and pushed (deploy pending)
**Commit:** `4c10a7a`

---

## What Changed

### 1. Dessert → Single Add-On
- Was: "Dessert Sets" (6-pack, $42/set) with ± buttons
- Now: Single-item add-on with per-card ± quantity buttons
- Price: $6.00 each

### 2. Added Fresh Cold-Pressed Juice
- Name: Fresh Cold-Pressed Juice
- Price: $10.00 each
- Desc: "16 oz glass bottle — Pineapple, Honeycrisp Apple, Lemon"
- Photo: `images/cold_pressed_juice_v2.jpg`
- Positioned beside dessert in 2-column grid

### 3. Layout: Evenly Centered Under Meals
- Both dessert and juice in same `addon-grid` (2 columns desktop, 1 column mobile)
- Shared header: "Add a Sweet Treat or Drink"
- Centered under the meal grid with `max-width: 640px`

### 4. Labor Fee = Static $50
- Was: $50 × (weekly sets + dessert sets)
- Now: $50 flat fee if any meal set is ordered
- Add-ons (dessert/juice) do NOT increase labor cost
- If no meal set ordered, labor = $0

### 5. Add-On Locking
- Dessert and juice sections **hidden until ≥1 meal set added**
- `addon-section` display toggled by `updateMPTotals()`
- Prevents ordering just add-ons without meals

---

## Files Modified

| File | Change |
|------|--------|
| `catering/catering-form.js` | Restructured cart logic, added `DRINKS` array, `updateAddonQty()`, static labor fee |
| `catering/index.html` | Replaced dessert section with unified add-on section, new CSS classes |
| `catering/images/cold_pressed_juice_v2.jpg` | Copied from pickup-order images |

---

## New DOM Structure

```
meal-prep-section
├── meal-grid (6 meals, 2-col)
├── package-control (weekly sets ±)
├── addon-section [hidden until meal set selected]
│   ├── addon-header
│   └── addon-grid (dessert + juice, 2-col)
├── mp-cta [shown when empty]
├── mp-totals [shown when items in cart]
└── mp-checkout
```

---

## Pricing Logic

```
subtotal = (weeklySets × 6 × $12) + (dessertQty × $6) + (juiceQty × $10)
labor = weeklySets > 0 ? $50 : $0
tax = (subtotal + labor) × 9.52%
total = subtotal + labor + tax
```

---

## Deployment
- ✅ Committed: `4c10a7a`
- ✅ Pushed: `main` → GitHub
- ⏳ Server sync: `rsync`/`git pull` needed (SSH timeout encountered)

**Deploy command (manual):**
```bash
ssh root@theutopiadeli.com "cd /var/www/order && git pull origin main"
```

---

## Testing Checklist

- [ ] Meal sets still add/remove correctly
- [ ] Add-on section appears when meal set added
- [ ] Add-on section hides when all meal sets removed
- [ ] Dessert qty buttons work individually
- [ ] Juice qty buttons work individually
- [ ] Labor fee stays $50 regardless of add-on qty
- [ ] Tax calculates correctly
- [ ] Total = meals + add-ons + $50 labor + tax
- [ ] Checkout payload sends correct items array
- [ ] Both add-on photos display
- [ ] Mobile: single column layout

---

## Breaking Changes
- Removed: `addDessertPackage()`, `removeDessertPackage()` functions
- Removed: `.dessert-grid`, `.dessert-section`, `.dessert-card` CSS classes
- Removed: `#dessert-package-qty` element
- Cart keys changed: `mpCart['dessert_sets']` → `mpCart['raspberry-mousse']` (qty number)
