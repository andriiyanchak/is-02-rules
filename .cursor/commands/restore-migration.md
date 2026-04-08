# Restore & migration (`restore.ts`)

Use when **saved files**, **clipboard**, or **shared links** must keep working after changing element or app state shape.

## Primary file

- **`packages/excalidraw/data/restore.ts`** — `restoreElements`, `restoreAppState`, `restoreLibraryItems`, version bumps where required.

## Goals

1. **Old JSON** still parses — add defaults for new fields; rename fields with explicit migration steps if needed.
2. **Invalid / partial data** — normalize defensively; match style of existing restore branches.
3. **Collaboration** — after restore, **element versions** must remain consistent with reconciliation expectations (see **`bumpElementVersions`** and related helpers in this file / callers).

## Related

- **`packages/element/src/types.ts`** — source of truth for element shape.
- **`packages/excalidraw/data/json.ts`** — serialization / types for file format.
- **Tests** — add fixtures or extend tests under **`packages/excalidraw/tests/`** or **`packages/element/tests/`** for old-format samples when possible.

## Checklist

- [ ] New field has a default in **`restore.ts`**
- [ ] No regression loading a **minimal** and a **real** saved `.excalidraw` file
- [ ] Clipboard paste from an **old** build still works if users mix versions
- [ ] `yarn test:typecheck` and targeted tests pass

## See also

`element-schema-change.md`, `dev-docs/docs/a-docs/05-important-components.md` (restore / migration), `.cursor/rules/excalidraw-data-collab.mdc`.
