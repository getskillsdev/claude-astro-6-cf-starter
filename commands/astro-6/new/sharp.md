---
description: Install sharp image optimization (optional)
---

# Astro New - Sharp

Install sharp for image optimization.

## Prerequisites

- Astro project with dependencies installed

## Instructions

**1. Install sharp with build deps:**
```bash
npm install -D node-addon-api node-gyp sharp
```

`node-addon-api` and `node-gyp` are required for sharp to build from source when no prebuilt binary is available.

**2. Verify installation:**
```bash
node -e "require('sharp'); console.log('sharp works')"
```

**3. Confirm setup complete**

Output: "Sharp installed successfully."

---

**Notes:**
- Sharp is optional — only needed for image optimization
- Never use platform-specific `@img/sharp-*` packages (e.g. `@img/sharp-darwin-arm64`) — they break CI on other platforms
- Cloudflare adapter defaults to `imageService: "compile"` — sharp runs at build time only, no config needed
