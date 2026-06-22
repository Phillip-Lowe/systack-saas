# Utopia Deli — Complete Modifier System Data

## Raw Data Dump (2026-06-06 12:03 CDT)

### Items Table
| item_id | name | base_price | sell_price | description | active | image_url |
|---------|------|-----------|-----------|-------------|--------|-----------|
| COWBOY | cowboy chikn sandwich | 13.00 | 13.00 | Grilled Cowboy Chik'n, Lettuce, Tomato, Ranch, Bac'n. | TRUE | https://cdn.shopify.com/... |
| CLUB | chikn club sub | 15.00 | 15.00 | Grilled Chik'n Bac'n Cheese on a bed of Lettuce and Tomatoes. | TRUE | https://cdn.shopify.com/... |
| FRIED | chikn fried chikn sub | 13.00 | 13.00 | Crispy Fried Chik'n on a hoagie with lettuce, tomato, ranch. | TRUE | https://cdn.shopify.com/... |
| POPPERS | chikn poppers | 10.00 | 10.00 | Crispy chik'n dippers or sauced with choice of BBQ, Garlic Parm, Jerk, Buffalo, Lemon Pepper Wet. | TRUE | |
| DUMPLING_TACOS | korean pork dumpling tacos | 10.00 | 10.00 | "Pork", pickled slaw, aioli, and sauce on a dumpling shell. Set of 4 tacos. | TRUE | |
| PHILLY | philly sub | 13.00 | 13.00 | "Stek" OR Chik'n with sautéed onions & bell peppers. | TRUE | |
| ROCKTOWN_SLIDERS | rocktown bourbon chikn sliders | 12.00 | 12.00 | Rocktown distillery bourbon‑infused chik'n with fresh slaw and aioli on a garlic butter slider bun. | TRUE | |
| JUICE_CP_16 | 16 oz glass bottle | 10.00 | 10.00 | Select size and flavor: Green Juice, Orange Machine, and Sweet Dreams. | TRUE | |
| JUICE_CP_10 | 10 oz plastic bottle | 5.00 | 5.00 | Select size and flavor: Green Juice, Orange Machine, and Sweet Dreams. | TRUE | |
| COOKIES_2 | two fresh baked chocolate chip cookies | 4.00 | 4.00 | 2 fresh baked chocolate chip cookies. | TRUE | |
| SIDE_SALAD | side salad | 5.00 | 5.00 | Fresh mixed greens with house vegetables, served with your choice of dressing. | TRUE | |
| WATER_16OZ | 16 oz bottled water | 2.00 | 2.00 | Crisp, chilled 16 oz bottled water — refreshing and perfect alongside any meal. | TRUE | |
| LOADED_FRIES_BC | loaded fries — bac'n | 13.00 | 13.00 | Crinkle‑cut fries with a golden, crispy exterior, loaded with your favorites. | TRUE | |
| LOADED_FRIES_PC | loaded fries — philly chik'n | 13.00 | 13.00 | Crinkle‑cut fries with a golden, crispy exterior, loaded with your favorites. | TRUE | |
| LOADED_FRIES_GP | loaded fries — garlic parm | 13.00 | 13.00 | Crinkle‑cut fries with a golden, crispy exterior, loaded with your favorites. | TRUE | |
| LOADED_FRIES_PS | loaded fries — philly "stek" | 13.00 | 13.00 | Crinkle‑cut fries with a golden, crispy exterior, loaded with your favorites. | TRUE | |
| FRIES_PLAIN | plain fries | 5.00 | 5.00 | Crinkle‑cut fries with a golden, crispy exterior. | TRUE | |

