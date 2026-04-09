# AGENTS.md

Persistent context for AI coding agents and developers working in this repository. Aligns with **`dev-docs/docs/a-docs/`**, **`.cursor/rules/`**, and project tooling.

---

## Project

**Excalidraw monorepo** — open-source collaborative virtual whiteboard: hand-drawn style canvas, real-time collaboration, end-to-end encrypted sharing, export (PNG, SVG, JSON).

| Layer | Role |
|-------|------|
| **`packages/excalidraw`** | Published npm package **`@excalidraw/excalidraw`** — embeddable React editor |
| **`excalidraw-app`** | Full web app (excalidraw.com) — Vite app, Firebase, Socket.io collab, local persistence |
| **`packages/element`** | Element model, scene, transforms, bindings, z-order (`@excalidraw/element`) |
| **`packages/common`** | Shared constants and utilities (`@excalidraw/common`) |
| **`packages/math`** | Geometry only, no UI deps (`@excalidraw/math`) |
| **`packages/utils`** | Export and bounds helpers for consumers (`@excalidraw/utils`) |
| **`examples/*`** | Integration samples (e.g. Next.js, Vite) |
| **`dev-docs/`** | Docusaurus site; deep onboarding lives in **`dev-docs/docs/a-docs/`** |

**Tech (high level):** React 19, TypeScript (strict), Yarn Classic workspaces, Vite (app), esbuild (packages), Vitest + jsdom + Testing Library, HTML Canvas 2D + Rough.js, SCSS, Jotai (scoped providers), Socket.io + Firebase in the app.

---

## Teams & audiences (who cares about what)

| Audience | Focus |
|----------|--------|
| **Library consumers** | Stable **`@excalidraw/excalidraw`** API, bundle size, SSR (e.g. Next.js example), minimal breaking changes |
| **Product / web app** | **`excalidraw-app`**: collab, auth-adjacent flows, env-specific config, hosting |
| **Core editor contributors** | **`packages/excalidraw`** + **`packages/element`**: actions, rendering, data format, undo/redo |
| **Infrastructure / release** | `scripts/`, `.github/workflows`, Docker/nginx as used by the project |

When unsure whether code belongs in the **library** or the **app**, default to: reusable editor behavior → **`packages/excalidraw`** (or lower packages); excalidraw.com-only → **`excalidraw-app`**.

---

## Architecture (mental model)

- **State:** Hybrid — large **`AppState`** object + immutable **`elements`** array; **Jotai** atoms for fine-grained UI (`editor-jotai.ts`, `app-jotai.ts`); **`Scene`** for element ordering (fractional indices); **History** uses deltas for undo/redo.
- **User operations:** Prefer the **action** system — `ActionManager` dispatches **`perform` → `ActionResult`** (`elements`, `appState`, `files`, **`captureUpdate`** for history). Not a single Redux-style store.
- **Rendering:** **Multi-canvas** — static (committed shapes, Rough.js, cache-friendly), interactive (selection, handles, cursors), new-element preview. Avoid redrawing the static layer on every pointer move.
- **Collaboration (app):** Socket.io for realtime; Firebase for durable storage; **`reconcileElements`** merges by element **version**; encryption key in URL **hash** (fragment), not query string.
- **Dependencies:** Flow **downward** — `common` / `math` have no internal `@excalidraw/*` deps; `element` → `common`, `math`; `excalidraw` → all; `excalidraw-app` → `@excalidraw/excalidraw` + app services.

**Heavy-touch files:** `packages/excalidraw/components/App.tsx` (class-based editor core — pointer, keyboard, tools). Treat changes there as high blast radius.

---

## Commands

Use **Yarn Classic v1** from the **repository root** (`packageManager: yarn@1.22.22`). Node **>= 18**.

| Command | Purpose |
|---------|---------|
| `yarn` | Install all workspace dependencies |
| `yarn start` | Dev server for **`excalidraw-app`** (default port from `.env.development`, often `3001`) |
| `yarn build` | Production build of the web app |
| `yarn build:packages` | Build npm packages only (`common`, `math`, `element`, `excalidraw`) |
| `yarn test` / `yarn test:app` | Vitest (watch mode by default for `test:app`) |
| `yarn test:update` | Tests + update snapshots — run before commit when snapshots are intentional |
| `yarn test:typecheck` | `tsc` for the monorepo |
| `yarn test:code` | ESLint |
| `yarn test:other` | Prettier check |
| `yarn test:all` | CI-like: typecheck + lint + prettier + tests (no watch) |
| `yarn fix` | Prettier write + ESLint fix |
| `yarn clean-install` | Remove workspace `node_modules` and reinstall |

**Examples:** `yarn start:example` — build packages + run browser example. **`VITE_APP_*`** env vars live in `.env.development` / `.env.production`.

---

## Repository map (where to look)

