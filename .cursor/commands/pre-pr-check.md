# Pre-PR check

Run this before opening a PR or pushing a feature branch.

## Commands (repo root)

```bash
yarn test:update
yarn test:typecheck
yarn fix
```

1. **`yarn test:update`** — runs Vitest and updates snapshots where intended. **Review every snapshot diff**; do not commit accidental UI regressions.
2. **`yarn test:typecheck`** — full TypeScript check for the monorepo configuration.
3. **`yarn fix`** — Prettier + ESLint auto-fix (`fix:other` + `fix:code`).

## CI parity (optional but thorough)

```bash
yarn test:all
```

Equivalent to typecheck + eslint + prettier check + tests (non-watch).

## Manual sanity

- [ ] New user-facing strings go through **`locales/`** (library UI).
- [ ] Element changes: **`mutateElement`** + version / collab implications considered.
- [ ] New deps: bundle / **size-limit** CI (`.github/workflows/size-limit.yml`) if the change affects published packages.

## If something fails

Fix in order: **types** → **lint** → **tests** → **snapshots** (only if output is correct).