### Modifier Groups
| group_id | item_id | mod_name | min_select | max_select | display_order | group_type |
|----------|---------|----------|-----------|-----------|--------------|------------|
| COW_SAUCES | COWBOY | Extra Sauce | 0 | 6 | 1 | ADD |
| COW_TAKEOFF | COWBOY | Take Off | 0 | 4 | 2 | HOLD |
| COW_NORANCH | COWBOY | No Ranch | 0 | 1 | 3 | SPECIAL |
| COW_ADDONS | COWBOY | Add Ons | 0 | 1 | 4 | ADD |
| COW_COMBO | COWBOY | Combo | 0 | 1 | 5 | ADD |
| COW_REC_SIDE | COWBOY | Recommended Sides and Apps | 0 | 1 | 99 | UPSELL |
| COW_REC_BEV | COWBOY | Recommended Beverage | 0 | 1 | 100 | UPSELL |
| PHILLY_PROTEIN | PHILLY | Protein | 1 | 1 | 1 | REQUIRED |
| PHILLY_ADDONS | PHILLY | Add Ons | 0 | 1 | 2 | ADD |
| PHILLY_TAKEOFF | PHILLY | Take Off | 0 | 4 | 4 | HOLD |
| PHILLY_EXTRAS | PHILLY | Extras | 0 | 3 | 5 | ADD |
| PHILLY_COMBO | PHILLY | Combo | 0 | 1 | 3 | ADD |
| PHILLY_REC_SIDE | PHILLY | Recommended Sides and Apps | 0 | 1 | 99 | UPSELL |
| PHILLY_REC_BEV | PHILLY | Recommended Beverage | 0 | 1 | 100 | UPSELL |
| CLUB_TAKEOFF | CLUB | Take Off | 0 | 4 | 1 | HOLD |
| CLUB_NORANCH | CLUB | No Ranch Toggle | 0 | 1 | 2 | SPECIAL |
| CLUB_COMBO | CLUB | Combo Upgrade | 0 | 1 | 3 | ADD |
| CLUB_ADDONS | CLUB | Add Ons | 0 | 1 | 4 | ADD |
| CLUB_SAUCES | CLUB | Add Sauce | 0 | 1 | 5 | ADD |
| CLUB_REC_SIDE | CLUB | Recommended Sides | 0 | 1 | 99 | UPSELL |
| CLUB_REC_BEV | CLUB | Recommended Beverage | 0 | 1 | 100 | UPSELL |
| FRIED_TAKEOFF | FRIED | Take Off | 0 | 4 | 1 | HOLD |
| FRIED_NORANCH | FRIED | No Ranch | 0 | 1 | 2 | SPECIAL |
| FRIED_COMBO | FRIED | Combo | 0 | 1 | 3 | ADD |
| FRIED_ADDONS | FRIED | Add Ons | 0 | 1 | 4 | ADD |
| FRIED_ADDSAUCE | FRIED | Add Sauce | 0 | 1 | 5 | ADD |
| FRIED_REC_SIDE | FRIED | Recommended Sides | 0 | 1 | 99 | UPSELL |
| FRIED_REC_BEV | FRIED | Recommended Beverage | 0 | 1 | 100 | UPSELL |
| FRIES_STYLE | FRIES | Loaded Fry Style | 1 | 1 | 1 | REQUIRED |
| FRIES_ADDONS | FRIES | Add Ons | 0 | 1 | 2 | ADD |
| FRIES_ADDPROTEIN | FRIES | Add Protein | 0 | 3 | 3 | ADD |
| FRIES_REC_SIDE | FRIES | Recommended Sides | 0 | 1 | 99 | UPSELL |
| FRIES_REC_BEV | FRIES | Recommended Beverage | 0 | 1 | 100 | UPSELL |
| JUICE_FLAVOR | JUICE_CP | Juice Flavor | 1 | 1 | 1 | REQUIRED |
| JUICE_SIZE | JUICE_CP | Juice Size | 1 | 1 | 2 | REQUIRED |
| JUICE_ADD | JUICE_CP | Add Juice | 0 | 1 | 1 | ADD |
| POP_SAUCE | POPPERS | Sauce Style | 0 | 1 | 1 | ADD |
| POP_REC_SIDE | POPPERS | Recommended Sides | 0 | 1 | 99 | UPSELL |
| POP_REC_BEV | POPPERS | Recommended Beverage | 0 | 1 | 100 | UPSELL |
| POP_COMBO | POPPERS | Combo Upgrade | 0 | 1 | 3 | ADD |
| DUMPLING_TAKEOFF | DUMPLING_TACOS | Take Off | 0 | 4 | 1 | HOLD |
| DUMPLING_SUBS | DUMPLING_TACOS | Substitutions | 0 | 1 | 2 | SPECIAL |
| DUMPLING_SAUCE | DUMPLING_TACOS | Choose Sauce | 0 | 1 | 3 | ADD |
| DUMPLING_COMBO | DUMPLING_TACOS | Combo Upgrade | 0 | 1 | 3 | ADD |
| PHILLY_TAKEOFF_2 | PHILLY | Take Off | 0 | 3 | 4 | HOLD |
| ROCK_TAKEOFF | ROCKTOWN_SLIDERS | Take Off | 0 | 1 | 1 | HOLD |
| ROCK_COMBO | ROCKTOWN_SLIDERS | Combo Upgrade | 0 | 1 | 3 | ADD |

