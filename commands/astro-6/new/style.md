---
description: Add Tailwind CSS and base styles
---

# Astro New - Style

Add Tailwind CSS entry point and dev utilities.

## Prerequisites

- Astro project with Tailwind v4 installed (run `/get:astro-6:new:core` first)

## Instructions

**1. Create `src/css/global.css`:**

Tailwind v4 entry point with optional theme customization:
```css
@import "tailwindcss";

@theme {
  --font-sans: system-ui, sans-serif;
  --color-brand: oklch(0.72 0.19 150);
}
```

This is all that's needed — Tailwind's preflight provides the CSS reset, and utilities are available everywhere via `@import "tailwindcss"`.

The `@theme` block is where custom design tokens go. Users can add colors, fonts, spacing, etc. here. These become usable as utilities (e.g., `text-brand`, `font-sans`).

**2. Add `dev:watch` script to `package.json`:**

Workaround for Astro 6 content caching bug:
```json
"dev:watch": "sh -c 'find src/pages src/content -type f 2>/dev/null | entr -r astro dev'"
```
Requires `entr` (`brew install entr` on macOS).

**3. Ask user:** "Add request logging middleware? (Astro 6 removed page request logs from the dev server)"

If yes, create `src/middleware.ts`:
```typescript
import { defineMiddleware, sequence } from 'astro:middleware';

const logging = defineMiddleware(async ({ request }, next) => {
  const url = new URL(request.url);
  const start = Date.now();
  const response = await next();
  const ms = Date.now() - start;
  const time = new Date().toTimeString().slice(0, 8);
  const isApi = url.pathname.startsWith('/api/');
  const prefix = isApi ? '  ↳' : '↳';
  console.log(`${time} ${prefix} ${request.method} ${url.pathname} ${response.status} ${ms}ms`);
  return response;
});

export const onRequest = sequence(logging);
```
Uses `sequence()` so additional middleware (auth, logging) can be composed later without restructuring.

**4. Confirm style setup complete**

Output: "Style setup complete. Run `/get:astro-6:new:components` to add starter components."

---

**Notes:**
- Tailwind v4 needs no `tailwind.config.*` or `postcss.config.*` — all config is in CSS
- `@theme` values become utilities automatically (e.g., `--color-brand` → `text-brand`, `bg-brand`)
- Preflight (reset) is included by default via `@import "tailwindcss"`
