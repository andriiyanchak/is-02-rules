# Rendering / canvas change

Use when changing **how elements are drawn** or how **canvas layers** behave.

## Architecture

- **Static canvas** — committed elements, **Rough.js**, should **not** redraw every pointer move.
- **Interactive canvas** — selection, handles, snaps, remote cursors.
- **New element canvas** — in-progress shape before commit.

Orchestration lives under **`packages/excalidraw/components/canvases/`**; core drawing in **`packages/excalidraw/renderer/`**.

## Files

| Concern | Typical files |
|--------|----------------|
| Static bitmap | `renderer/staticScene.ts` |
| Selection overlay | `renderer/interactiveScene.ts` |
| SVG export | `renderer/staticSvgScene.ts` |
| Export to PNG / clipboard | `scene/export.ts` |

## Performance

- Preserve **caching** of Rough drawables (tied to element version — do not recreate all drawables on every frame).
- Avoid pushing static-layer updates from **`pointerMove`** unless necessary.
- Stress-test with **many elements** (hundreds+) after changes.

## Consistency

- If canvas appearance changes, check **SVG export** matches for the same scene where applicable.

## Tests / checks

- Relevant renderer tests or visual snapshots; `yarn test:app` for affected areas.
- `yarn test:typecheck`

## See also

`dev-docs/docs/a-docs/01-architecture-overview.md` (rendering), `.cursor/rules/excalidraw-rendering.mdc`, `excalidraw-canvas-components.mdc`.
