# Update editor state (actions & related touchpoints)

Use this flow when adding or changing **how the Excalidraw editor state is updated** (elements, `AppState`, files, undo/redo, shortcuts, or reactive UI). Follow branches that apply; skip the rest.

## 0. Classify the change

| Need | Primary path |
|------|----------------|
| User-triggered change (menu, shortcut, toolbar, command palette) | **Action** + `ActionManager` |
| New/changed field on a **drawing element** | **`@excalidraw/element`** types + factories + **`restore`** + often renderer/export |
| New/changed **editor chrome** state (tool, zoom, selection flags, …) | **`AppState`** / `types.ts` + **`appState.ts`** defaults + readers (hooks / UI) |
| Shared **reactive** UI state (panels, library, collab handles) | **Jotai** via `editor-jotai.ts` or `excalidraw-app/app-jotai.ts` |
| Behavior during **pointer / drag / tool** lifecycle | **`App.tsx`** handlers (keep small; prefer actions for pure state transforms) |
| **Binary attachments** (images, …) | `ActionResult.files`, `replaceFiles`; align with existing file APIs |

State is **not** a single Redux store: it combines **`AppState` + elements**, **Jotai** atoms, **`Scene`**, and **History** deltas.

---

## 1. New or changed **action** (most common)

`ActionResult` shape (see `packages/excalidraw/actions/types.ts`):

- `elements` — new element array or omit/null per existing patterns
- `appState` — partial `AppState` updates
- `files` / `replaceFiles` — when binary files change
- `captureUpdate` — **required**; controls **undo/redo** (`CaptureUpdateAction` from `@excalidraw/element`). Match a similar existing action.
- Return **`false`** to no-op.

**Steps:**

1. Add `packages/excalidraw/actions/actionYourFeature.ts` (or `.tsx`) using `register()` like sibling `action*.ts` files.
2. Implement `perform(elements, appState, formData, app)` → `ActionResult` or `false`.
3. Optional: `keyTest`, `predicate`, `PanelComponent`, `trackEvent`, icons — mirror nearby actions.
4. **Export** the action from `packages/excalidraw/actions/index.ts` (required for registration).
5. If it needs a **shortcut**, update `packages/excalidraw/actions/shortcuts.ts` and/or wire UI in the relevant component (`Actions.tsx`, menus, command palette).
6. **Tests:** `packages/excalidraw/actions/actionYourFeature.test.tsx` using `tests/test-utils`, `API`, `Keyboard`, etc.
7. Run `yarn test:typecheck`, `yarn test:update` if snapshots change, `yarn fix`.

**Imports:** `import type` for types; inside `packages/excalidraw` do not import from the package barrel `index.tsx`. Jotai only from `editor-jotai.ts` / `app-jotai.ts`.

---

## 2. New **element** field or behavior

1. **`packages/element/src/types.ts`** — extend the right element / base type.
2. **`packages/element/src/newElement.ts`** (and any focused factories) — defaults for new elements.
3. **`packages/excalidraw/data/restore.ts`** — migrations / defaults for loaded or pasted data.
4. If it affects appearance or export: **`packages/excalidraw/renderer/staticScene.ts`**, **`staticSvgScene.ts`**, and **`scene/export.ts`** as needed.
5. **Immutability:** use **`mutateElement`** (and version discipline); never mutate element objects in place.
6. **Collaboration:** reconcile uses **version**; test concurrent edits if z-order or structure changes (`packages/excalidraw/data/reconcile.ts`).

---

## 3. New **`AppState` / UI editor state** field

1. **`packages/excalidraw/types.ts`** — `AppState` / `UIAppState` (or relevant subsection).
2. **`packages/excalidraw/appState.ts`** — default value and any reset paths.
3. Update **readers** (hooks like `useAppStateValue`, components, `App.tsx`) and any **serialization** if the field must persist (JSON / localStorage / share link) — follow how similar fields are saved in `data/json.ts` and app `LocalData` if applicable.

---

## 4. **Jotai** (reactive UI, not the canvas element array)

- Library/editor scope: **`packages/excalidraw/editor-jotai.ts`**.
- Hosted app scope: **`excalidraw-app/app-jotai.ts`**.
- Do not import `jotai` directly in feature code.

Use atoms for UI that many components subscribe to; keep **elements** and core **`AppState`** updates flowing through **actions** when the change must be **undoable** and consistent with the rest of the editor.

---

## 5. **Pointer / tool** wiring

If the change starts from canvas interaction:

- **`packages/excalidraw/components/App.tsx`** — `handleCanvasPointerDown` / `Move` / `Up` (and related). Prefer delegating the final state commit to an **action** or shared helper so `perform` stays testable.
- New **tool type**: `ActiveTool` in `types.ts`, toolbar UI, shortcuts, and `App.tsx` tool branches (see `dev-docs/docs/a-docs/08-adding-features.md`).

---

## 6. **History (undo/redo)**

- Incorrect **`captureUpdate`** breaks undo/redo or records noise.
- History uses **deltas**; avoid assumptions that break after **collab reconcile**.

---

## 7. **Persistence, export, collab**

- **JSON / clipboard / share:** `packages/excalidraw/data/json.ts`, `blob.ts`, app `data/` for backend.
- **Collaboration:** `excalidraw-app/collab/` + **`reconcileElements`**; encryption keys stay in URL **hash**.

---

## 8. Checklist before PR

- [ ] Types and defaults updated (`types.ts`, `appState.ts`, `newElement.ts`, `restore.ts` as needed)
- [ ] Action exported in `actions/index.ts` with correct `captureUpdate`
- [ ] Shortcuts / UI entry points wired
- [ ] Renderer / export updated if visual or SVG/PNG output changes
- [ ] Tests added or updated; `yarn test:update` if snapshots intentional
- [ ] `yarn test:typecheck` and `yarn fix`
- [ ] User-visible strings use **`locales/`** (library UI)

## Reference docs & rules

- `dev-docs/docs/a-docs/08-adding-features.md`
- `dev-docs/docs/a-docs/04-key-business-flows.md`
- `.cursor/rules/excalidraw-actions-state.mdc`, `excalidraw-element-model.mdc`, `excalidraw-data-collab.mdc`, `excalidraw-typescript-imports.mdc`
