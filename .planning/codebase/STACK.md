# Tech Stack Specification

This document provides a detailed overview of the technologies, tooling, compilers, and environments configured in the National Logistics codebase.

## 1. Core Language & Runtime

| Technology | Version / Requirement | Role |
| :--- | :--- | :--- |
| **Node.js** | `>=22.12.0` | Runtime environment for build, dev server, and scripting. |
| **TypeScript** | Strict Configuration | Static type checking and modern JS features. |
| **JavaScript (ES Modules)** | Modern ES Module standard (`"type": "module"`) | Standard execution module format. |

### TypeScript Configuration
The project extends the strict Astro TypeScript presets:
* **Base Config**: `astro/tsconfigs/strict`
* **Included Paths**: `.astro/types.d.ts`, `**/*`
* **Excluded Paths**: `dist` (production build output)

---

## 2. Core Frameworks & Bundlers

| Technology | Version | Description |
| :--- | :--- | :--- |
| **Astro** | `^6.3.6` | Island-architecture framework, content-focused, zero-JS by default. |
| **Vite** | Built-in (Astro default) | Bundling, hot-module replacement (HMR), and asset compilation. |

---

## 3. Package & Dependency Management

* **Package Manager**: `pnpm`
* **Lockfile**: `pnpm-lock.yaml`
* **Installation standard**: Uses strict dependency resolution with shared content-addressable store.

---

## 4. Build, Development & Operational Commands

The project defines standard npm scripts in `package.json` executed using `pnpm`:

| Command | Underlying Action | Description |
| :--- | :--- | :--- |
| `pnpm dev` | `astro dev` | Starts the Astro local development server with hot reload. |
| `pnpm build` | `astro build` | Compiles static assets and pages to the `dist` directory. |
| `pnpm preview` | `astro preview` | Serves the locally built `dist` folder to preview the production site. |
| `pnpm astro` | `astro` | Executes CLI helpers and management tasks directly from Astro. |

---

## 5. UI, Styling & Layout

| Technology | Status | Integration Notes |
| :--- | :--- | :--- |
| **Styling Solution** | *Not yet configured (Greenfield stage)* | Currently relies on native CSS or scoped Astro `<style>` tags. No preprocessors (Sass) or utility frameworks (Tailwind CSS) have been added. |
| **Component Libraries** | *Not yet configured (Greenfield stage)* | No component libraries (e.g., React, Vue, Svelte, Preact) have been integrated yet. Astro islands are ready to accept them when needed. |

---

## 6. Databases & Storage

| Technology | Status | Integration Notes |
| :--- | :--- | :--- |
| **Database Engines** | *Not yet configured (Greenfield stage)* | No database engines (e.g., PostgreSQL, SQLite, MongoDB) are configured. |
| **ORM / Query Builders** | *Not yet configured (Greenfield stage)* | No ORMs (e.g., Drizzle, Prisma, Mongoose) have been installed. |
| **Storage Services** | *Not yet configured (Greenfield stage)* | No cloud or file storage services are configured. |