### Modifiers (Complete)
| mod_id | group_id | mod_name | base_price | sell_price | price_delta | active |
|--------|----------|----------|-----------|-----------|------------|--------|
| C_COMBO_FRIES | COW_COMBO | fries | 0.00 | 0.00 | 5.00 | TRUE |
| C_COMBO_SALAD | COW_COMBO | side salad | 0.00 | 0.00 | 5.00 | TRUE |
| C_HOLD_BACN | COW_TAKEOFF | no bac'n | 0.00 | 0.00 | 0.00 | TRUE |
| C_HOLD_CHIVES | COW_TAKEOFF | no chives | 0.00 | 0.00 | 0.00 | TRUE |
| C_HOLD_LETTUCE | COW_TAKEOFF | no lettuce | 0.00 | 0.00 | 0.00 | TRUE |
| C_HOLD_TOMATO | COW_TAKEOFF | no tomato | 0.00 | 0.00 | 0.00 | TRUE |
| C_JALAP | COW_ADDONS | jalapenos | 1.00 | 1.00 | 1.00 | TRUE |
| C_NORANCH_FLAG | COW_NORANCH | no ranch | 0.00 | 0.00 | 0.00 | TRUE |
| C_SAUCE_BBQ | COW_SAUCES | bbq | 0.50 | 0.50 | 0.50 | TRUE |
| C_SAUCE_BUFFALO | COW_SAUCES | buffalo | 0.50 | 0.50 | 0.50 | TRUE |
| C_SAUCE_GARLICPARM | COW_SAUCES | garlic parm | 1.00 | 1.00 | 1.00 | TRUE |
| C_SAUCE_JERK | COW_SAUCES | jerk | 0.50 | 0.50 | 0.50 | TRUE |
| C_SAUCE_LEMONPEP | COW_SAUCES | lemon pepper | 1.00 | 1.00 | 1.00 | TRUE |
| C_SAUCE_TRUTH | COW_SAUCES | truth | 0.50 | 0.50 | 0.50 | TRUE |
| C_SUB_GARLICBUTR | COW_NORANCH | add garlic butter instead | 0.00 | 0.00 | 0.00 | TRUE |
| C_SUB_SAUCE_BBQ | COW_NORANCH | add bbq sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| C_SUB_SAUCE_BUFF | COW_NORANCH | add buffalo sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| C_SUB_SAUCE_JERK | COW_NORANCH | add jerk sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| C_SUB_SAUCE_TRTH | COW_NORANCH | add truth sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| DUMP_COMBO_FRIES | DUMPLING_COMBO | fries | 0.00 | 0.00 | 5.00 | TRUE |
| DUMP_COMBO_SALAD | DUMPLING_COMBO | side salad | 0.00 | 0.00 | 5.00 | TRUE |
| DUMP_NO_AIOLI | DUMPLING_TAKEOFF | no aioli | 0.00 | 0.00 | 0.00 | TRUE |
| DUMP_NO_JERK | DUMPLING_TAKEOFF | no jerk | 0.00 | 0.00 | 0.00 | TRUE |
| DUMP_NO_SLAW | DUMPLING_TAKEOFF | no cabbage | 0.00 | 0.00 | 0.00 | TRUE |
| DUMP_SUB_LETTUCE | DUMPLING_SUBS | sub lettuce for cabbage | 0.00 | 0.00 | 0.00 | TRUE |
| F_ADD_JALAP | FRIED_ADDONS | jalapenos | 1.00 | 1.00 | 1.00 | TRUE |
| F_COMBO_FRIES | FRIED_COMBO | fries | 0.00 | 0.00 | 5.00 | TRUE |
| F_COMBO_SALAD | FRIED_COMBO | side salad | 0.00 | 0.00 | 5.00 | TRUE |
| F_NO_BACN | FRIED_TAKEOFF | no bac'n | 0.00 | 0.00 | 0.00 | TRUE |
| F_NO_CHIVES | FRIED_TAKEOFF | no chives | 0.00 | 0.00 | 0.00 | TRUE |
| F_NO_LETTUCE | FRIED_TAKEOFF | no lettuce | 0.00 | 0.00 | 0.00 | TRUE |
| F_NO_RANCH_FLAG | FRIED_NORANCH | no ranch | 0.00 | 0.00 | 0.00 | TRUE |
| F_NO_TOMATO | FRIED_TAKEOFF | no tomato | 0.00 | 0.00 | 0.00 | TRUE |
| F_SAUCE_AIOLI | FRIED_ADDSAUCE | aioli | 0.75 | 0.75 | 0.75 | TRUE |
| F_SAUCE_BBQ | FRIED_ADDSAUCE | bbq | 0.50 | 0.50 | 0.50 | TRUE |
| F_SAUCE_BUFFALO | FRIED_ADDSAUCE | buffalo | 0.50 | 0.50 | 0.50 | TRUE |
| F_SAUCE_GARLICPARM | FRIED_ADDSAUCE | garlic parm | 2.50 | 2.50 | 2.50 | TRUE |
| F_SAUCE_JERK | FRIED_ADDSAUCE | jerk | 0.50 | 0.50 | 0.50 | TRUE |
| F_SAUCE_LEMONPEP | FRIED_ADDSAUCE | lemon pepper wet | 0.75 | 0.75 | 0.75 | TRUE |
| F_SAUCE_TRUTH | FRIED_ADDSAUCE | truth | 0.50 | 0.50 | 0.50 | TRUE |
| F_SUB_BBQ | FRIED_NORANCH | add bbq sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| F_SUB_BUFFALO | FRIED_NORANCH | add buffalo sauce | 0.00 | 0.00 | 0.00 | TRUE |
| F_SUB_GARLICBUTR | FRIED_NORANCH | add garlic butter instead | 0.00 | 0.00 | 0.00 | TRUE |
| F_SUB_JERK | FRIED_NORANCH | add jerk sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| F_SUB_TRUTH | FRIED_NORANCH | add truth sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| FRIES_ADD_BACN | FRIES_ADDPROTEIN | add bac'n | 1.50 | 1.50 | 1.50 | TRUE |
| FRIES_ADD_CHIKN | FRIES_ADDPROTEIN | add chik'n | 2.00 | 2.00 | 2.00 | TRUE |
| FRIES_ADD_JALAP | FRIES_ADDONS | jalapeños | 1.00 | 1.00 | 1.00 | TRUE |
| FRIES_ADD_STEK | FRIES_ADDPROTEIN | add stek | 2.00 | 2.00 | 2.00 | TRUE |
| FRIES_BACN | FRIES_STYLE | bac'n | 0.00 | 0.00 | 0.00 | TRUE |
| FRIES_CHIKN | FRIES_STYLE | philly chik'n | 0.00 | 0.00 | 0.00 | TRUE |
| FRIES_GARLICPARM | FRIES_STYLE | garlic parm | 0.00 | 0.00 | 0.00 | TRUE |
| FRIES_PLAIN | FRIES_STYLE | plain fries | 0.00 | 0.00 | 0.00 | TRUE |
| FRIES_STEK | FRIES_STYLE | philly stek | 0.00 | 0.00 | 0.00 | TRUE |
| JFLAV_GREEN | JUICE_FLAVOR | green juice | 0.00 | 0.00 | 0.00 | TRUE |
| JFLAV_ORANGE | JUICE_FLAVOR | orange machine | 0.00 | 0.00 | 0.00 | TRUE |
| JFLAV_SWEET | JUICE_FLAVOR | sweet dreams | 0.00 | 0.00 | 0.00 | TRUE |
| P_CHIKN | PHILLY_PROTEIN | chik'n | 0.00 | 0.00 | 0.00 | TRUE |
| P_STEK | PHILLY_PROTEIN | stek | 0.00 | 0.00 | 0.00 | TRUE |
| P_EXTRA_CHIKN | PHILLY_EXTRAS | extra chikn | 0.00 | 0.00 | 2.00 | TRUE |
| P_EXTRA_STEK | PHILLY_EXTRAS | extra stek | 0.00 | 0.00 | 2.00 | TRUE |
| P_EXTRA_AIOLI | PHILLY_EXTRAS | extra aioli | 0.00 | 0.00 | 0.75 | TRUE |
| P_EXTRA_CHEZE | PHILLY_EXTRAS | extra cheze | 0.00 | 0.00 | 1.00 | TRUE |
| P_EXTRA_ONIONS | PHILLY_EXTRAS | extra onions | 0.00 | 0.00 | 1.00 | TRUE |
| P_EXTRA_PEPPERS | PHILLY_EXTRAS | extra peppers | 0.00 | 0.00 | 1.00 | TRUE |
| P_JALAP | PHILLY_ADDONS | jalapeños | 0.00 | 0.00 | 1.00 | TRUE |
| P_COMBO_FRIES | PHILLY_COMBO | fries | 0.00 | 0.00 | 5.00 | TRUE |
| P_COMBO_SALAD | PHILLY_COMBO | side salad | 0.00 | 0.00 | 5.00 | TRUE |
| P_HOLD_PEPPERS_ONIONS | PHILLY_TAKEOFF | no peppers and onions | 0.00 | 0.00 | 0.00 | TRUE |
| P_NO_AIOLI | PHILLY_TAKEOFF | no aioli | 0.00 | 0.00 | 0.00 | TRUE |
| P_NO_CHEZE | PHILLY_TAKEOFF | no cheze | 0.00 | 0.00 | 0.00 | TRUE |
| P_NO_PARSLEY | PHILLY_TAKEOFF | no parsley | 0.00 | 0.00 | 0.00 | TRUE |
| P2_NO_CHEESE | PHILLY_TAKEOFF_2 | no cheese | 0.00 | 0.00 | 0.00 | TRUE |
| P2_NO_ONIONS | PHILLY_TAKEOFF_2 | no onions | 0.00 | 0.00 | 0.00 | TRUE |
| P2_NO_PEPPERS | PHILLY_TAKEOFF_2 | no peppers | 0.00 | 0.00 | 0.00 | TRUE |
| POP_BUFFALO | POP_SAUCE | buffalo | 0.00 | 0.00 | 0.00 | TRUE |
| POP_COMBO_FRIES | POP_COMBO | fries | 0.00 | 0.00 | 5.00 | TRUE |
| POP_COMBO_SALAD | POP_COMBO | side salad | 0.00 | 0.00 | 5.00 | TRUE |
| POP_GARLIC | POP_SAUCE | garlic parm | 0.00 | 0.00 | 0.00 | TRUE |
| POP_JERK | POP_SAUCE | jerk | 0.00 | 0.00 | 0.00 | TRUE |
| POP_LEM_WET | POP_SAUCE | lemon pepper wet | 0.00 | 0.00 | 0.00 | TRUE |
| POP_PLAIN | POP_SAUCE | plain | 0.00 | 0.00 | 0.00 | TRUE |
| R_COMBO_FRIES | ROCK_COMBO | fries | 0.00 | 0.00 | 5.00 | TRUE |
| R_COMBO_SALAD | ROCK_COMBO | side salad | 0.00 | 0.00 | 5.00 | TRUE |
| ROCK_NO_AIOLI | ROCK_TAKEOFF | no aioli | 0.00 | 0.00 | 0.00 | TRUE |
| ROCK_NO_CABBAGE | ROCK_TAKEOFF | no cabbage | 0.00 | 0.00 | 0.00 | TRUE |
| S_COMBO_FRIES | CLUB_COMBO | fries | 0.00 | 0.00 | 5.00 | TRUE |
| S_COMBO_SALAD | CLUB_COMBO | side salad | 0.00 | 0.00 | 5.00 | TRUE |
| S_HOLD_BACN | CLUB_TAKEOFF | no bac'n | 0.00 | 0.00 | 0.00 | TRUE |
| S_HOLD_CHIVES | CLUB_TAKEOFF | no chives | 0.00 | 0.00 | 0.00 | TRUE |
| S_HOLD_LETTUCE | CLUB_TAKEOFF | no lettuce | 0.00 | 0.00 | 0.00 | TRUE |
| S_HOLD_TOMATO | CLUB_TAKEOFF | no tomato | 0.00 | 0.00 | 0.00 | TRUE |
| S_JALAP | CLUB_ADDONS | jalapenos | 0.00 | 0.00 | 1.00 | TRUE |
| S_NORANCH_FLAG | CLUB_NORANCH | no ranch | 0.00 | 0.00 | 0.00 | TRUE |
| S_SAUCE_BBQ | CLUB_SAUCES | bbq | 0.00 | 0.00 | 0.50 | TRUE |
| S_SAUCE_BUFFALO | CLUB_SAUCES | buffalo | 0.00 | 0.00 | 0.50 | TRUE |
| S_SAUCE_GARLICPARM | CLUB_SAUCES | garlic parm | 0.00 | 0.00 | 2.50 | TRUE |
| S_SAUCE_JERKS | CLUB_SAUCES | jerk | 0.00 | 0.00 | 0.50 | TRUE |
| S_SAUCE_LEMONPEP | CLUB_SAUCES | lemon pepper | 0.00 | 0.00 | 1.00 | TRUE |
| S_SAUCE_TRUTH | CLUB_SAUCES | truth | 0.00 | 0.00 | 0.50 | TRUE |
| S_SUB_GARLICBUTR | CLUB_NORANCH | add garlic butter instead | 0.00 | 0.00 | 0.00 | TRUE |
| S_SUB_SAUCE_BBQ | CLUB_NORANCH | add bbq sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| S_SUB_SAUCE_BUFF | CLUB_NORANCH | add buffalo sauce | 0.00 | 0.00 | 0.00 | TRUE |
| S_SUB_SAUCE_JERK | CLUB_NORANCH | add jerk sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| S_SUB_SAUCE_TRTH | CLUB_NORANCH | add truth sauce instead | 0.00 | 0.00 | 0.00 | TRUE |
| UPS_JUICE | JUICE_ADD | fresh cold press juices | 5.00-10.00 | 5.00-10.00 | 0.00 | TRUE |

## Modifier Group Types Explained

| Type | Description | Example |
|------|-------------|---------|
| REQUIRED | Must select exactly min-max | Protein choice (PHILLY_PROTEIN) |
| ADD | Optional additions | Extra sauce, jalapenos |
| HOLD | Remove ingredients | No lettuce, no tomato |
| SPECIAL | Toggle/replace | No ranch → add garlic butter |
| UPSELL | Suggested add-ons | Recommended sides, drinks |

## Key Implementation Notes

1. **max_select enforcement:** UI must prevent selecting more than max (e.g., max 6 sauces)
2. **min_select validation:** Required groups must have at least min before add to cart
3. **price_delta vs base_price:** price_delta is what adds to item price, base_price is standalone
4. **group_type logic:** Different UI treatment per type (radio for required, checkbox for add/hold)
5. **display_order:** Controls UI ordering, 99/100 are upsell sections (show last)

## Next Steps for Modifier Implementation

1. Build GROUP_RULES lookup from this data
2. Update toggleMod() to enforce max_select
3. Add validateRequiredGroups() before addTo cart
4. Flatten modifiers array for payload
5. Compute total price with modifier deltas