| Topic | Location |
|-------|-----------|
| Actions | `packages/excalidraw/actions/` — `manager.tsx`, `types.ts`, `action*.ts(x)` |
| Main editor | `packages/excalidraw/components/App.tsx` |
| Canvases | `packages/excalidraw/components/canvases/` |
| Renderers | `packages/excalidraw/renderer/` |
| Serialization / reconcile | `packages/excalidraw/data/` — `json.ts`, `restore.ts`, `reconcile.ts`, `encryption.ts` |
| Collab | `excalidraw-app/collab/` |
| App persistence | `excalidraw-app/data/` |
| i18n (library UI) | `packages/excalidraw/locales/` + `useI18n` |
| Tests setup | Root `setupTests.ts`, `vitest.config.mts` |
| Onboarding docs | `dev-docs/docs/a-docs/README.md` (index) |
| Agent rules | `.cursor/rules/*.mdc` |
| Slash-command prompts | `.cursor/commands/*.md` |

---

## Code style & conventions

- **TypeScript:** **`strict: true`** (root `tsconfig.json`). Use **`import type { … }`** for type-only imports (ESLint enforced).
- **React:** Prefer **functional components** and hooks for **new UI**; the core **`App`** remains a **class** — follow existing patterns when touching it.
- **Exports:** Prefer **named exports** for new modules; match neighboring files when editing legacy code.
- **Imports:** Use path aliases **`@excalidraw/common`**, `@excalidraw/element`, `@excalidraw/math`, `@excalidraw/utils`. **Inside `packages/excalidraw`**, do **not** import from the package barrel **`index.tsx`** — import the **specific module**.
- **Jotai:** Use **`packages/excalidraw/editor-jotai.ts`** or **`excalidraw-app/app-jotai.ts`** — do not import **`jotai`** directly in feature code.
- **Styling:** SCSS, often colocated (e.g. `Component.scss`). Library styles frequently nest under **`.excalidraw`**.
- **Naming:** Components **PascalCase**; utilities/modules **camelCase**; actions **`actionName.ts`**; tests **`*.test.ts(x)`**.
- **i18n:** No hardcoded English for **library** user-visible strings — use **`locales/`** and **`t(...)`**.

---

## State & data rules

- **Elements are immutable** — use **`mutateElement`** (and correct **version** / **versionNonce** behavior). Direct field assignment breaks render optimization, history, and collaboration.
- **Z-order:** Fractional **`index`** on elements; prefer **`Scene`** APIs for reordering.
- **Schema changes:** Update **`types.ts`**, **`newElement.ts`**, **`restore.ts`**, and rendering/export as needed.
- **Collaboration:** Be careful with **`reconcileElements`** and **tombstones** (`isDeleted`); test concurrent editing when changing element structure or versioning.

---

## Testing

- **Vitest** + **jsdom** + **@testing-library/react**; **`packages/excalidraw/tests/test-utils`** (`render`, `unmountComponent`) and helpers **`API`**, **`Keyboard`**, etc.
- Run **`yarn test:update`** when snapshots change intentionally; **review snapshot diffs** before committing.
- Path aliases in tests must match **`vitest.config.mts`**.

---

## Constraints & non-goals

- **Package manager:** Yarn Classic workspaces — do not switch to npm or Yarn Berry without an explicit team decision.
- **Dependencies:** Avoid new **heavy** dependencies in **`packages/excalidraw`** without considering **bundle size** and **`.github/workflows/size-limit.yml`**. Discuss significant additions.
- **No direct mutation** of element objects or ad-hoc editor state that bypasses **actions** when the change should be **undoable** and consistent.
- **SSR / embeds:** Avoid browser-only APIs on code paths used by **Next.js** (or other SSR) without guards or dynamic import patterns consistent with the codebase.
- **Security:** Do not log full **share URLs** or move **encryption keys** from the URL **hash** to query strings.

---

## Contributing & external docs

- **`CONTRIBUTING.md`** points to [Excalidraw contributing docs](https://docs.excalidraw.com/docs/introduction/contributing).
- First-time deep dive: start with **`dev-docs/docs/a-docs/09-tldr-new-devs.md`**, then **`01-architecture-overview.md`**, **`03-codebase-navigation.md`**, **`08-adding-features.md`**.

---

## Cursor integration (this repo)

- **Rules:** `.cursor/rules/` — monorepo layers, imports, element model, actions, data/collab, app, rendering, testing, UI (each includes **How to verify** where relevant).
- **Commands:** `.cursor/commands/` — e.g. **`update-state-flow`**, **`new-action`**, **`pre-pr-check`**, **`element-schema-change`**, **`add-locale-string`**, **`debug-collab`**, **`rendering-change`**, etc. Invoke via **`/`** in Cursor chat.

When answering in this project, prefer citing **real paths** and **existing patterns** over generic React advice.
