# Prompt: Slice Lab, an interactive "Build Your Pizza" landing page

Paste this whole file into Claude Code in a folder that contains `hero-pizza.webp` to rebuild the site from scratch.

## Role
You are a senior front-end developer and UI/UX designer who specializes in playful, game-like web experiences. Build a polished, production-quality landing page for a pizza brand. Visitors create their own pizza by dragging ingredients onto a pizza base, like a mini game, then confirm it and place an order.

## Deliverable
- One self-contained `index.html` (HTML + CSS + vanilla JavaScript, no frameworks, no build step).
- The hero uses the photo `hero-pizza.webp` (900×900 with a transparent background) that is already in the folder. Draw everything else (the builder pizza, the 12 topping icons, UI icons) with inline SVG or CSS so the page works offline.
- Google Fonts: **Poppins** (600, 700, 800) and **DM Sans** (400, 500, 700).
- Clean, commented code organized into clear sections: config, icons, state, pricing, pizza drawing, render, drag & drop, order flow, events.
- Also write a `CLAUDE.md` describing the files, code map, design tokens and rules.

---

## 1. Design system ("Slice Lab")
The style direction is bold fast-food: one confident red band, a mustard bar and mustard buttons, calm cream pages with white cards.

Define these as CSS variables on `:root` and use only them:

| Token | Value | Rule |
|---|---|---|
| `--flame-red` | #C8191F | Hero band, feature tiles, order bar. White text only. |
| `--red-deep` | #9E1217 | Hover and pressed red, red text on cream |
| `--mustard` | #FFC20E | Nav bar, primary buttons. Ink text only, never white. |
| `--mustard-deep` | #E5A800 | Mustard hover |
| `--bun-cream` | #F7F0E4 | Page background |
| `--toast` | #F4DDB2 | Warm panels, behind food images |
| `--card-white` | #FFFFFF | Cards, inputs |
| `--ink` | #1C1410 | Text |
| `--ink-muted` | #5E5149 | Secondary text (not on red or mustard) |
| `--line` | #E8DCC8 | Hairlines |
| `--radius-sm` / `-md` / `-lg` | 6px / 14px / 20px | Buttons and tags / cards and inputs / tiles and panels |
| `--shadow-card` | 0 8px 20px rgba(28,20,16,.10) | Cards on cream only |

- **Type:** Poppins 800 for headlines (tight leading, slight negative letter-spacing), 700 for card titles and prices; DM Sans for body text.
- **Shapes:** buttons are rounded rectangles (`--radius-sm`), not pills. Primary buttons are mustard with ink text and a subtle bottom inset shadow.
- **Signature motif, "the notch":** a small downward triangle in the block's own color that hangs under the hero band and under each "How it works" tile, like a speech bubble.
- **Brand name:** "Slice Lab", set in one `CONFIG.brand` variable.
- **Logo:** a red disc with a small mustard and cream pizza slice inside (inline SVG), plus the name in Poppins 800.
- Smooth micro-animations everywhere, but nothing that slows the user down.

---

## 2. Page structure (top to bottom)

