# App-only feature (excalidraw.com)

Use when the change targets **`excalidraw-app/`** only and must **not** bloat **`@excalidraw/excalidraw`**.

## Boundaries

- **App shell:** routing, welcome, share dialogs, Plus hooks, Firebase, collab wiring — **`excalidraw-app/`**.
- **Reusable editor:** canvas, actions, element model — **`packages/excalidraw/`** (published npm package).

Prefer **props and callbacks** into `<Excalidraw />` rather than forking core editor logic into the app.

## State

- **App-level Jotai:** **`excalidraw-app/app-jotai.ts`** — not raw `jotai`.
- **Editor state:** still **`AppState` + elements** inside the package; app listens via **`onChange`** / APIs as today.

## Config & services

- **Env:** `VITE_APP_*` in `.env.development` / `.env.production`.
- **Firebase / WebSocket URLs:** see app `data/` and `collab/`; reuse **`packages/excalidraw/data`** for encode/decode and reconcile — do not duplicate JSON formats.

## UI placement

- Components in **`excalidraw-app/components/`** with colocated **`.scss`** when needed (match **`AppSidebar.scss`** patterns).

## Finish

`yarn test:typecheck`, tests under **`excalidraw-app/tests/`** if applicable, `yarn fix`.

## See also

`dev-docs/docs/a-docs/03-codebase-navigation.md` (app-level data), `.cursor/rules/excalidraw-app-integration.mdc`, `excalidraw-ui-app.mdc`.
