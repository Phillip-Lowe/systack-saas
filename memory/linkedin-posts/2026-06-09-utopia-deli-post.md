# LinkedIn Post — Utopia Deli (Updated: June 9, 2026)

**Status:** Draft — ready for review  
**Platform:** LinkedIn (Systack business account)  
**Tone:** Story-driven, problem/solution, local pride  
**Hashtags:** #SmallBusiness #FoodService #Automation #LittleRock #LocalBusiness #AI #Systack

---

## Post Text

My partner runs a deli in Little Rock. Great food, loyal customers, brutal back-office work.

Every night: 2-3 hours of "paperwork" — copying orders, confirming catering requests, chasing payments, answering "are you open?" for the 40th time.

So we built something.

**Online ordering:** https://order.theutopiadeli.com/pickup-order/
- Full menu with modifiers (extra sauce, no tomato, swap the protein)
- Cart builds in real-time, Square checkout at the end
- Customer gets receipt, kitchen gets the order automatically

**Catering/events:** https://order.theutopiadeli.com/catering/
- 5-step form captures event details, headcount, dietary restrictions
- Auto-scoring engine (0-100) ranks leads by size, budget, lead time, distance
- Customer gets instant email response with payment terms
- Owner gets notified on every submission
- Everything logs to a database for follow-up

**What it actually replaced:**
→ Phone tag for catering availability
→ Manual quote building in spreadsheets
→ "Did they pay the deposit?" confusion
→ Copying order details into Square one by one

**The whole system:**
- Frontend: HTML/CSS/JS on GitHub Pages (free hosting)
- Backend: n8n workflow automation (visual, maintainable)
- Payments: Square (they already used it)
- Database: SQLite for lead tracking
- Emails: Automated via SMTP

Total fixed cost: $0. Only Square's per-transaction fee.

The modifier system alone took 3 days — 17 items, 30+ modifier groups, 100+ individual options. Required choices, multi-select sauces, hold-the-lettuce, swap-the-dressing. The kind of complexity that makes generic ordering platforms charge $200+/month.

We built it once. Now we can white-label it for any restaurant that wants the same thing without the enterprise software bill.

If you know a food business drowning in busywork, send them my way.

---

## Links to Include
- **Pickup ordering:** https://order.theutopiadeli.com/pickup-order/
- **Catering/events:** https://order.theutopiadeli.com/catering/
- **Systack:** https://systack.net

## Media Suggestions
- Screenshot of the ordering page (mobile view best)
- Screenshot of the catering form
- Short video walkthrough (15-30 sec) of placing an order
- Photo of the deli + the system side by side

## Version History
- **2026-06-07:** Original draft ("Looking for good food in Little Rock?")
- **2026-06-09:** Complete rewrite with catering system, URLs, technical details