### 2.1 Navbar
- Sticky, mustard (`rgba(255,194,14,.94)` with a backdrop blur), 68px tall.
- Logo on the left; links on the right: Build, How it works, Menu (scrolls to the Chef's picks), Contact.
- A red cart button with a bag icon that shows the live total (animated). Clicking it scrolls to the order summary.
- Under 720px, hide the links and keep the logo and cart.

### 2.2 Hero (full-width red band)
- Background `--flame-red`, white text, with the notch centered under the band.
- Small eyebrow pill: "Build-your-own pizza, baked to order" (translucent white, mustard dot).
- Headline: **"Build the pizza of your dreams."**, with "dreams." in mustard.
- Subheadline: "Drag, drop, and bake. Your perfect pizza is just a few moves away."
- Primary CTA **"Start Building"** (smooth-scrolls to the builder), plus a white underlined "How it works" text link.
- On the right, `hero-pizza.webp` with a deep drop shadow, gently floating and tilting (8s loop, ±2deg). Five small SVG toppings float around it (basil, cherry tomato, pepperoni, mushroom, olive).
- Under 960px, stack the hero and center the text.

### 2.3 How it works: 3 category tiles (red, mustard, red), each with a notch
1. **Pick your base:** Choose the size, crust, and sauce that start your pizza.
2. **Drag your toppings:** Drop them right onto the pizza and watch it fill up.
3. **Bake & order:** Confirm it and we will deliver it hot to your door.

Each tile has a white rounded icon square with a red line icon, a faint big step number in the corner, a title and one line of text.

### 2.4 The pizza builder (see Section 3) on a `--toast` band

### 2.5 Social proof
- 3 testimonial cards: 5 stars, quote, avatar initials (avatars in red, mustard with ink text, and ink).
  - "I added pineapple just to unlock the badge. Zero regrets, it was delicious." (Amira K., Pineapple Rebel)
  - "My kids build theirs, I build mine. Friday-night pizza arguments are officially over." (Daniel R., Family of four)
  - "Thin crust, pesto, double basil. It showed up exactly the way I built it." (Sofia M., Thin-crust loyalist)
- A stats row of 3 ink tiles with mustard numbers: "10,000+ pizzas built", "4.9★ average rating", "30 min delivery".
- A small note: "Sample reviews and stats. Swap in your real ones before launch."

### 2.6 Footer (ink background)
Logo, tagline "Your pizza, built your way and baked to order.", 3 social icon buttons, opening hours (Mon – Thu · 11:00 – 22:00, Fri – Sun · 11:00 – 23:30), contact placeholders ([Street address, City], [Phone number], hello@[yourdomain].com), Explore links, and a copyright line with the current year.

---

## 3. The pizza builder (mini game)

### 3.1 Layout
- **Desktop:** a grid with areas `"base tools tray" / "base stage tray" / "sum sum sum"` and columns 270px / 1fr / 300px.
  - Left: a white panel "Pick your base".
  - Center: the toolbar and presets above the pizza.
  - Right: a white panel "Toppings".
  - Bottom: a red order summary bar across the full width.
- **Mobile (≤960px):** one column ordered stage, tray, tools, base, summary.
  - The pizza stage is sticky under the nav (max 260px wide).
  - The tray becomes a horizontal swipe row; tiles use `touch-action: pan-x` so a sideways swipe scrolls and an upward drag picks a topping up.
  - The base options collapse behind a "Base · Medium, Classic, Tomato" toggle.

### 3.2 Base options (pill buttons, selected = red with white text)
- **Size:** Small (25cm, $8), Medium (30cm, $11), Large (35cm, $14). The pizza scales to 0.8 / 0.9 / 1 with a springy transition.
- **Crust:** Classic, Thin, Stuffed (+$2). It changes the crust width and color; stuffed adds an inner ring.
- **Sauce:** Tomato #C9342A, BBQ #7B2F1B, White Garlic #F1E3C8, Pesto #5D8B2F, each shown with a color swatch.
- **Cheese:** Mozzarella, Extra Mozzarella (+$1.50, bigger cheese spots), Vegan Cheese (+$1, paler), No Cheese.

### 3.3 Ingredients (draggable)

| Topping | Price | Group |
|---|---|---|
| Pepperoni | $1.50 | Meats |
| Mushrooms | $1.00 | Veggies |
| Green Peppers | $0.80 | Veggies |
| Black Olives | $0.90 | Veggies |
| Red Onions | $0.70 | Veggies |
| Fresh Basil | $0.60 | Extras |
| Cherry Tomatoes | $1.00 | Veggies |
| Sweet Corn | $0.70 | Veggies |
| Grilled Chicken | $2.00 | Meats |
| Jalapeños | $0.90 | Extras |
| Pineapple | $1.00 | Extras |
| Beef / Sausage | $1.80 | Meats |

- Filter tabs: **All · Meats · Veggies · Extras**.
- Each tray tile works like a mini product card:
  - the SVG icon sits on a `--toast` strip;
  - below it, the name and the size-adjusted price;
  - once the topping is on the pizza, a red "×1"/"×2" tag shows in the top-left and the tile border turns red.

### 3.4 Drag & drop (core interaction)
- Use **Pointer Events** (not the HTML5 drag API) so mouse, touch and pen behave the same. Call `preventDefault` on mouse pointerdown so dragging never selects page text.
- A drag starts after 6px of movement; otherwise it's a tap.
- While dragging:
  - a 72px ghost of the topping follows the pointer, scaled up 1.2× with a drop shadow;
  - the pizza shows a dashed ring, which turns into a red glowing ring when the pointer is over the pizza.
- **Drop on the pizza:**
  - scatter 6–10 pieces of that topping across the sauce area, never on the crust, with best-candidate random placement so the pieces spread out;
  - pieces get random rotation and size (0.9–1.12) and land with a staggered bounce;
  - a short "pop" sound plays, made with Web Audio (no audio file), and can be muted.
- **Drop the same topping again:** adds an extra portion (5–7 more pieces, price added again). Max 2 portions.
- **Drop outside:** the ghost slides back to its tray tile.
- **Tap, click, Enter or Space on a tile:** adds the topping the same way.

### 3.5 Removing and editing
- Tap a piece on the pizza: that topping glows, the others dim, and a dark "× Remove Mushrooms" bubble appears above it.
- Drag a piece off the pizza: a "Release to remove" label shows; on release, the topping is removed and the ghost flies back to its tile and fades.
- The summary bar lists each topping as a chip (icon, name, ×2, price) with an × button.
- **Clear all:** an in-page confirm popover "Start over? This removes all toppings." (never `window.confirm`).
- **Undo:** keeps the last 10 actions, base changes included.

### 3.6 Game feel
- The live total counts up or down (420ms ease-out) and bumps wherever it's shown.
- **Topping limit:** max 8 different toppings. On the 9th, the pizza shakes and a toast says "Whoa, that's a loaded pizza! Max 8 toppings."
- **Badge toasts** (white with a mustard border, "BADGE UNLOCKED" label):
  - "Veggie Lover 🌱": 4+ toppings, no meat
  - "Meat Feast 🥩": 3+ meats
  - "Spicy Chef 🌶️": jalapeños
  - "Pineapple Rebel 🍍": pineapple
- **"Surprise Me":** builds a random but sensible pizza (BBQ goes with chicken, pesto with basil, 0–1 meat otherwise, 4–5 toppings), with toppings flying from their tiles onto the pizza one by one.
- **Chef's picks:**
  - Margherita: tomato sauce, cherry tomatoes, basil
  - Pepperoni Classic: double pepperoni
  - Veggie Supreme: mushrooms, peppers, olives, onions, tomatoes
  - BBQ Chicken: BBQ sauce, chicken, onions, peppers, corn
- Confetti in brand colors when the pizza comes out of the oven.

### 3.7 Pricing
- Base by size: Small $8, Medium $11, Large $14. Stuffed crust +$2, Extra Mozzarella +$1.50, Vegan Cheese +$1.
- Topping prices scale with size: Small ×1, Medium ×1.25, Large ×1.5. Round to cents with `Math.round(n * 100 + 1e-6) / 100`, so $1.875 becomes $1.88.
- Quantity stepper from 1 to 10.
- Everything lives in one `CONFIG` object at the top of the script.

---

## 4. Order flow
1. **"Bake My Pizza"** (mustard button in the red summary bar) plays the oven animation:
   - the pizza sits on a wooden peel under a dark oven, then slides up into the arched oven mouth (~0.7s);
   - the glass glows orange and flickers for ~1.5s;
   - the pizza comes back out golden (darker crust, golden cheese, a few toasted spots) with three rising steam lines and confetti.
2. **Review modal:**
   - the baked pizza on a `--toast` square;
   - an itemized receipt: base, crust, sauce, cheese, each topping with its price, per-pizza price, quantity;
   - the total in large red;
   - buttons "Edit pizza" (closes and keeps everything) and "Continue to order".
3. **Order form:**
   - fields: Full name, Phone number, a Delivery or Pickup segmented toggle, Delivery address (shown only for delivery), Notes (placeholder "extra crispy please");
   - payment choice only: Cash or Card on delivery (or "at pickup"), with no card fields;
   - inline friendly errors on submit that re-check live as the user types;
   - submit button "Place order · $total".
4. **Confirmation:**
   - "Your pizza is in the oven! 🍕", "Thanks, {first name}.";
   - an order number like `#SL-48213`;
   - an ETA (delivery 25–35 min, pickup 15–20 min);
   - a 4-step tracker (Received → Baking → Out for delivery → Delivered) with the first step active and pulsing;
   - "Build another pizza" resets the builder.

Front-end demo only: send nothing to a server. `console.log` the final order object as JSON (number, date, pizza, toppings with prices, pricing, customer, fulfillment, payment, notes).

---

## 5. Technical requirements
- Vanilla JS only. Keep one `state` object; change it through an `update()` function and re-render from it.
- The builder pizza adds and removes pieces by id, so existing pieces don't re-animate.
- Save the pizza to `localStorage` (key `slice-lab-pizza-v1`), wrapped in try/catch.
- Responsive at 375, 768, 1280 and 1440px, with no horizontal scroll on mobile.
- Animate only `transform` and `opacity`; keep dragging at 60fps.
- Respect `prefers-reduced-motion`.

## 6. Accessibility
- Every control is a real `<button>`, reachable by keyboard, with visible focus styles.
- An `aria-live` region announces changes ("Mushrooms added. Total: $13.50.").
- WCAG AA contrast (follow the token rules above).
- `aria-label` on icon-only buttons and on the topping tiles.
- Escape closes the review and the form.

## 7. Quality checklist (verify before finishing)
- [ ] Drag & drop works with mouse and touch, and dragging never selects text.
- [ ] Toppings never land outside the pizza or on the crust.
- [ ] Prices are always right (Medium + Pepperoni = $12.88).
- [ ] Undo, remove, Clear all, Surprise Me and presets all work.
- [ ] The order flow works from start to confirmation and can be restarted.
- [ ] It looks great on mobile and desktop.
- [ ] No console errors.
