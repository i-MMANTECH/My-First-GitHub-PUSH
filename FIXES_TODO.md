# Fixes Status & TODO

Below is the current state of each requested fix in your codebase, followed by exact step-by-step instructions for what's still missing.

---

## Status Summary

| # | Fix | Status |
|---|------|--------|
| 1 | Star ratings yellow | NOT DONE — fill is still teal `#138695` |
| 2 | Products in 3 columns | NOT DONE — still `lg:grid-cols-4` |
| 3 | Custom cursor shape (not a circle) | DONE — diamond shape applied in [src/index.css:350-359](src/index.css#L350-L359) |
| 4 | Hover: zoom + top-to-bottom light gray mask | PARTIAL — zoom is done, the top-to-bottom light gray mask is missing |

---

## Fix 1 — Make the rating stars yellow

**File:** [src/components/ProductDisplay.jsx](src/components/ProductDisplay.jsx) (lines 26–30)

**Problem:** The `fill` is currently `#138695` (teal). Only the outline (`color`) is yellow, so the stars look mostly teal.

**Steps:**
1. Open [src/components/ProductDisplay.jsx](src/components/ProductDisplay.jsx).
2. Find the 5 `<Star ... />` lines (lines 26–30).
3. Change the **filled** stars (the first 4) so both `fill` and `color` are yellow.
4. Leave the last star (the empty one) with `fill='gray'` so it visibly shows as unrated.

Replace lines 26–30 with:

```jsx
<Star fill='#FACC25' color="#FACC25"/>
<Star fill='#FACC25' color="#FACC25"/>
<Star fill='#FACC25' color="#FACC25"/>
<Star fill='#FACC25' color="#FACC25"/>
<Star fill='gray' color="#FACC25"/>
```

5. Save the file.

---

## Fix 2 — Show products in 3 columns instead of 4

**File:** [src/components/ProductList.jsx](src/components/ProductList.jsx) (line 10)

**Problem:** The grid uses `lg:grid-cols-4`, which renders 4 columns on large screens.

**Steps:**
1. Open [src/components/ProductList.jsx](src/components/ProductList.jsx).
2. On line 10 find:

```jsx
<div className='mt-10 grid grid-cols-1 gap-x-6 gap-y-10 px-6 md:px-0 sm:grid-cols-2 lg:grid-cols-4 xl:gap-x-8'>
```

3. Change `lg:grid-cols-4` to `lg:grid-cols-3`:

```jsx
<div className='mt-10 grid grid-cols-1 gap-x-6 gap-y-10 px-6 md:px-0 sm:grid-cols-2 lg:grid-cols-3 xl:gap-x-8'>
```

4. Save the file.

---

## Fix 3 — Custom cursor shape — ALREADY DONE

The cursor has been changed from a circle to a **diamond** (a square rotated 45 degrees) via the rules in [src/index.css:350-359](src/index.css#L350-L359). No action needed.

If you'd like a different shape (e.g. a square, triangle, or arrow), edit the `#mouse-follower` block in [src/index.css](src/index.css#L350-L359).

---

## Fix 4 — Hover: zoom + top-to-bottom light gray mask

**File:** [src/components/Item.jsx](src/components/Item.jsx)

**Problem:** The image already zooms on hover (`group-hover:scale-110` on line 11), but there is no overlay that wipes from top to bottom in light gray.

**Steps:**
1. Open [src/components/Item.jsx](src/components/Item.jsx).
2. Replace the existing image-wrapper `<div>` (lines 8–12) with the version below. It adds a sibling overlay `<div>` that starts above the card (`-translate-y-full`) and slides down to cover it on hover (`group-hover:translate-y-0`).

```jsx
<div className='relative aspect-h-1 aspect-w-1 w-full overflow-hidden rounded-md bg-gray-800/50 lg:aspect-none lg:h-80 h-96 border border-gray-700/50'>
  <img
    src={product?.image}
    alt={product?.name}
    className='h-full w-full object-cover object-center lg:h-full lg:w-full transition-transform duration-500 ease-out group-hover:scale-110'
  />
  <div className='pointer-events-none absolute inset-0 bg-gray-300/50 -translate-y-full group-hover:translate-y-0 transition-transform duration-500 ease-out'></div>
</div>
```

What this does:
- `relative` on the wrapper so the overlay can be absolutely positioned over it.
- The new overlay `<div>` is light gray (`bg-gray-300/50`) and starts at `-translate-y-full` (entirely above the card).
- On hover the parent `.group` triggers `group-hover:translate-y-0`, sliding the overlay down to fully cover the image.
- The image itself still zooms (`group-hover:scale-110`).
- I removed `group-hover:opacity-75` so the mask is the visible hover effect, not a generic dim.

3. Save the file.

---

## Run the project locally

Open a terminal **in the project folder** (`c:\Users\imman_tech\Desktop\nerdy\Test-dev`) and run the commands below in order. Use Git Bash or PowerShell — both work.

### Step A — Install dependencies (only needed the first time, or if `node_modules` is missing)

```bash
npm install
```

### Step B — Start the dev server

```bash
npm run dev
```

You should see output similar to:

```
  VITE v6.x.x  ready in xxx ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: http://192.168.x.x:5173/
```

### Step C — Open the app in your browser

Hold **Ctrl** and click the `http://localhost:5173/` link in the terminal, or paste it into your browser's address bar.

### Step D — Verify each fix on screen

- **Fix 1 (yellow stars):** Open any product detail page (click a product). The 4 filled stars should be solid yellow.
- **Fix 2 (3 columns):** Scroll to the "Top Sellers" / product list section on a wide screen. Count 3 products per row, not 4.
- **Fix 3 (cursor shape):** Move your mouse around the page. The follower should be a diamond, not a circle.
- **Fix 4 (hover mask + zoom):** Hover over any product card. The image should zoom in *and* a light gray panel should slide down from the top to cover it.

### Step E — Stop the server when done

In the terminal running `npm run dev`, press **Ctrl + C**, then `Y` if it asks for confirmation.

---

## If something goes wrong

- **`npm: command not found`** — Install Node.js from https://nodejs.org (LTS), close the terminal, and reopen it.
- **Port 5173 already in use** — Stop the other process, or run with a different port: `npm run dev -- --port 5174`.
- **Page is blank / errors in browser console** — Check the terminal output for the file and line of the error, fix the typo, and save. Vite hot-reloads automatically.
- **Changes don't show** — Hard refresh the browser with **Ctrl + Shift + R**.
