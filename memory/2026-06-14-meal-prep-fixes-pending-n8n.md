# Meal Prep Order Fixes — 2026-06-14

**Status:** Frontend code updated, pushed to GitHub. n8n backend still needs manual update.

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

### Key Code Changes:

**Constants added:**
```javascript
const MEALS_PER_PACKAGE = 6;
```

**Rendering:**
- Meals now show: "$12.00 per meal · $72.00 per package"
- Button changed from +/- qty to "Add to Package" / "Remove"

**Pricing calculation (updateMPTotals):**
```javascript
// Each selected meal = 1 package of 6 meals
const selectedMeals = MEALS.filter(function(m) { return mpCart[m.id]; });
const labor = LABOR_FEE * selectedMeals.length; // $50 per package
```

**Payload (submitMealPrep):**
```javascript
// Qty sent as 6 (MEALS_PER_PACKAGE) per selected meal
lineItems.push({ id: meal.id, name: meal.name, qty: qty * MEALS_PER_PACKAGE, ... });
const labor = LABOR_FEE * packageCount;
```

---

## Pending: n8n Backend Update

**Needs to be done at workstation (Phillip).**

The n8n workflow `utopia-deli-order-v4` has hardcoded values that need updating:

### Node: MP - Build Square Payload
**File:** `utopia-deli-revamp/meal-prep-n8n-nodes.json`

Change:
```javascript
// FROM:
lineItems.push({
  name: 'Tax (6.5%)',
  quantity: '1',
  base_price_money: { amount: input.pricing.tax, currency: 'USD' }
});
tax_rate_percent: '6.5',

// TO:
lineItems.push({
  name: 'Tax (9.52%)',
  quantity: '1',
  base_price_money: { amount: input.pricing.tax, currency: 'USD' }
});
tax_rate_percent: '9.52',
```

### Node: MP - Email Customer Payment Link
**File:** Same JSON

Change:
```html
<!-- FROM: -->
<tr><td colspan="2">Labor & Packaging</td><td align="right">$50.00</td></tr>
<tr><td colspan="2">Tax (6.5%)</td><td align="right">${($json.pricing.tax / 100).toFixed(2)}</td></tr>

<!-- TO: -->
<tr><td colspan="2">Labor & Packaging</td><td align="right">${($json.pricing.labor / 100).toFixed(2)}</td></tr>
<tr><td colspan="2">Tax (9.52%)</td><td align="right">${($json.pricing.tax / 100).toFixed(2)}</td></tr>
```

**Note:** The email template already uses `$json.pricing.labor` variable — just need to replace the hardcoded "$50.00" with the expression.

---

## Verification Checklist (for Phillip)

- [ ] Deploy updated `catering-form.js` to GitHub Pages (push commit)
- [ ] Update n8n "MP - Build Square Payload" node: Tax label 6.5% → 9.52%
- [ ] Update n8n "MP - Email Customer Payment Link" node: Labor label $50.00 → dynamic
- [ ] Test order on live site: select 2 meal types, verify total = $267.24
- [ ] Verify Square payment link shows correct line items
- [ ] Verify confirmation email shows correct pricing

---

## Context

User directive: "No save this everywhere note I'll fix n8n when I return to the workstation end session"

This file serves as the handoff note for the n8n work.
