# New Vitest test (Excalidraw editor)

Add or extend tests using the project’s **Vitest + jsdom + Testing Library** setup.

## Setup (global)

- **`setupTests.ts`** (repo root) — canvas, IndexedDB, fonts, RAF mocks apply automatically.
- **`vitest.config.mts`** — path aliases **`@excalidraw/*`** must match imports in tests.

## Component / integration tests

1. Import **`render`**, **`unmountComponent`** from **`packages/excalidraw/tests/test-utils`** (adjust relative path from test file).
2. **`beforeEach`:** `await render(<Excalidraw />)` (or the minimal wrapper used in sibling tests).
3. **`afterEach`:** `unmountComponent()` when using that helper.
4. Use **`API`** (`../tests/helpers/api`), **`Keyboard`**, **`Mouse`** from **`../tests/helpers/ui`** when simulating editor actions — prefer existing helpers over brittle DOM queries.

## Pure logic tests

- **`packages/element/tests/`**, **`packages/math/tests/`**, **`packages/common/`** colocated `*.test.ts` — no React unless needed.

## Running

```bash
yarn test:app -- path/to/file.test.tsx
yarn test:app -- -t "partial test name"
```

## Snapshots

- If output is correct, run **`yarn test:update`** and **review diffs** before committing.

## Finish

`yarn test:typecheck`, `yarn fix`.

## See also

`dev-docs/docs/a-docs/06-testing-guide.md`, `.cursor/rules/excalidraw-testing.mdc`.
