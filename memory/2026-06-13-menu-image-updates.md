# Menu Image Update Log - 2026-06-13

## Changes Made

### Images Added/Corrected
- `buffalo_chikn_slider.jpg` - Buffalo Chik'n Sliders (buffalo sauce drizzle on bun)
- `rocktown_bourbon_slider.jpg` - Rocktown Bourbon Chik'n Sliders (2 sliders with pickles)
- `chicken_fried_chikn_sub.png` - Chik'n Fried Chik'n Sub (fried chicken in hoagie + fries)

### Remote v2 Images (Merged)
- `bourbon_sliders_v2.jpg` - IDENTICAL to `rocktown_bourbon_slider.jpg` (MD5 match)
- `chicken_fried_sub_v2.jpg` - IDENTICAL to `buffalo_chikn_slider.jpg` (MD5 match) - misnamed by remote
- Other v2 images: bottled_water_v2.png, chicken_poppers_v3.jpg, cold_pressed_juice_v2.jpg, cookies_v2.jpg, garlic_parm_fries_v2.jpg, side_salad_v2.png, spiral_chips.jpg

### Files Updated
1. **pickup-order/menu-data.js**
   - Rocktown Bourbon Sliders: `../images/legacy/IMG_2627.WEBP` → `../images/rocktown_bourbon_slider.jpg`
   - Buffalo Chik'n Sliders: `../images/legacy/IMG_2627.WEBP` → `../images/buffalo_chikn_slider.jpg`
   - Chik'n Fried Chik'n Sub: `../images/chicken_fried_chicken_sub.jpg` → `../images/chicken_fried_chikn_sub.png`

2. **menu-data.js** (root)
   - Rocktown Bourbon Sliders: `images/legacy/IMG_2627.WEBP` → `images/rocktown_bourbon_slider.jpg`
   - Buffalo Chik'n Sliders: `images/legacy/IMG_2627.WEBP` → `images/buffalo_chikn_slider.jpg`
   - Chik'n Fried Chik'n Sub: `images/chicken_fried_chicken_sub.jpg` → `images/chicken_fried_chikn_sub.png`

3. **utopia-deli-revamp/menu-data.js**
   - Rocktown Bourbon Sliders: `images/logo.png` → `images/rocktown_bourbon_slider.jpg`
   - Buffalo Chik'n Sliders: `images/logo.png` → `images/buffalo_chikn_slider.jpg`
   - Chik'n Fried Chik'n Sub: `images/chicken_fried_chicken_sub.jpg` → `images/chicken_fried_chikn_sub.png`

## Important Discovery
The remote's `chicken_fried_sub_v2.jpg` is actually the Buffalo Chik'n Slider image (MD5 matches `buffalo_chikn_slider.jpg`).
The remote's `bourbon_sliders_v2.jpg` is identical to our `rocktown_bourbon_slider.jpg`.

## Key Mapping (Saved to Memory)
- "Spiral chips" = Potato Chip Spirals (menu item)
- Buffalo Chik'n Slider image: `images/buffalo_chikn_slider.jpg`
- Rocktown Bourbon Slider image: `images/rocktown_bourbon_slider.jpg`
- Chik'n Fried Chik'n Sub image: `images/chicken_fried_chikn_sub.png`

## Status: ALL IMAGES CORRECTED AND PUSHED ✅
