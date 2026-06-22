# Frontend Source Field Rule

**Date:** 2026-06-11
**Rule:** Every frontend sends a `source` field identifying itself in webhook payloads and DB inserts.

## Frontends and Source Values

| Frontend | File | Source Value |
|----------|------|-------------|
| Pickup Order | `order-form.js` | `pickup-order` |
| Meal Prep | `catering-form.js` | `meal-prep` |

## Implementation

### Pickup Order (order-form.js)
```javascript
const payload = {
  // ... other fields ...
  source: 'pickup-order',
  timestamp: new Date().toISOString()
};
```

### Meal Prep (catering-form.js)
```javascript
const payload = {
  source: 'meal-prep',
  timestamp: new Date().toISOString(),
  // ... other fields ...
};
```

## Database Schema
```sql
source TEXT DEFAULT 'meal-prep'
```

## Why This Matters
- Allows filtering orders by origin (pickup vs meal-prep)
- Enables separate reporting/analytics per channel
- Makes debugging easier — know which frontend created the order
- Future-proof: new frontends just add their own source value

## Adding a New Frontend
1. Choose a unique source value (e.g., `catering-form`, `delivery-app`)
2. Add `source: 'your-value'` to the payload
3. Update this doc

## Files
- `utopia-deli-revamp/order-form.js` — pickup-order frontend
- `utopia-deli-revamp/catering-form.js` — meal-prep frontend
- `utopia-deli-revamp/orders.db` — SQLite database with source column
