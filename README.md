# astro-6

[![CI](https://github.com/getskillsdev/claude-astro-6-cf-starter/actions/workflows/ci.yml/badge.svg)](https://github.com/getskillsdev/claude-astro-6-cf-starter/actions/workflows/ci.yml)

Scaffold Astro 6 projects on Cloudflare Pages with Tailwind CSS v4, D1 + Drizzle REST APIs, and Vitest.

## Why use this?

Astro 6 + Cloudflare is a minefield of integration gotchas. Sharp breaks your CI. Drizzle throws SSR chunk errors. Vitest needs a Workers pool with migration wiring. CSP blocks Shiki. You'll spend hours on Stack Overflow before writing a single line of app code.

This plugin has already hit every wall so you don't have to.

### Battle-tested fixes baked in

- Sharp platform packages (`@img/sharp-darwin-arm64`) break Linux CI — installs `sharp` correctly
- Drizzle SSR chunk errors — pre-configured `ssr.optimizeDeps.include`
- Vitest D1 testing — `singleWorker: true`, migration setup file, Workers pool wiring
- CSP + Shiki conflict — warns before you enable both (Shiki uses inline styles)
- Wrangler `compatibility_date` — set recent enough for workerd + Vite to work
- Old env pattern — uses `import { env } from 'cloudflare:workers'`, not the deprecated `App.Locals.runtime.env`

## What you get

- Astro 6 + Tailwind CSS v4 (Vite plugin — no PostCSS or Tailwind config files)
- Cloudflare Pages deployment ready
- Optional D1 + Drizzle REST API with model/action/route pattern
- Vitest with Cloudflare Workers pool for real D1 testing
- Sharp image optimization (CI-safe install)
- Content collections, client-side routing, request logging middleware

## Commands

| Command | Purpose |
|---------|---------|
| `/get:astro-6:new` | Full project setup (orchestrator) |
| `/get:astro-6:new:core` | Scaffold, Tailwind, configs |
| `/get:astro-6:new:style` | CSS entry point, middleware |
| `/get:astro-6:new:components` | Starter components + layout |
| `/get:astro-6:new:sharp` | Sharp image optimization |
| `/get:astro-6:api` | D1 + Drizzle + Vitest infrastructure |
| `/get:astro-6:api:resource` | Scaffold model, actions, routes |
| `/get:astro-6:api:resource-test` | Test helpers + example test |

## Install (quit Claude first)

```bash
claude plugin marketplace add getskillsdev/claude-astro-6-cf-starter
claude plugin install get@astro-6
```

## Usage (in Claude)

```
/get:astro-6:new
```

Walks you through creating a full Astro 6 project with Tailwind CSS v4 on Cloudflare Pages. Optionally scaffolds a REST API with D1 + Drizzle.

Each subcommand can also be run standalone to add that layer to an existing project.

## Update (quit Claude first)

```bash
claude plugin marketplace update astro-6
claude plugin update get@astro-6
```

## Uninstall (quit Claude first)

```bash
claude plugin uninstall get@astro-6
claude plugin marketplace remove astro-6
```

## Troubleshooting

**Skill not available?**

Restart Claude after installing or updating plugins.

## License

MIT - see [LICENSE.md](./LICENSE.md)

## Disclaimer

Not affiliated with, endorsed by, or sponsored by Anthropic, Astro, or Cloudflare.
