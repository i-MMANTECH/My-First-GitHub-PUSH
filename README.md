# Fix: `Cannot find module 'tailwind-animationbasic'`

## What the error means

Vite/PostCSS is trying to load a Tailwind plugin named `tailwind-animationbasic`, but **that package does not exist on npm**. It is listed in `package.json` and required from `tailwind.config.js`, so the build crashes the moment any CSS file gets processed.

`npm install` reports "up to date" without errors because npm silently skipped the missing package — it never landed in `node_modules`, which is why PostCSS can't resolve it.

The real package you want is **`tailwindcss-animate`** (v1.x). That is the standard animation plugin used by shadcn/ui and most Tailwind starter templates.

---

## How to fix

Run all commands from the project root: `C:\Users\imman_tech\Desktop\nerdy\Test-dev`.

### Step 1 — Edit `package.json`

Find this line in `devDependencies`:

```json
"tailwind-animationbasic": "^2.3.7",
```

Replace it with:

```json
"tailwindcss-animate": "^1.0.7",
```

> ⚠️ **Watch the version number.** Don't just rename the package and leave `^2.3.7` — that version of `tailwindcss-animate` does not exist and `npm install` will fail with:
>
> ```
> npm error code ETARGET
> npm error notarget No matching version found for tailwindcss-animate@^2.3.7.
> ```
>
> The `tailwindcss-animate` package's latest release is in the **1.x** line (currently `1.0.7`). Use `^1.0.7`.

### Step 2 — Edit `tailwind.config.js`

Find the last line:

```js
plugins: [require('tailwind-animationbasic')],
```

Replace it with:

```js
plugins: [require('tailwindcss-animate')],
```

### Step 3 — Reinstall dependencies cleanly

The previous "successful" install left the lockfile in a confused state, so do a clean reinstall:

```powershell
cd C:\Users\imman_tech\Desktop\nerdy\Test-dev
Remove-Item -Recurse -Force node_modules
Remove-Item -Force package-lock.json
npm install
```

### Step 4 — Start the dev server

```powershell
npm run dev
```

You should now see Vite serve on `http://localhost:5173/` with no PostCSS error.

---

## What to do next (after the fix works)

1. **Hard-refresh the browser** (`Ctrl + Shift + R`) — the old error overlay can stick around in cache.
2. **Verify animations actually load.** If any component uses classes like `animate-in`, `fade-in-0`, `slide-in-from-bottom-4`, those come from `tailwindcss-animate`. They should render smoothly.
3. **Commit the fix to your project repo** so you don't hit the same error on a clean clone:
   ```powershell
   git add package.json package-lock.json tailwind.config.js
   git commit -m "fix: replace nonexistent tailwind-animationbasic with tailwindcss-animate"
   ```
4. **If you actually wanted a different plugin** (not the standard animate one), the only other plugins commonly used are:
   - `@tailwindcss/typography` — for prose styling
   - `@tailwindcss/forms` — for form element resets
   - `@tailwindcss/aspect-ratio` — for aspect-ratio utilities

   Install whichever applies and update `tailwind.config.js` accordingly.

---

## Why this happened

Most likely one of these:
- A typo or AI-generated dependency name that was never verified against npm.
- A copy-paste from a tutorial that invented a plugin name.
- A find-and-replace gone wrong on the original `tailwindcss-animate` entry.

**Lesson:** when `npm install` says "up to date" but a module can't be resolved at runtime, check `node_modules/<package-name>` directly — if the folder is missing, the package name in `package.json` is wrong.
