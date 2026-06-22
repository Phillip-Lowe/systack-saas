# LinkedIn Post — Weekly Meal Prep Automation

**Date:** June 10, 2026
**Posted:** ~12:19 PM CDT
**Platform:** LinkedIn (Systack business account)
**Topic:** Utopia Deli meal prep ordering system launch
**Status:** ✅ POSTED BY USER

---

## Post Text

My partner's deli just got meal prep ordering automated.

🍱 Weekly Meal Prep — 6 rotating meals, $12 each
📅 Orders in by Wednesday noon, pickup Thursday
💳 Pay online through Square — no chasing invoices
🤖 Whole thing runs through n8n behind the scenes

The cool part? It's the same workflow as the regular pickup ordering. No separate system. No extra complexity. Just a switch node that routes meal-prep orders one way and pickup orders another. Same validation, same payment link generation, same database.

Built it this morning. Fixed it three times. Shipped it at noon.

The thing about building automation: it's never the big architecture decisions that get you. It's the small stuff. A `body` variable that should've been `$json`. A merge node that was eating the payment link before it reached the frontend. A duplicate `const` declaration that silently killed all the JavaScript on the page.

But now? It works. Someone orders meals, Square creates the payment link, they pay, and the deli just makes the food.

That's the goal. Make the tech disappear so the food business can do what it actually does — feed people.

#BuildInPublic #Automation #n8n #SquareAPI #SmallBusiness #FoodTech #Systack
