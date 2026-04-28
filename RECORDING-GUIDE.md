# Test-dev Recording Guide

A complete reference for recording the 6-minute walkthrough of the four required changes.

---

## 1. Project Overview

- **Repo:** https://github.com/yugin0120/Test-dev
- **Stack:** React 18 + Vite + Tailwind CSS + Framer Motion + react-mouse-follower
- **Run dev server:** `npm install` then `npm run dev` → `http://localhost:5173`

---

## 2. Files You Will Edit

```
Test-dev/
├── src/
│   ├── components/
│   │   ├── ProductDisplay.jsx   ← Task 1: Star rating colors
│   │   ├── ProductList.jsx      ← Task 2: Grid columns (4 → 3)
│   │   └── Item.jsx             ← Task 4: Hover zoom + top-to-bottom mask
│   └── index.css                ← Task 3: Cursor shape override
└── (no other files needed)
```

That's only **4 files** total. Keep the editor's file tree visible during recording so the viewer can follow along.

---

## 3. Where to Find Each Change in the Browser

| Task | Page to navigate to during recording |
|------|--------------------------------------|
| Stars yellow | Click any product card → `/products/:id` (single product view) |
| 3 columns | Click "Mens" / "Womens" / "Kids" in nav → `/mens` etc. |
| Cursor shape | Visible everywhere, demo on home page |
| Product hover animation | `/mens` (or any category page) — hover over any product card |

---

## 4. Step-by-Step Code Changes

### TASK 1 — Stars to Yellow

**File:** `src/components/ProductDisplay.jsx`

**Current (lines 26–30):**
```jsx
<Star fill='#138695' />
<Star fill='#138695' />
<Star fill='#138695' />
<Star fill='#138695' />
<Star fill='gray' />
```

**Replace with:**
```jsx
<Star fill='#FACC15' color='#FACC15' />
<Star fill='#FACC15' color='#FACC15' />
<Star fill='#FACC15' color='#FACC15' />
<Star fill='#FACC15' color='#FACC15' />
<Star fill='#FACC15' color='#FACC15' />
```

**Why:** `lucide-react` icons need both `fill` (interior) and `color` (stroke) for a solid yellow look. `#FACC15` is Tailwind's `yellow-400`. Use `'gold'` or `'yellow'` if you want named colors.

> Note: the empty/gray fifth star becomes filled yellow with this change. If you want the 5th to remain unfilled, leave it as `<Star color='#FACC15' />` (no `fill`).

---

### TASK 2 — Products Grid: 4 → 3 Columns

**File:** `src/components/ProductList.jsx` (line 10)

**Current:**
```jsx
<div className='mt-10 grid grid-cols-1 gap-x-6 gap-y-10 px-6 md:px-0 sm:grid-cols-2 lg:grid-cols-4 xl:gap-x-8'>
```

**Replace with:**
```jsx
<div className='mt-10 grid grid-cols-1 gap-x-6 gap-y-10 px-6 md:px-0 sm:grid-cols-2 lg:grid-cols-3 xl:gap-x-8'>
```

**Single change:** `lg:grid-cols-4` → `lg:grid-cols-3`.

---

### TASK 3 — Cursor Shape (Not a Circle)

The project uses `react-mouse-follower` which renders a follower div with `id="mouse-follower"` and `border-radius: 9999px` (perfect circle). Override it with CSS.

**File:** `src/index.css`

**Append at the very bottom of the file:**
```css
/* Custom cursor shape — square rotated 45° (diamond) instead of a circle */
#mouse-follower,
#mouse-follower > div,
#mouse-follower > div > div {
  border-radius: 0 !important;
}

#mouse-follower {
  transform: rotate(45deg) !important;
}
```

**Why this works:** the library applies `borderRadius: '9999px'` inline, so we use `!important` to override it. The 45° rotation turns the square into a diamond — clearly not a circle, and looks intentional.

> Alternatives if you prefer a different shape:
> - Star: use `clip-path: polygon(...)` instead of `border-radius: 0`
> - Triangle: `clip-path: polygon(50% 0%, 0% 100%, 100% 100%)`
> - Hexagon: `clip-path: polygon(25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%, 0% 50%)`

---

### TASK 4 — Hover Zoom + Top-to-Bottom Light-Gray Mask

**File:** `src/components/Item.jsx`

**Current (full file):**
```jsx
import React from 'react'
import { Link } from 'react-router-dom'

const Item = ({product}) => {
  return (
    <div className='group relative'>
      <Link to={`/products/${product?.id}`}>
      <div className='aspect-h-1 aspect-w-1 w-full overflow-hidden rounded-md bg-gray-800/50 lg:aspect-none grup-hover:opacity-75 lg:h-80 h-96 border border-gray-700/50'>
        <img src={product?.image} alt={product?.name} className='h-full w-full object-cover object-center lg:h-full lg:w-full' />
      </div>
      </Link>
      ...
```

**Replace the inner `<div>` and `<img>` with:**
```jsx
<div className='relative aspect-h-1 aspect-w-1 w-full overflow-hidden rounded-md bg-gray-800/50 lg:aspect-none lg:h-80 h-96 border border-gray-700/50'>
  <img
    src={product?.image}
    alt={product?.name}
    className='h-full w-full object-cover object-center lg:h-full lg:w-full transition-transform duration-500 ease-out group-hover:scale-110'
  />
  <div
    className='pointer-events-none absolute inset-x-0 top-0 h-0 bg-gray-300/70 transition-[height] duration-500 ease-out group-hover:h-full'
  />
</div>
```

