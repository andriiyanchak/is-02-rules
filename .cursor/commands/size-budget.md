# Size budget & bundle impact

Use before adding **heavy dependencies** or **large imports** to **`packages/excalidraw`** or the app entry.

## Why

- The published editor is sensitive to bundle size; CI may enforce limits.
- **`@size-limit/preset-big-lib`** is a dev dependency of **`packages/excalidraw`**; workflow automation lives under **`.github/workflows/size-limit.yml`**.

## What to do

1. **Prefer** existing utilities in **`@excalidraw/common`**, tree-shakeable imports, and **dynamic import** for rare code paths when the codebase already does so for similar features.
2. **Avoid** importing whole libraries when only a small API is needed.
3. After significant dependency adds, run a **production build** locally and compare size if unsure:

   ```bash
   yarn build:packages
   yarn build
   ```

4. Open a PR early if the change is large — CI **size-limit** will report regressions.

## Library vs app

- **`packages/excalidraw`** — stricter; affects all embedders.
- **`excalidraw-app`** — still matters for first load; be deliberate with new chunks.

## See also

`packages/excalidraw/package.json` (size-limit scripts if present), `.github/workflows/size-limit.yml`.
