---
description: Add REST API infrastructure with Cloudflare D1 + Drizzle
---

# Astro API

Add Cloudflare D1 database, Drizzle ORM, and Vitest infrastructure to an existing Astro project.

## Prerequisites

- Existing Astro 6 project (run `/get:astro-6:new` first)
- Logged into Cloudflare (`npx wrangler login`)

## Instructions

**1. Install dependencies:**

The Cloudflare adapter requires sharp. Install sharp build deps first to avoid failures:
```bash
npm install -D node-addon-api node-gyp sharp
npm install @astrojs/cloudflare drizzle-orm drizzle-zod
npm install -D drizzle-kit wrangler @cloudflare/vitest-pool-workers
```

**2. Update `astro.config.mjs`:**

Merge the Cloudflare adapter into the existing config (preserve Tailwind vite plugin, fonts, shiki, etc.):
```javascript
// @ts-check
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';
import cloudflare from '@astrojs/cloudflare';

export default defineConfig({
  output: 'server',
  security: {
    csp: true,
  },
  adapter: cloudflare({
    platformProxy: { enabled: true },
    imageService: 'compile',
  }),
  vite: {
    plugins: [tailwindcss()],
    ssr: {
      optimizeDeps: {
        include: ['drizzle-orm', 'drizzle-orm/d1', 'drizzle-orm/sqlite-core'],
      },
    },
    server: {
      allowedHosts: ['.trycloudflare.com'],
    },
  },
  // ... preserve any existing config (shiki, fonts, integrations)
});
```

Key points:
- `ssr.optimizeDeps.include` — prevents Vite SSR chunk errors with Drizzle
- `imageService: 'compile'` — avoids sharp runtime warnings on workerd

**3. Ask user:** "Will this API serve mobile or external clients that don't send Origin headers? If yes, set `checkOrigin: false` in security config."

If yes, add to security config:
```javascript
security: {
  checkOrigin: false,
  csp: true,
},
```

**4. Create `wrangler.jsonc`:**

Use `.jsonc` (not `.toml`) — supports comments, easier to read:
```jsonc
{
  "name": "<project-name>",
  "workers_dev": false,
  "compatibility_date": "2026-02-01",
  "compatibility_flags": [
    "nodejs_compat"
  ],
  "d1_databases": [
    {
      "binding": "DB",
      "database_name": "<project-name>-db",
      "database_id": "<will-be-filled-after-db-create>",
      "migrations_dir": "db/migrations"
    }
  ]
}
```

A recent `compatibility_date` is required for Astro 6 + Vite to work with workerd.

**5. Create `drizzle.config.ts`:**

Schema glob loads all model files:
```typescript
import { defineConfig } from 'drizzle-kit';

export default defineConfig({
  schema: './src/models/*.ts',
  out: './db/migrations',
  dialect: 'sqlite',
});
```

**6. Create `src/env.d.ts`:**

Use `Cloudflare.Env` namespace (NOT the old `App.Locals.runtime.env` pattern):
```typescript
declare namespace Cloudflare {
  interface Env {
    DB: D1Database;
  }
}
```

**7. Create `src/db/index.ts`:**
```typescript
import { drizzle } from 'drizzle-orm/d1';

export function getDb(d1: D1Database) {
  return drizzle(d1);
}
```

**8. Set up Vitest with Cloudflare Workers pool:**

Create `vitest.config.ts`:
```typescript
import path from "node:path";
import {
  defineWorkersProject,
  readD1Migrations,
} from "@cloudflare/vitest-pool-workers/config";

export default defineWorkersProject(async () => {
  const migrationsPath = path.join(import.meta.dirname, "db", "migrations");
  const migrations = await readD1Migrations(migrationsPath);

  return {
    resolve: {
      alias: {
        "@": path.join(import.meta.dirname, "src"),
      },
    },
    test: {
      reporters: "verbose",
      include: ["test/**/*.test.ts"],
      setupFiles: ["./test/apply-migrations.ts"],
      poolOptions: {
        workers: {
          singleWorker: true,
          wrangler: {
            configPath: "./wrangler.jsonc",
          },
          miniflare: {
            bindings: { TEST_MIGRATIONS: migrations },
          },
        },
      },
    },
  };
});
```

Key points:
- `singleWorker: true` — all tests share one worker, see each other's DB writes (must cleanup in `beforeEach`)
- `@` alias mirrors tsconfig
- Migrations loaded at config time, applied via setup file

**9. Create `test/apply-migrations.ts`:**
```typescript
import { applyD1Migrations, env } from "cloudflare:test";

await applyD1Migrations(env.DB, env.TEST_MIGRATIONS);
```

**10. Create `test/env.d.ts`:**
```typescript
declare module "cloudflare:test" {
  interface ProvidedEnv extends Env {
    TEST_MIGRATIONS: D1Migration[];
  }
}
```

**11. Add scripts to `package.json`:**
```json
"db:create": "wrangler d1 create <project-name>-db",
"db:generate": "drizzle-kit generate",
"db:migrate": "wrangler d1 migrations apply <project-name>-db --local",
"db:migrate:prod": "wrangler d1 migrations apply <project-name>-db --remote",
"db:studio": "drizzle-kit studio",
"test": "npx vitest run",
"routes": "grep -r 'export const' src/pages/api/ --include='*.ts' | sed 's/.*pages//' | sed 's/export const //' | sort"
```

**12. Output next steps:**
```
API infrastructure ready!

Next: run `/get:astro-6:api:resource <name>` to scaffold your first resource.
  e.g. /get:astro-6:api:resource post
```

---

## Env access

Always use `import { env } from 'cloudflare:workers'` — NOT `locals.runtime.env` (old pattern), NOT `process.env` (doesn't work on workerd).

## Reference

- Astro Cloudflare adapter: https://docs.astro.build/en/guides/integrations-guide/cloudflare/
- Drizzle ORM: https://orm.drizzle.team/
- Drizzle + D1: https://orm.drizzle.team/docs/get-started-sqlite#cloudflare-d1
- Cloudflare D1: https://developers.cloudflare.com/d1/
- Vitest Workers Pool: https://developers.cloudflare.com/workers/testing/vitest-integration/
