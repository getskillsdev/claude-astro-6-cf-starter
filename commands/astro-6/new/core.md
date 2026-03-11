---
description: Scaffold Astro 6 project with deps and configs
---

# Astro New - Core

Scaffold a new Astro 6 project with Tailwind CSS v4 on Cloudflare Pages.

## Prerequisites

- Node 20.19.1+ or 22.12.0+ (Astro 6 dropped 18)

## Instructions

**1. Check Node version:**
```bash
node --version
```
Require Node 20.19.1+ or 22.12.0+. Node 23+ is fine.
If below: ask user what version manager they use (nvm, fnm, volta, mise, etc.) and help them switch to Node 22.

**2. Ask for project directory name** (e.g., `website/`)

**3. Check latest Astro 6:**
```bash
npm view astro versions --json | grep "6\." | tail -1
```
Ask: "Newest V6 is {version} - install?"

**4. Scaffold and install:**

Always scaffold to `/tmp` first (avoids interactive prompts that cause timeouts):
```bash
rm -rf /tmp/astro-scaffold
npm create astro@latest /tmp/astro-scaffold -- --template minimal --no-install --no-git
cp -r /tmp/astro-scaffold/* <directory>/
cp /tmp/astro-scaffold/.gitignore <directory>/
```

Then install Astro:
```bash
cd <directory> && npm install astro@<chosen-version>
```

**5. Add `.nvmrc`:**
```
22
```

**6. Install Tailwind CSS v4:**

Tailwind v4 uses a Vite plugin — no PostCSS config needed:
```bash
npm install tailwindcss @tailwindcss/vite
```

**7. Update `tsconfig.json` with path alias:**

Add `@/*` path alias for clean imports:
```json
{
  "extends": "astro/tsconfigs/strict",
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

**8. Ask user:** "Do you want icon support? (astro-icon + Material Design Icons)"

If yes:
```bash
npm install astro-icon @iconify-json/mdi
```

**9. Ask user:** "Add Shiki dual-theme syntax highlighting for code blocks? (Note: Shiki uses inline styles that conflict with CSP. If you later add the API layer with CSP enabled, you'll need to switch Shiki to `class` mode or use Prism instead.)"

**10. Ask user:** "Add custom Google Fonts via Astro Fonts API? If yes, which fonts?"

**11. Create `astro.config.mjs`:**

Include optional blocks based on user answers:
```javascript
// @ts-check
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  vite: {
    plugins: [tailwindcss()],
    server: {
      allowedHosts: ['.trycloudflare.com']
    }
  },
  // Only if user said yes to Shiki:
  markdown: {
    shikiConfig: {
      themes: {
        light: 'github-light',
        dark: 'github-dark'
      }
    }
  },
  // Only if user said yes to Fonts API:
  // import { fontProviders } from 'astro/config';
  // fonts: [
  //   { provider: fontProviders.google(), name: '<font>', cssVariable: '--font-<name>', weights: [400, 700] },
  // ],
  // Only if user said yes to icons:
  // import icon from 'astro-icon';
  // integrations: [icon()]
});
```

If Fonts API is used, Head.astro uses `<Font />` from `astro:assets`.
No manual `<link rel="preconnect">` or Google Fonts stylesheet links needed — Astro handles preloading and fallbacks automatically.

**12. Confirm core setup complete**

Output: "Core setup complete. Run `/get:astro-6:new:style` to add CSS architecture."

---

**Notes:**
- Tailwind v4 is a Vite plugin — no `postcss.config.mjs` or `tailwind.config.*` needed
- All config is done in CSS via `@theme` and `@import "tailwindcss"`
- Node 20.19.1+ or 22.12.0+ required (Astro 6 dropped Node 18)
