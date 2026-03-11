---
description: Add starter components and layouts
---

# Astro New - Components

Add starter components (Head, Header, Footer) and a base layout.

## Prerequisites

- Astro project with Tailwind CSS setup (run `/get:astro-6:new:style` first)

## Instructions

**1. Create `src/c/Head.astro`:**
```astro
---
interface Props {
  title: string;
}

// Only if Fonts API was enabled in /get:astro-6:new:core:
// import { Font } from 'astro:assets';

const { title } = Astro.props;
---

<head>
  <meta charset="utf-8" />
  <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
  <meta name="viewport" content="width=device-width" />
  <meta name="generator" content={Astro.generator} />
  <!-- Only include <Font /> if Fonts API was enabled -->
  <title>{title}</title>
</head>
```

**2. Create `src/c/Header.astro`:**
```astro
<header class="flex justify-between items-center p-4 max-w-prose mx-auto">
  <nav>
    <a href="/" class="text-brand hover:underline">Home</a>
  </nav>
</header>
```

**3. Create `src/c/Footer.astro`:**
```astro
<footer class="mt-auto pt-8 pb-4 text-sm max-w-prose mx-auto">
  <p>&copy; {new Date().getFullYear()}</p>
</footer>
```

**4. Create `src/layouts/Base.astro`:**
```astro
---
import Head from '../c/Head.astro';
import Header from '../c/Header.astro';
import Footer from '../c/Footer.astro';
import '../css/global.css';

interface Props {
  title: string;
}

const { title } = Astro.props;
---

<html lang="en">
  <Head title={title} />
  <body class="min-h-screen flex flex-col">
    <Header />
    <slot />
    <Footer />
  </body>
</html>
```

**5. Update `src/pages/index.astro`:**
```astro
---
import Base from '../layouts/Base.astro';
---

<Base title="Home">
  <main class="max-w-prose mx-auto px-4 py-8">
    <h1 class="text-3xl font-bold">Hello</h1>
  </main>
</Base>
```

**6. Confirm components setup complete**

Output: "Components setup complete. Project is ready!"

---

**Directory structure created:**
```
src/
├── c/
│   ├── Head.astro
│   ├── Header.astro
│   └── Footer.astro
├── css/
│   └── global.css
├── layouts/
│   └── Base.astro
└── pages/
    └── index.astro
```
