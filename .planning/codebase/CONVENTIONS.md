# Quality & Style Conventions

This document establishes the code style, file naming, component architecture, styling foundations, and architectural conventions for the **National Logistics** project. Adhering to these standards ensures codebase consistency, readability, and long-term maintainability as the project scales.

---

## 1. Directory and File Naming Conventions

Consistency in naming allows developers to locate files quickly and prevents imports from breaking on case-sensitive file systems (such as Linux containers used in CI/CD).

### 1.1 Directory Naming
All directory names must use **kebab-case** (lowercase, hyphen-separated).
*   **Correct:** `src/components/shipment-tracker/`, `src/pages/driver-dashboard/`, `src/layouts/`
*   **Incorrect:** `src/components/ShipmentTracker/`, `src/pages/driverDashboard/`

### 1.2 File Naming Rules
*   **Astro Components:** Must use **PascalCase** with the `.astro` extension.
    *   *Examples:* `Button.astro`, `ShipmentCard.astro`, `SidebarNav.astro`
*   **Layout Components:** Must use **PascalCase** with the `.astro` extension, suffixed with `Layout` if necessary, or grouped inside a layouts directory.
    *   *Examples:* `MainLayout.astro`, `DashboardLayout.astro`
*   **TypeScript / JavaScript Files:** Must use **kebab-case** with `.ts` or `.js` extension.
    *   *Examples:* `format-date.ts`, `api-client.ts`, `auth-handler.ts`
*   **Stylesheets:** Must use **kebab-case** with `.css` extension.
    *   *Examples:* `global.css`, `theme-variables.css`
*   **Content Collections:** Must reside under `src/content/` and follow Astro content schema rules using kebab-case for directories and specific schema definitions.

---

## 2. Astro Component Styles & Anatomy

Astro components are the core building blocks of the UI. To maintain readability, every `.astro` file must follow a clean, standardized structure.

### 2.1 File Anatomy
Every Astro component should be split into three distinct zones, in the following order:

```astro
---
// 1. Component Script (TypeScript)
// - Imports (Third-party packages, components, types, helpers)
// - Component Props Interface declaration
// - Destructured Astro.props with default values
// - Component-level logic (fetching, sorting, formatting)

import type { HTMLAttributes } from 'astro/types';
import StatusBadge from './StatusBadge.astro';

interface Props extends HTMLAttributes<'div'> {
  shipmentId: string;
  trackingNumber: string;
  status: 'pending' | 'in-transit' | 'delivered' | 'delayed';
  eta?: string;
}

const { 
  shipmentId, 
  trackingNumber, 
  status, 
  eta = 'TBD', 
  class: className, 
  ...rest 
} = Astro.props;

// Logic execution
const formattedTracking = trackingNumber.toUpperCase();
---

<!-- 2. Component Template (HTML / JSX expressions) -->
<div class:list={['shipment-card p-4 border rounded-lg shadow-sm', className]} {...rest}>
  <div class="flex justify-between items-center mb-2">
    <span class="text-sm font-semibold text-gray-500">ID: {shipmentId}</span>
    <StatusBadge status={status} />
  </div>
  
  <p class="text-lg font-bold text-gray-900">{formattedTracking}</p>
  
  {eta && (
    <p class="text-sm text-gray-600 mt-2">
      <strong>ETA:</strong> {eta}
    </p>
  )}
</div>

<!-- 3. Component Styles (Scoped CSS) -->
<style>
  /* Use scoped styles sparingly or for custom component animations/variables */
  .shipment-card {
    transition: transform 0.2s ease-in-out;
  }
  .shipment-card:hover {
    transform: translateY(-2px);
  }
</style>
```

### 2.2 Component Standards
*   **Props Typing:** Always define a strict `interface Props` to leverage TypeScript's compile-time validation. Use `Astro.props` destructuring right at the start of the frontmatter script.
*   **Class Merging:** When creating reusable components that accept custom classes, always destructure `class: className` from `Astro.props` and merge it with internal classes using Astro's `class:list` directive.
*   **Scoped CSS:** Keep style tags scoped to the component. If a style needs to be global, add it to `src/styles/global.css` or use the `:global()` selector inside a scoped `<style>` block.
*   **Pure HTML over Client Islands:** Keep components static. Only introduce interactive Framework Islands (Preact, React, Vue) when runtime interactivity is absolutely required (e.g., dynamic search filters, interactive mapping dashboards).

---

## 3. TypeScript & Coding Style

A strict coding style prevents subtle bugs, speeds up onboarding, and ensures predictability.

*   **No Implicit Any:** Set `"noImplicitAny": true` in `tsconfig.json`. Every function parameter and object type must be explicitly typed.
*   **Prefer Interfaces over Types for Objects:** Use `interface` for components, pages, data definitions, and class shapes. Use `type` only for unions, tuples, or utility mappings.
*   **Null and Undefined Safeguards:** Always use optional chaining (`?.`) and nullish coalescing (`??`) when working with external data/APIs.
*   **Functional Programming Style:** Prefer pure, deterministic functions. Avoid global state modification, side effects in helpers, and excessive mutation of input parameters.
*   **Imports Order:**
    1.  Third-party packages (e.g., `'astro/config'`, `'react'`)
    2.  Absolute or aliased imports (e.g., `'@/components/Button.astro'`)
    3.  Relative imports (e.g., `'../utils/formatter'`)
    4.  CSS/Style imports

