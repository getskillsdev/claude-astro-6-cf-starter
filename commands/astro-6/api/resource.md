---
description: Scaffold a REST resource (model, actions, routes)
argument-hint: <resource>
---

# Astro API - Resource

Scaffold a new REST resource with model, action handlers, and route files.

## Arguments

- `<resource>` - Singular resource name (e.g., "post", "user", "mark")

## Prerequisites

- API infrastructure set up (run `/get:astro-6:api` first)

## Instructions

Given resource name (e.g., "post" → plural "posts"):

**1. Create model `src/models/<resource>.ts`:**

Each model file contains table + types + Zod schema together:
```typescript
import { sqliteTable, text } from 'drizzle-orm/sqlite-core';
import { sql } from 'drizzle-orm';
import { createInsertSchema } from 'drizzle-zod';

export const <resources> = sqliteTable('<resources>', {
  id: text('id').primaryKey().$defaultFn(() => crypto.randomUUID()),
  // TODO: add resource-specific columns here
  createdAt: text('created_at').notNull().default(sql`(datetime('now'))`),
});

// Type inference from table definition
export type <Resource> = typeof <resources>.$inferSelect;
export type New<Resource> = typeof <resources>.$inferInsert;

// Zod schema (override specific fields as needed)
export const insert<Resource>Schema = createInsertSchema(<resources>, {
  // e.g. url: z.string().min(1),
});
```

**2. Create action `src/actions/<resources>/create.ts`:**

```typescript
import type { APIRoute } from 'astro';
import { env } from 'cloudflare:workers';
import { getDb } from '@/db';
import { <resources> } from '@/models/<resource>';

export const create: APIRoute = async ({ request }) => {
  const db = getDb(env.DB);
  const body = await request.json();

  const [inserted] = await db.insert(<resources>).values({
    // TODO: add fields from body
  }).returning();

  return Response.json(inserted, { status: 201 });
};
```

**3. Create action `src/actions/<resources>/index.ts`:**

```typescript
import type { APIRoute } from 'astro';
import { env } from 'cloudflare:workers';
import { desc } from 'drizzle-orm';
import { getDb } from '@/db';
import { <resources> } from '@/models/<resource>';

export const index: APIRoute = async () => {
  const db = getDb(env.DB);
  const rows = await db.select().from(<resources>).orderBy(desc(<resources>.createdAt));
  return Response.json(rows);
};
```

**4. Create action `src/actions/<resources>/destroy.ts`:**

```typescript
import type { APIRoute } from 'astro';
import { env } from 'cloudflare:workers';
import { eq } from 'drizzle-orm';
import { getDb } from '@/db';
import { <resources> } from '@/models/<resource>';

export const destroy: APIRoute = async ({ params }) => {
  const db = getDb(env.DB);
  await db.delete(<resources>).where(eq(<resources>.id, params.id!));
  return new Response(null, { status: 204 });
};
```

**5. Create route `src/pages/api/<resources>.ts`:**

Thin file — just imports and re-exports:
```typescript
import { create } from '@/actions/<resources>/create';
import { index } from '@/actions/<resources>/index';

export const POST = create;
export const GET = index;
```

**6. Create route `src/pages/api/<resources>/[id].ts`:**

```typescript
import { destroy } from '@/actions/<resources>/destroy';

export const DELETE = destroy;
```

**7. Output next steps:**
```
Resource "<resource>" scaffolded!

Next steps:
1. Add your columns to src/models/<resource>.ts
2. Run: npm run db:generate (creates migration)
3. Run: npm run db:migrate (applies to local D1)
4. Update action files with your fields
5. Run: /get:astro-6:api:resource-test <resource> to add tests

Endpoints:
  POST   /api/<resources>      - Create
  GET    /api/<resources>      - List all
  DELETE /api/<resources>/:id  - Delete

Structure:
  src/models/<resource>.ts         - Table + types + Zod schema
  src/actions/<resources>/*.ts     - Business logic
  src/pages/api/<resources>.ts     - Route (verb exports)
```

---

## Status codes

- 201 — create success
- 200 — get/list success
- 204 — delete success (no body)
- 400 — validation error
- 401 — auth error
- 404 — not found
- 409 — conflict (e.g., duplicate)
- 422 — unprocessable (e.g., limit reached)

---

**Resource:** $ARGUMENTS
