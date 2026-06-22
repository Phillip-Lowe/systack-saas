# Meal Prep Page — Dessert Centering Fix

## Problem
Dessert card was floating left in a 2-column grid despite `justify-items: center`. The grid had 2 columns but only 1 dessert item.

## Fix Applied (2026-06-11 10:21 CDT)
Changed `.dessert-grid` CSS in `catering/index.html`:

**Before:**
```css
.dessert-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);  /* 2 columns */
  gap: 16px;
  margin-bottom: 16px;
  justify-items: center;
}
```

**After:**
```css
.dessert-grid {
  display: grid;
  grid-template-columns: 1fr;  /* Single column */
  gap: 16px;
  margin-bottom: 16px;
  justify-items: center;
  max-width: 400px;         /* Prevent stretching */
  margin-left: auto;        /* Center the grid container */
  margin-right: auto;       /* Center the grid container */
}
```

## Result
- Single dessert card now sits nicely centered at ~400px max-width
- Looks balanced and intentional rather than floating left
- Ready for multiple desserts if added later (grid stays clean)

## Commit
`668a264` — fix(css): single dessert column centered with max-width

## Weekly Template Reminder
When uploading new weekly meal prep images:
1. Drop photos in `utopia-deli-revamp/images/Meal Prep/`
2. I identify, rename, match to menu items
3. Update `MEALS`/`DESSERTS` array in `catering-form.js`
4. Commit + push → GitHub Pages auto-deploys