---

## 4. Design System Foundations & Tokens

To reflect the branding of **National Logistics**, all components must align to a strict token scale. The design tokens below define our visual character: precision, speed, reliability, and security.

### 4.1 Color System (Logistics Theme)
*   **Primary (Brand Identity):** Deep Ocean Blue (`#0F172A` / Slate-900) and Navy (`#1E3A8A` / Blue-900) representing authority, reliability, and trust.
*   **Accent (Alerts & Action):** High-visibility Amber (`#F59E0B` / Amber-500) and Emerald (`#10B981` / Emerald-500) representing fast movement and success.
*   **Neutral Palette:**
    *   *Backgrounds:* Pure White (`#FFFFFF`) and Light Off-Gray (`#F8FAFC` / Slate-50)
    *   *Borders & Dividers:* Slate-200 (`#E2E8F0`)
    *   *Typography:* Charcoal Slate-800 (`#1E293B`) for body, and Gray-600 (`#475569`) for subtext.
*   **Status Indicators:**
    *   `Pending`: Amber-500 / Amber-50 background (In Queue)
    *   `In-Transit`: Blue-500 / Blue-50 background (Moving)
    *   `Delivered`: Emerald-500 / Emerald-50 background (Completed)
    *   `Delayed / Exception`: Rose-500 / Rose-50 background (Action Required)

### 4.2 Spacing & Grid Scale
To maintain visual rhythm, use a **4px grid spacing system** (Tailwind equivalents):
*   `xs`: 4px (`1` / `0.25rem`) - For small gaps between icons and labels.
*   `sm`: 8px (`2` / `0.5rem`) - For internal padding of small components/badges.
*   `md`: 16px (`4` / `1rem`) - Standard layout padding, spacing between inputs.
*   `lg`: 24px (`6` / `1.5rem`) - Standard card padding, grid gaps.
*   `xl`: 32px (`8` / `2rem`) - Section padding, layout grids.
*   `xxl`: 48px (`12` / `3rem`) - Large vertical margins.

### 4.3 Typography Scale
*   **Font Family:** Inter or system sans-serif (`ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`).
*   **Sizing Hierarchy:**
    *   `H1` (Page Title): `2.25rem` (36px), `leading-tight`, `font-bold`
    *   `H2` (Section Title): `1.5rem` (24px), `leading-snug`, `font-semibold`
    *   `H3` (Card Header): `1.125rem` (18px), `leading-normal`, `font-medium`
    *   `Body`: `1rem` (16px), `leading-relaxed`, `font-normal`
    *   `Caption / Helper`: `0.875rem` (14px), `leading-normal`, `font-normal`
    *   `Badge / Tag`: `0.75rem` (12px), `leading-none`, `font-semibold`

---

## 5. Responsive Design & Accessibility (a11y) Standards

As a logistics application, accessibility in environments like high-glare vehicle mounts or small mobile screens is a business requirement.

### 5.1 Responsive Breakpoints
Follow Tailwind default breakpoints (Mobile-First approach):
*   `sm`: `640px` (Small tablets, large phones)
*   `md`: `768px` (Tablets, portrait)
*   `lg`: `1024px` (Laptops)
*   `xl`: `1280px` (Desktops)
*   `2xl`: `1536px` (Wide screens)

### 5.2 Accessibility Checklist
1.  **Semantic Elements:** Use `<main>`, `<header>`, `<footer>`, `<nav>`, `<section>`, and `<article>` tags appropriately. Avoid overuse of generic `<div>` containers.
2.  **Focus States:** Never disable focus borders globally (`outline: none` is forbidden). Always design an explicit, high-contrast `:focus-visible` ring.
3.  **Color Contrast:** Ensure a minimum color contrast ratio of **4.5:1** for body text and **3:1** for large heading text (WCAG AA standard).
4.  **Touch Targets:** Interactive targets (buttons, links, form inputs) must have a minimum clickable area of **44x44 CSS pixels** to prevent misclicks on mobile devices.
5.  **Alt Text:** All image and SVG assets must have meaningful `alt` descriptions or `aria-hidden="true"` tags if purely decorative.

---

## 6. Proposed Code Quality Setup (Linter & Formatter)

To enforce these quality guidelines programmatically as development progresses, we propose installing and configuring the following toolchain in the next phase:

*   **Prettier:** For deterministic code formatting.
    *   *Proposed plugin:* `prettier-plugin-astro` to format Astro markup.
*   **ESLint:** For syntax auditing and anti-pattern warnings.
    *   *Proposed plugin:* `@typescript-eslint/eslint-plugin`, `eslint-plugin-astro`, and `eslint-plugin-jsx-a11y` for automated accessibility audits.
*   **Husky & lint-staged:** To prevent formatting violations or compile errors from being committed to source control.
