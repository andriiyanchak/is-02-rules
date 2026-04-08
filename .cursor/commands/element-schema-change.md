# Element schema change

Use when adding or changing fields on **drawing elements** (shapes, text, arrows, frames, images, etc.).

## 1. Types

- **`packages/element/src/types.ts`** — extend the correct element interface or shared base type.

## 2. Creation defaults

- **`packages/element/src/newElement.ts`** (and any specialized factories) — set defaults for **new** elements.

## 3. Load / paste / migrate

- **`packages/excalidraw/data/restore.ts`** — defaults and migrations for **old** JSON, clipboard, and shared links so older files still open.

## 4. Mutation

- Use **`mutateElement`** (or established helpers). **Never** mutate element objects in place without proper **version** / **versionNonce** behavior.

## 5. Ordering / scene

- If z-order or structure changes: **`packages/element/src/Scene.ts`** and **fractional `index`** rules — prefer Scene APIs over hand-rolled indices.

## 6. Rendering & export

- Canvas: **`packages/excalidraw/renderer/staticScene.ts`**
- SVG: **`packages/excalidraw/renderer/staticSvgScene.ts`**
- PNG / export pipeline: **`packages/excalidraw/scene/export.ts`** as needed

## 7. Serialization

- **`packages/excalidraw/data/json.ts`** (and related types) if the field must round-trip in `.excalidraw` JSON.

## 8. Collaboration

- **`packages/excalidraw/data/reconcile.ts`** — merging uses **version**; deleted elements stay as tombstones for a reason.
- Test **two clients** editing the same scene when the change affects concurrent updates.

## 9. Tests

- Add or extend tests under **`packages/element/tests/`** and **`packages/excalidraw/tests/`** as appropriate.

## Finish

`yarn test:typecheck`, `yarn test:update`, `yarn fix`.

## See also

`restore-migration.md`, `.cursor/rules/excalidraw-element-model.mdc`, `excalidraw-data-collab.mdc`.
