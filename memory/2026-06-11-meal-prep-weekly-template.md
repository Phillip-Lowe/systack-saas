# Meal Prep Weekly Image Update Template

## Standard Process (Every Week)

When new meal prep photos arrive:

1. **Get photos** from Jacqueline (owner) via text/iCloud/whatever
2. **Identify each meal** using image analysis
3. **Match names** to the current menu items in `catering/catering-form.js`
4. **Rename files** descriptively: `meal-{dish-name}.jpg` or `dessert-{name}.jpg`
5. **Copy to** `catering/images/`
6. **Update JS** — swap the `photo` path in the MEALS or DESSERTS array
7. **Commit and push:**
   ```bash
   git add catering/
   git commit -m "feat(menu): Week of [DATE] — new meal prep photos"
   git push origin main
   ```
8. **Done** — GitHub Pages auto-deploys

## CSS Rule: Dessert Cards
- Grid uses `justify-items: center` so single desserts don't float left
- Applies to `.dessert-grid`

## Current Menu (as of 2026-06-11)
**Meals ($12):**
- Buffalo Chickpea Ranch Bowl
- Teriyaki Tofu Bowl
- Red Lentil Coconut Masala
- Peanut Ginger Bowl
- Cajun Northern Beans & Rice
- Rainbow BBQ Tofu Wild Rice

**Desserts ($6):**
- Raspberry Dark Chocolate Mousse

## Pricing
- Meals: $12.00
- Desserts: $6.00
- Labor & Packaging: $50.00 flat
- Tax: 9.52%
