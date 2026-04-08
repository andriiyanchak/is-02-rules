# Debug collaboration

Use when **live collaboration**, **shared rooms**, or **remote cursors** misbehave (drift, lost updates, duplicates, stale scenes).

## Mental model

- **Socket.io** (`excalidraw-app/collab/Portal.tsx`) carries realtime updates.
- **Firebase** (`excalidraw-app/data/firebase.ts`) persists encrypted scene + files for rooms.
- **Merge** uses **`reconcileElements`** in **`packages/excalidraw/data/reconcile.ts`** (version-based; ties / tombstones matter).
- **Encryption key** is in the URL **hash** — not sent to HTTP servers; do not log full share URLs in production debug.

## Investigation order

1. **Repro** — two browsers, same room, minimal steps (draw, delete, reorder, image paste).
2. **Local vs remote** — does the bug appear offline? If only online, suspect **reconcile**, **socket**, or **Firebase** timing.
3. **`Collab.tsx`** — `startCollaboration`, `syncElements`, `handleRemoteSceneUpdate`, save queues.
4. **`reconcile.ts`** — same element id, **version** / **isDeleted** / ordering after merge.
5. **Element mutations** — direct mutation or missing **version** bumps break reconciliation and undo.
6. **Images** — `FileManager`, Firebase file prefix, lazy fetch paths for peers’ assets.

## Files to read first

- `excalidraw-app/collab/Collab.tsx`
- `excalidraw-app/collab/Portal.tsx`
- `packages/excalidraw/data/reconcile.ts`
- `packages/excalidraw/data/encryption.ts`

## See also

`dev-docs/docs/a-docs/04-key-business-flows.md` (collaboration section), `.cursor/rules/excalidraw-data-collab.mdc`, `excalidraw-app-integration.mdc`.
