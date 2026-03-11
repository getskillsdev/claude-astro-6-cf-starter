---
description: Add test helpers and example test for a resource
argument-hint: <resource>
---

# Astro API - Resource Test

Add test helpers and an example test file for a REST resource.

## Arguments

- `<resource>` - Singular resource name (e.g., "post", "user", "mark")

## Prerequisites

- API infrastructure set up (run `/get:astro-6:api` first)
- Resource scaffolded (run `/get:astro-6:api:resource <resource>` first)

## Instructions

Given resource name (e.g., "post" → plural "posts"):

**1. Create or update `test/helpers.ts`:**

If `test/helpers.ts` doesn't exist, create it. If it exists, add the new resource's routes to the registry.

```typescript
import type { APIContext, APIRoute } from "astro";
import { create } from "@/actions/<resources>/create";
import { index } from "@/actions/<resources>/index";
import { destroy } from "@/actions/<resources>/destroy";

const routes: Record<string, Record<string, APIRoute>> = {
  "/api/<resources>": { POST: create, GET: index, DELETE: destroy },
};

function context(request: Request): APIContext {
  return { request, url: new URL(request.url), params: {} } as APIContext;
}

export const response = { status: 0, body: null as any };

async function request(method: string, path: string, opts: { headers?: Record<string, string>; body?: any } = {}) {
  const headers: Record<string, string> = { ...opts.headers };
  if (opts.body) headers["Content-Type"] = "application/json";

  const req = new Request(`http://localhost${path}`, {
    method,
    headers,
    body: opts.body ? JSON.stringify(opts.body) : undefined,
  });

  const handler = routes[path]?.[method];
  if (!handler) throw new Error(`No route: ${method} ${path}`);

  const res = await handler(context(req));
  response.status = res.status;
  response.body = await res.json().catch(() => null);
}

export const get = (path: string, opts?: { headers?: Record<string, string> }) =>
  request("GET", path, opts);

export const post = (path: string, opts?: { headers?: Record<string, string>; body?: any }) =>
  request("POST", path, opts);

export const del = (path: string, opts?: { headers?: Record<string, string>; body?: any }) =>
  request("DELETE", path, opts);
```

Tests call action handlers directly — no HTTP server needed.

**2. Create test `test/actions/api/<resources>.test.ts`:**

```typescript
import { describe, it, expect, beforeEach } from "vitest";
import { env } from "cloudflare:test";
import { getDb } from "@/db";
import { <resources> } from "@/models/<resource>";
import { post, get, response } from "../../helpers";

describe("POST /api/<resources>", () => {
  beforeEach(async () => {
    await getDb(env.DB).delete(<resources>);
  });

  describe("with valid data", () => {
    beforeEach(() => post("/api/<resources>", {
      body: { /* TODO: fields */ },
    }));

    it("returns 201", () => {
      expect(response.status).toBe(201);
    });
  });
});

describe("GET /api/<resources>", () => {
  beforeEach(async () => {
    await getDb(env.DB).delete(<resources>);
  });

  it("returns empty array", async () => {
    await get("/api/<resources>");
    expect(response.body).toEqual([]);
  });
});
```

**3. Confirm test setup complete**

Output: "Tests scaffolded. Run `npm test` to verify."

---

**Resource:** $ARGUMENTS
