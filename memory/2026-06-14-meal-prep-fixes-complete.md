# Meal Prep Order Fixes — 2026-06-14

**Status:** ✅ COMPLETE - Frontend committed and pushed. n8n backend updated by Phillip.

---

## Problems Fixed (Frontend)

### 1. No Minimum Order Enforcement
**Before:** Customers could order 1, 2, 3 individual meals. No package concept.
**After:** Each meal type selected = 1 package of 6 meals. Customers select meal types (toggle on/off), not individual quantities.

### 2. Wrong Tax Rate Label
**Before:** Label showed "Tax (6.5%)" but calculated at 9.52%
**After:** Label now correctly shows "Tax (9.52%)"

### 3. Labor Fee Logic
**Before:** $50 labor fee added once total, regardless of order size
**After:** $50 labor fee per package. If customer orders 2 packages (2 meal types), labor = $100

---

## Pricing Model (Correct)

- 1 package = 6 meals (one of each selected meal type)
- Each meal shown at $12.00, but billed as package: 6 × $12 = $72.00
- Labor: $50.00 per package
- Tax: 9.52% on (subtotal + labor)

**Example:**
- Customer selects Buffalo Chickpea + Teriyaki Tofu = 2 packages
- Subtotal: $72 + $72 = $144
- Labor: $50 × 2 = $100
- Tax: ($144 + $100) × 0.0952 = $23.24
- **Total: $267.24**

---

## Files Modified

| File | Change |
|------|--------|
| `catering/catering-form.js` | Replaced `setMealQty()` with `toggleMeal()` for on/off selection. Updated `updateMPTotals()` to calculate per-package pricing. Updated `submitMealPrep()` to send package-based qty and variable labor. |
| `catering/index.html` | Added CSS for `.meal-toggle-btn` and selected state styles |

### Key Code Changes:

**Constant added:**
```javascript
const MEALS_PER_PACKAGE = 6;
```

**Rendering:**
- Meals now show: "$12.00 per meal · $72.00 per package (6 meals)"
- Button changed from +/- qty to "Add to Package" (toggles on/off)

**Pricing calculation (updateMPTotals):**
```javascript
// Each selected meal = 1 package of 6 meals
const selectedMealTypes = [];
MEALS.forEach(function(meal) {
  if (mpCart[meal.id]) {
    selectedMealTypes.push(meal);
    const packagePrice = meal.price * MEALS_PER_PACKAGE;
    subtotal += packagePrice;
    // ... display package
  }
});

// Labor: $50 per package (per meal type)
const packageCount = selectedMealTypes.length + (mpCart['raspberry-mousse'] ? 1 : 0);
const labor = LABOR_FEE * packageCount;
```

**Payload (submitMealPrep):**
```javascript
// Qty sent as 6 (MEALS_PER_PACKAGE) per selected meal
lineItems.push({ id: meal.id, name: meal.name, qty: MEALS_PER_PACKAGE, ... });
const labor = LABOR_FEE * packageCount;
```

---

## Git History

```
a85bd40 fix(meal-prep): enforce minimum order, fix tax rate, per-package labor
```

---

## Status: ✅ DONE

- [x] Frontend committed and pushed to GitHub Pages
- [x] n8n backend updated (Phillip confirmed)
- [x] Tax rate label corrected to 9.52%
- [x] Labor fee now per-package
- [x] Package-based minimum order enforced

---

## Original Issue

Session from 2026-06-14 timed out before completing file changes. Memory documented the intent but execution failed. Fixed in session 2026-06-15.
