# Slice Lab — project notes for Claude

Slice Lab is a landing page for a pizza brand where visitors build their own pizza by dragging toppings onto it, like a mini game, then bake it and place an order. It is a front-end demo: nothing is sent to a server.

The owner is a junior web designer who is new to coding tools. Explain changes in simple, beginner-friendly words, and say what you changed and where.

## Files

- `index.html` is the whole site in one file: HTML, CSS in `<style>`, vanilla JavaScript in `<script>`. No frameworks and no build step.
- `hero-pizza.webp` is the pizza photo in the hero (900×900 with a transparent background).
- `PROMPT.md` is the full original spec, for reference or for rebuilding from scratch.

To preview, open `index.html` in a browser. A local server also works: `npx serve .`.

## How the code is organized (inside index.html)

**CSS**
- `:root` holds the design tokens (colors, radii, fonts). Change brand colors here only.
- Sections follow the page order: nav, hero, how it works, builder, pizza drawing, toasts and ghost, overlays (oven, review, form, done), social proof, footer, then responsive rules and reduced motion.

**HTML**
- Order: nav, hero, how it works, builder (`#build`), reviews, footer, and the four order-flow overlays (`#ov-bake`, `#ov-review`, `#ov-form`, `#ov-done`), which start `hidden`.

**JavaScript**, top to bottom:
1. **`CONFIG`:** brand name, order prefix, currency, sizes, crusts, sauces, cheeses, toppings, presets and limits. All prices live here.
2. **`ICONS`:** inline SVG for each topping, keyed by topping id (viewBox 0 0 40 40). A new topping needs an entry here and in `CONFIG.toppings`.
3. **State:** one `state` object. Every change goes through `update(patch)`, which calls `render(prev)`. Use `commit(fn, message)` for user actions that should be undoable (it saves a snapshot to `history`, max 10).
4. **Pricing:** `calc(state)` returns every line and the total. The total animates via `tween()`.
5. **Pizza drawing:** `pizzaLook()` gives the crust, sauce and cheese visuals. `scatter()` places pieces inside the sauce area, never on the crust. `renderPizza()` updates the builder pizza and adds or removes pieces by `uid`, so only new pieces play the landing bounce.
6. **Render:** `build()` runs once (pills, presets, tray). `render(prev)` updates everything from state.
7. **Drag & drop:** Pointer Events (`startDrag`, `onMove`, `onUp`), so mouse, touch and pen behave the same. Do not switch to the HTML5 drag API.
8. **Order flow:** `startBake()` runs the oven stages, then review, form and confirmation. `placeOrder()` logs the order object as JSON to the console.
9. **Events:** one delegated click handler maps `data-action="..."` buttons to `ACTIONS`.

## Design system (Slice Lab)

The look is bold fast-food: a red hero band, a mustard nav and buttons, cream pages, white cards.

| Token | Value | Use |
|---|---|---|
| `--flame-red` | #C8191F | Hero band, feature tiles, order bar. **White text only.** |
| `--red-deep` | #9E1217 | Hover and pressed red; red text on cream |
| `--mustard` | #FFC20E | Nav bar, primary buttons. **Ink text only, never white.** |
| `--mustard-deep` | #E5A800 | Mustard hover |
| `--bun-cream` | #F7F0E4 | Page background |
| `--toast` | #F4DDB2 | Warm panels, behind food images |
| `--card-white` | #FFFFFF | Cards, inputs |
| `--ink` | #1C1410 | Text |
| `--ink-muted` | #5E5149 | Secondary text (not on red or mustard) |
| `--line` | #E8DCC8 | Hairline borders |

- **Fonts:** Poppins 700–800 for headlines, card titles and prices (`.disp`); DM Sans for body text. Both load from Google Fonts.
- **Shapes:** buttons and tags use `--radius-sm` (6px, rounded rectangles, not pills). Cards and inputs use `--radius-md` (14px). Tiles and panels use `--radius-lg` (20px). Cards get `--shadow-card`. Red and mustard blocks get no shadow.
- **Signature motif, "the notch":** a small downward triangle in the block's own color, under the hero band and under the "How it works" tiles. Use it only for hero bands and category tiles.
- **Balance:** at most one big red band per screen height. Most of the page is cream with white cards, and red and mustard are accents.
- **Copy:** short, punchy, food-first headlines; talk to "you"; buttons say what happens.
- **Icons:** simple line icons with a 2px stroke. No emoji in the UI, except the badge names and the confirmation title, which the spec asks for.

## Rules to keep

- Keep it a single `index.html` with vanilla JS unless the owner asks otherwise.
- Every price lives in `CONFIG`; never hard-code a price in the markup.
- Accessibility: real `<button>` elements, visible focus, `aria-label` on icon-only buttons, announcements through `say()` (the `#announce` live region), text contrast of at least 4.5:1.
- Animate only `transform` and `opacity`, and respect `prefers-reduced-motion` (the `REDUCED` flag plus the CSS media query).
- Wrap all `localStorage` use in try/catch (key `slice-lab-pizza-v1`).
- No network requests besides Google Fonts. The order is only `console.log`ged.
- Reviews, stats, opening hours and contact details are placeholders. The page says so; keep that note until real ones are added.

## Checklist after any change

- [ ] Dragging works with mouse and touch; a drop outside the pizza slides back to the tray.
- [ ] Pieces never land outside the pizza or on the crust.
- [ ] Prices are right after any change (e.g. Medium + Pepperoni = $11 + $1.88 = $12.88).
- [ ] Undo, remove (bubble, chip ×, drag off), Clear all, Surprise Me and presets all work.
- [ ] The order flow runs from Bake to confirmation and "Build another pizza" resets.
- [ ] No horizontal scroll at 375px; looks right at 768, 1280 and 1440px.
- [ ] No console errors.
