# CO2 Emission Checker — Agent Guide

This document summarizes everything an AI coding agent needs to know about this project.

---

## Project Overview

This is a **Vue.js 3 single-page application (SPA)** that allows users to search for Hugging Face machine-learning models and retrieve their CO2 emission data. The goal is to promote sustainable and responsible AI usage by surfacing the environmental impact of individual models.

The app consists of three pages:
- **Home (`/`)** — Landing page with an introduction to the project.
- **About (`/about`)** — Explains the environmental impact of AI models and cites sources.
- **Checker (`/checker`)** — A search form that queries the public Hugging Face API (`https://huggingface.co/api/models/{modelName}`) and displays model details including CO2 emissions, pipeline tag, safetensors count, and downloads.

The project is scaffolded with the official Vue 3 + Vite + TypeScript starter and uses Tailwind CSS for styling.

---

## Technology Stack

| Layer              | Technology                          |
|--------------------|-------------------------------------|
| Framework          | Vue 3.4 (Composition API)           |
| Language           | TypeScript ~5.4                     |
| Build Tool         | Vite 5.2                            |
| Router             | Vue Router 4.3 (history mode)       |
| State Management   | Pinia 2.1                           |
| Styling            | Tailwind CSS 3.4 + PostCSS          |
| Unit Testing       | Vitest 1.4 + jsdom + @vue/test-utils|
| Linting / Formatting | ESLint 8 + Prettier 3             |
| Package Manager    | Bun (lockfile: `bun.lockb`)         |

---

## Project Structure

```
├── public/                     # Static files served at root
│   └── favicon.ico
├── src/
│   ├── assets/                 # Images, global CSS
│   │   ├── main.css            # Global app wrapper styles
│   │   ├── base.css            # CSS variables (light/dark) & reset
│   │   ├── style.css           # Tailwind directives (@tailwind base/components/utilities)
│   │   └── *.png / *.jpg / *.svg
│   ├── components/             # Reusable Vue components
│   │   ├── AboutProject.vue    # About page content
│   │   ├── FormItem.vue        # Hugging Face search form & results display
│   │   ├── TheWelcome.vue      # Home page content
│   │   └── __tests__/          # Unit tests for components
│   │       └── HelloWorld.spec.ts
│   ├── router/
│   │   └── index.ts            # Vue Router config (3 routes)
│   ├── stores/
│   │   └── counter.ts          # Example Pinia store (not used in UI)
│   ├── views/                  # Page-level view components
│   │   ├── HomeView.vue
│   │   ├── AboutView.vue
│   │   └── CheckerView.vue
│   ├── App.vue                 # Root layout (header nav + footer)
│   ├── main.ts                 # App bootstrap
│   └── style.css               # Tailwind directives (imported by main.ts)
├── index.html                  # HTML entry point
├── vite.config.ts              # Vite config (aliases, plugins)
├── vitest.config.ts            # Vitest config (merges Vite, jsdom env)
├── tailwind.config.js          # Tailwind content paths
├── postcss.config.js           # PostCSS plugins (tailwindcss, autoprefixer)
├── tsconfig.json               # TS project references
├── tsconfig.app.json           # App TS config (includes `@/*` alias)
├── tsconfig.node.json          # Node / config TS config
├── tsconfig.vitest.json        # Vitest TS config
├── .eslintrc.cjs               # ESLint rules
├── .prettierrc.json            # Prettier rules
└── package.json                # Scripts & dependencies
```

---

## Build, Dev & Test Commands

All commands are run via **Bun** (preferred) or npm:

```bash
# Install dependencies
bun install

# Start the Vite dev server (default port 5174)
bun run dev

# Production build (type-check + vite build)
bun run build

# Preview the production build locally
bun run preview

# Run unit tests in watch mode (Vitest)
bun run test:unit

# Run type-check only
bun run type-check

# Lint and auto-fix
bun run lint

# Format source files with Prettier
bun run format
```

---

## Code Style Guidelines

### TypeScript / Vue
- Use the **Composition API**.
  - Prefer `<script setup lang="ts">` for simple components.
  - Use `defineComponent({ setup() { ... } })` only when you need more explicit control (e.g., `FormItem.vue`).
- All components are written in `.vue` SFCs with `lang="ts"`.

### Prettier Configuration (enforced)
- `semi: false` — **no semicolons**
- `singleQuote: true`
- `tabWidth: 2`
- `printWidth: 100`
- `trailingComma: none`

### ESLint
- Extends `plugin:vue/vue3-essential`, `eslint:recommended`, `@vue/eslint-config-typescript`, and `@vue/eslint-config-prettier/skip-formatting`.
- Run `bun run lint` before committing.

### Tailwind CSS
- Utility-first styling. Almost all UI styling is done via Tailwind classes in templates.
- Global styles live in `src/assets/base.css` (CSS variables & reset) and `src/assets/main.css` (app wrapper).
- Tailwind directives are in `src/style.css`.

### Path Aliases
- `@/` resolves to `src/` in both Vite and TypeScript.
- Use `@/components/FormItem.vue` instead of relative paths where possible.

---

## Testing Instructions

- **Framework:** Vitest with `jsdom` environment.
- **Utilities:** `@vue/test-utils` for mounting components.
- Tests live next to the components they test: `src/components/__tests__/*.spec.ts`.
- Test files are **excluded** from the app TypeScript build (`tsconfig.app.json`).
- Run: `bun run test:unit`

> **Note:** The existing test file (`HelloWorld.spec.ts`) references a `HelloWorld.vue` component that no longer exists in the codebase. Update or remove this test if it causes failures.

---

## Router & Navigation

Defined in `src/router/index.ts`:

| Route     | Component    | Loading Strategy |
|-----------|--------------|------------------|
| `/`       | `HomeView`   | Eager            |
| `/about`  | `AboutView`  | Lazy-loaded      |
| `/checker`| `CheckerView`| Lazy-loaded      |

The root layout (`App.vue`) renders a shared header with `RouterLink` navigation and a footer.

---

## State Management

- **Pinia** is installed and instantiated in `main.ts`.
- The only existing store is `src/stores/counter.ts` (a demo store). It is **not wired into the UI**.
- If you add new stores, follow the same pattern: `defineStore('id', () => { ... })` using the Composition-API-style setup function.

---

## External API

The checker form (`FormItem.vue`) makes an unauthenticated `fetch` to:

```
GET https://huggingface.co/api/models/{modelName}
```

The response is mapped to a local `ModelData` interface. No API keys or tokens are required.

---

## Deployment

The application is deployed to **Netlify** (`co2-emission-checker-ai-models.netlify.app`).

Standard Vite build output (`dist/`) is used for static hosting. No server-side runtime is required.

---

## Security Considerations

- The app is a static SPA. There is no backend, authentication, or secrets file.
- External fetches go to the public Hugging Face API.
- The footer loads an external image from `app.greenweb.org` for green-hosting verification.
- No `.env` file or sensitive configuration is present.
