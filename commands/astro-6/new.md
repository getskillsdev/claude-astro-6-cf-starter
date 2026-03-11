---
description: Create new Astro 6 project on Cloudflare Pages
argument-hint: [directory]
---

# Astro New

Create a new Astro 6 project deployed to Cloudflare Pages.

## Stack

- Astro 6 (latest)
- Tailwind CSS v4
- Cloudflare Pages
- Node 22 (Active LTS)

## Instructions

This command orchestrates the full setup by calling subcommands:

**1. Run `/get:astro-6:new:core`**

Scaffolds project, installs deps, creates configs.
- Ask for directory name
- Check Astro 6 version
- Scaffold with `create astro`
- Install Tailwind CSS v4 (Vite plugin)
- Create astro.config.mjs, .nvmrc

**2. Run `/get:astro-6:new:style`**

Sets up CSS files.
- Create src/css/global.css (Tailwind imports, reset, base styles)
- Add dev:watch script
- Optional: request logging middleware

**3. Run `/get:astro-6:new:components`**

Creates starter components and layouts.
- Create src/c/Head.astro
- Create src/c/Header.astro
- Create src/c/Footer.astro
- Create src/layouts/Base.astro
- Update src/pages/index.astro

**4. Run `/get:astro-6:new:sharp`**

Installs sharp + build deps for image optimization.

**5. Optional prompts**

Ask user about optional features:

- "Do you want content collections?" → create `src/content.config.ts` using this template:
```ts
import { defineCollection } from 'astro:content';
import { z } from 'astro/zod';
import { glob } from 'astro/loaders';

const pages = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/pages' }),
  schema: z.object({
    title: z.string(),
    description: z.string().optional(),
    date: z.coerce.date().optional(),
  }),
});

export const collections = { pages };
```
- "Do you want client-side routing?" → add `import { ClientRouter } from 'astro:transitions';` and `<ClientRouter />` to Head.astro (replaces old `<ViewTransitions />`)
- "Scaffold REST API? Enter a singular resource name (e.g., post, mark, user) or leave blank to skip:" → if provided, run `/get:astro-6:api` then `/get:astro-6:api:resource <resource>` then `/get:astro-6:api:resource-test <resource>`. If blank, skip.

**6. Finish**

- Create CLAUDE.md with project info (stack choices made, directory structure, dev commands)
- Confirm setup complete

---

## Subcommands

| Command | Purpose |
|---------|---------|
| `/get:astro-6:new:core` | Scaffold, deps, configs |
| `/get:astro-6:new:style` | Tailwind CSS, reset, base styles |
| `/get:astro-6:new:components` | Starter components, layouts |
| `/get:astro-6:new:sharp` | Sharp image optimization |
| `/get:astro-6:api` | D1 + Drizzle + Vitest infrastructure |
| `/get:astro-6:api:resource` | Scaffold model, actions, routes |
| `/get:astro-6:api:resource-test` | Test helpers + example test |

Each subcommand can be run standalone to add that layer to an existing project.

---

## Reference

- Astro: https://docs.astro.build
- Tailwind CSS: https://tailwindcss.com/docs

---

**Argument:** $ARGUMENTS
