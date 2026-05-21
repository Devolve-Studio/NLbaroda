# Structure Design Contract

This document outlines the detailed folder structure, page routing system, state management, and asset-handling mechanisms for the **National Logistics** platform.

---

## 1. Directory Tree Layout

The following tree diagram represents the standard, organized codebase layout for a growing Astro application. This structure separates core source code, configurations, public assets, and GSD planning metadata:

```
national-logistics/
├── .planning/                     # GSD lifecycle specifications and workspace documents
│   └── codebase/                  # Codebase maps (STACK, STRUCTURE, ARCHITECTURE, etc.)
├── .vscode/                       # Editor workspace settings
├── public/                        # Unprocessed static assets (served at root /)
│   ├── favicon.ico
│   ├── favicon.svg
│   └── robots.txt
├── src/                           # Primary source code directory
│   ├── components/                # Reusable UI component modules
│   │   ├── common/                # Buttons, Inputs, Modals, etc.
│   │   ├── features/              # Feature-specific islands (e.g., tracking, booking)
│   │   └── theme/                 # Styling-oriented UI elements
│   ├── layouts/                   # Global page templates / document shells
│   │   └── RootLayout.astro       # Main layout with HTML core and head metadata
│   ├── pages/                     # File-based routing system (pages & API endpoints)
│   │   └── index.astro            # Homepage entrypoint
│   ├── styles/                    # Global stylesheets and design tokens
│   │   └── global.css
│   └── utils/                     # Shared JS/TS helper utilities and API clients
│       └── formatters.ts
├── astro.config.mjs               # Astro framework configuration
├── package.json                   # Project scripts and dependencies
├── tsconfig.json                  # TypeScript compiler rules
└── pnpm-lock.yaml                 # Pnpm locked dependency tree
```

---

## 2. Directory Purposes

### `.planning/`
Stores structural, architectural, technical, and convention contracts defined by GSD subagents. It preserves project continuity, architecture decisions, and roadmap lists.
*   **`.planning/codebase/`:** Contains maps like `ARCHITECTURE.md`, `STRUCTURE.md`, `STACK.md`, etc., generated during mapper phases.

### `public/`
Contains static resources that Astro does not process. Any files placed here are copied directly into the root of the build output folder (`dist/`).
*   **Best Practice:** Use this for resources that do not change or do not require compilation (e.g., standard site icons, `robots.txt`, raw configuration `.json` files, or static legacy downloads).

### `src/`
The core directory containing all compilation-bound code. Files here are handled, optimized, and bundled by Astro's built-in Vite compiler.
*   **`src/components/`:** Home for modular page sections, reusable widgets, and UI framework components. They are structured into `common/` (basic atomic buttons and icons) and `features/` (complex interactive island blocks).
*   **`src/layouts/`:** Defines structural shells containing `<html>`, `<head>`, `<body>`, and meta templates. Layouts receive content pages as dynamic slots (`<slot />`).
*   **`src/pages/`:** Astro's routing engine directory. Every `.astro`, `.md`, or `.ts` file in this directory maps directly to a public URL endpoint.
*   **`src/styles/`:** Contains global style parameters, design tokens, and CSS imports.
*   **`src/utils/`:** Holds standalone helper functions, date formatters, validation files, and API services used throughout the application.

---

## 3. Page Routing System (`src/pages/`)

Astro leverages a file-based routing mechanism, resolving endpoints based on the file hierarchy within the `src/pages/` directory.

### Routing Table Examples

| File Location | Resolved Route | Type | Purpose |
| :--- | :--- | :--- | :--- |
| `src/pages/index.astro` | `/` | HTML | Platform Homepage |
| `src/pages/about.astro` | `/about` | HTML | Static About Us Page |
| `src/pages/tracking/index.astro` | `/tracking` | HTML | Main Tracking Entry Dashboard |
| `src/pages/tracking/[id].astro` | `/tracking/NL-8742` | Dynamic HTML | Individual Shipment Detail Page |
| `src/pages/api/v1/ship.ts` | `/api/v1/ship` | JSON | Backend API endpoint |

### Handling Dynamic Routes (`[param].astro`)
For pages with dynamic segments (like individual shipment codes `/tracking/[id]`), the routing matches via brackets:
1.  **Static Site Generation (SSG) Mode:** The page must export a `getStaticPaths()` function returning a list of valid parameter paths.
2.  **Server-Side Rendering (SSR) Mode:** The route is resolved dynamically at request time, accessing parameters via `Astro.params.id`.

### Static vs. Dynamic Framework Layout
```
src/pages/
├── index.astro              <-- Accessible at / (Home)
├── about.astro              <-- Accessible at /about
├── tracking/
│   ├── index.astro          <-- Accessible at /tracking
│   └── [id].astro           <-- Accessible at /tracking/:id (Dynamic)
└── api/
    └── track.ts             <-- Accessible at /api/track (Server Route)
```

---

## 4. State Management

Due to the modular nature of the Islands Architecture, standard unified SPA context systems are not ideal. The platform uses tailored state paradigms to handle communication across different boundaries:

### Parent-to-Child (Build Time)
*   **Mechanism:** Standard HTML attributes and TypeScript interfaces.
*   **Scope:** Server-to-server or server-to-client parameter passing. Astro layouts and pages pass static data down into individual components at compile time using `Astro.props`.

### Inter-Island State Communication (Client Runtime)
When client-side components (e.g., a booking calculator island and a mini-cart island) need to share dynamic reactive state:
*   **Nano Stores:** A lightweight, framework-agnostic state manager is highly recommended. It compiles to less than 1 KB and coordinates states across isolated components without massive context wrappers.
*   **Browser Web Storage:** Standard `localStorage` is used for caching long-term preferences, user authentication tokens, and drafts.

---

## 5. Asset Handling

Astro provides highly optimized compilers to process frontend assets efficiently:

### Image Optimization
*   **Location:** Source images must be stored within the `src/` directory (e.g., `src/assets/`).
*   **Optimization:** Using Astro's built-in `<Image />` component:
    ```astro
    ---
    import { Image } from 'astro:assets';
    import logoImage from '../assets/logo.png';
    ---
    <Image src={logoImage} alt="National Logistics" width={200} height={50} format="webp" />
    ```
    This generates WebP formats dynamically, crops dimensions, and implements lazy loading natively to optimize core web vitals.

### CSS and Stylesheet Management
*   **Scoped Styling:** Astro styles are scoped by default using automatic hash selectors:
    ```astro
    <style>
      h1 { color: #0284c7; } /* Scoped specifically to this component */
    </style>
    ```
*   **Global Styling:** Imported inside layout headers or layouts via standard JS/CSS imports:
    ```typescript
    import '../styles/global.css';
    ```