**What changed:**
1. Added `relative` to the wrapper so the mask absolutely positions inside it.
2. Added `transition-transform duration-500 ease-out group-hover:scale-110` to the `<img>` → smooth zoom on hover.
3. Added a sibling overlay div that starts at `h-0` and grows to `h-full` on hover, fixed at `top-0` so it animates **top → bottom**.
4. `bg-gray-300/70` is light gray with 70% opacity (the "light gray mask").
5. Removed the unused/typo `grup-hover:opacity-75` class.

> The parent already has `className='group relative'`, which is what makes `group-hover:` work on the children. Don't remove that.

---

## 5. Verification Checklist (Run After Each Change)

The dev server hot-reloads. After each save:

- [ ] **Task 1** — go to `/products/1` → 5 stars are yellow
- [ ] **Task 2** — go to `/mens` → grid shows 3 columns on large screens (resize browser if needed)
- [ ] **Task 3** — move mouse anywhere → follower is a diamond, not a circle
- [ ] **Task 4** — go to `/mens`, hover a product → image zooms AND a light-gray panel sweeps down from the top covering the image

---

## 6. Six-Minute Video Script

Total budget: **6:00**. Keep narration crisp; pause between sections.

### 0:00 – 0:30 — Intro (30s)
> "Hi, I'm completing the front-end test on the Test-dev repo. I'll show four changes: yellow stars, a 3-column product grid, a non-circle cursor, and a zoom + light-gray mask hover animation. Here's the project running locally."
- Show the home page in browser.
- Briefly show the file tree on the side.

### 0:30 – 1:30 — Task 1: Yellow Stars (60s)
- Navigate to `/products/1` to show current teal stars.
- Open `src/components/ProductDisplay.jsx`.
- Point to the 5 `<Star>` lines (26–30).
- Edit `fill='#138695'` → `fill='#FACC15' color='#FACC15'` for all 5.
- Save → switch to browser → stars are yellow.
> "I'm using `#FACC15`, which is Tailwind's yellow-400. I added `color` so the stroke matches the fill."

### 1:30 – 2:30 — Task 2: 3-Column Grid (60s)
- Navigate to `/mens` to show the current 4-column layout.
- Open `src/components/ProductList.jsx`.
- Point to line 10 → change `lg:grid-cols-4` to `lg:grid-cols-3`.
- Save → browser reloads with 3 columns.
> "Single Tailwind class change — the grid switches from 4 to 3 at the `lg` breakpoint."

### 2:30 – 4:00 — Task 3: Custom Cursor Shape (90s)
- Show the round white follower on the home page.
- Briefly explain: "The project uses `react-mouse-follower`, which renders a div with `id='mouse-follower'` and a circular border-radius."
- Open `src/index.css`.
- Scroll to the bottom and add the CSS block (Task 3 above).
- Save → browser → follower is now a diamond.
> "I override the inline `border-radius` with `!important`, then rotate the square 45 degrees to get a diamond — clearly not a circle, and it still feels intentional."

### 4:00 – 5:30 — Task 4: Hover Zoom + Top-Down Mask (90s)
- Navigate to `/mens`.
- Hover a product → show current behavior (no animation).
- Open `src/components/Item.jsx`.
- Make the three edits:
  - Add `relative` to the image wrapper.
  - Add `transition-transform duration-500 ease-out group-hover:scale-110` to the `<img>`.
  - Add the overlay `<div>` after the `<img>`.
- Save → hover a product → image zooms and gray panel sweeps top to bottom.
> "The parent has `group`, so `group-hover` triggers both the image zoom and the overlay's height transition. Setting `top-0` and animating `h-0` to `h-full` makes the mask grow downward."

### 5:30 – 6:00 — Wrap-Up (30s)
- Quickly re-show: stars yellow, 3-column grid, diamond cursor, hover animation on a product.
- "All four tasks are done. The full diff covers four files: `ProductDisplay.jsx`, `ProductList.jsx`, `index.css`, and `Item.jsx`. Thanks for watching."

---

## 7. Recording Tips

- **Browser at the side, editor at the side** — split the screen 50/50 so both are visible.
- **Increase font size** in the editor to 16–18pt before recording.
- **Show the URL bar** when navigating between routes.
- **Pause between tasks** for a clean cut if you need to re-record one.
- **Run `npm run dev` before starting** so you don't burn time on install.
- **Mute notifications** (Slack, email) and close unrelated tabs.
- If you want to skip narration of `npm install`, run it once before hitting record.

---

## 8. Quick Reference — All Diffs in One Block

```
src/components/ProductDisplay.jsx   ▸ 5× Star fill = '#FACC15', add color='#FACC15'
src/components/ProductList.jsx      ▸ lg:grid-cols-4 → lg:grid-cols-3
src/index.css                       ▸ append CSS overriding #mouse-follower
src/components/Item.jsx             ▸ add relative, scale on img, add overlay div
```

That's it — four files, four small changes.
