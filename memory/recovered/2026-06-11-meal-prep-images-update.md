# Meal Prep Page Update — 2026-06-11

## Changes Made

### 1. Real Meal Photos Added
Copied 7 images from `utopia-deli-revamp/images/Meal Prep/` to `catering/images/` with descriptive names:

| File | Food Item |
|------|-----------|
| `meal-buffalo-chickpea.jpg` | Buffalo Chickpea Ranch Bowl |
| `meal-teriyaki-tofu.jpg` | Teriyaki Tofu Bowl |
| `meal-red-lentil-masala.jpg` | Red Lentil Coconut Masala |
| `meal-peanut-ginger.jpg` | Peanut Ginger Bowl |
| `meal-cajun-northern-beans.jpg` | Cajun Northern Beans & Rice |
| `meal-rainbow-bbq-tofu.jpg` | Rainbow BBQ Tofu Wild Rice |
| `dessert-raspberry-mousse.jpg` | Raspberry Dark Chocolate Mousse |

### 2. Menu Items Updated (`catering-form.js`)
**Meals ($12 each):**
- Buffalo Chickpea Ranch Bowl (490 cal)
- Teriyaki Tofu Bowl (480 cal)
- Red Lentil Coconut Masala (510 cal)
- Peanut Ginger Bowl (500 cal)
- Cajun Northern Beans & Rice (470 cal)
- Rainbow BBQ Tofu Wild Rice (520 cal)

**Desserts ($6 each):**
- Raspberry Dark Chocolate Mousse (340 cal)

**Pricing:**
- Meals: $12.00
- Desserts: $6.00
- Labor & Packaging: $50.00 flat fee
- Tax: 9.52%

### 3. Dessert Section Added (`index.html`)
- Separate "Add a Sweet Treat" section below meals
- Distinct styling with pink accent header
- Same card layout as meals

### 4. Tax Rate Aligned with n8n
- Frontend: `TAX_RATE = 0.0952` (9.52%)
- Backend n8n: Already updated by Phillip to 9.52%

### 5. Files Modified
- `catering/index.html` — Added dessert section HTML/CSS
- `catering/catering-form.js` — Updated meals array, added DESSERTS array, dynamic pricing
- `catering/images/` — 7 new food photos

## n8n Workflow Status
✅ **No changes needed** — workflow is item-agnostic. Items flow through generically with `name`, `qty`, `price`, `category`. Desserts pass through same as meals.

## Deployment
Copy updated files to production:
```bash
scp -r catering/* root@theutopiadeli.com:/var/www/order/catering/
```

## Testing Checklist
- [ ] All 6 meal photos display correctly
- [ ] Dessert photo displays correctly
- [ ] Meal qty buttons work
- [ ] Dessert qty buttons work
- [ ] Subtotal calculates correctly ($12×qty meals + $6×qty desserts)
- [ ] Labor fee ($50) always added
- [ ] Tax at 9.52% calculated correctly
- [ ] Total = subtotal + labor + tax
- [ ] Checkout submits to n8n webhook
- [ ] Square payment link received
- [ ] Success page shows after payment
