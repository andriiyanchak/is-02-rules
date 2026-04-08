# New editor action

Add a **user-triggered** state change via the action system (`ActionManager`).

## Steps

1. **Create** `packages/excalidraw/actions/actionYourFeature.ts` or `actionYourFeature.tsx`.
2. **Import** `register` from `./register`, `CaptureUpdateAction` from `@excalidraw/element`, and types with `import type` from `./types` and `../types` as needed.
3. **Implement** `export const actionYourFeature = register({ name, label, perform, captureUpdate, … })`:
   - `perform(elements, appState, formData, app)` → `{ elements?, appState?, files?, captureUpdate }` or `false` to no-op.
   - Choose **`captureUpdate`** to match the closest existing action (`IMMEDIATELY`, `EVENTUALLY`, `NEVER`, etc.).
4. **Export** from `packages/excalidraw/actions/index.ts` (required for registration).
5. **Shortcut** (optional): `keyTest` on the action and/or `packages/excalidraw/actions/shortcuts.ts`.
6. **UI** (optional): toolbar / menu / command palette — follow patterns in `Actions.tsx`, `MainMenu.tsx`, or command palette registrations.
7. **Label / i18n:** use a translation key for `label` (e.g. `"labels.myFeature"`) and add the string under `packages/excalidraw/locales/` (English + structure for Crowdin).
8. **Tests:** `packages/excalidraw/actions/actionYourFeature.test.tsx` with `render` / `unmountComponent` from `../tests/test-utils`, `API`, `Keyboard` where relevant.

## Conventions

- Do not import from `packages/excalidraw/index.tsx` inside the package; use concrete module paths.
- Prefer keeping pure state transforms in **`perform`**; use `App.tsx` only when pointer/tool lifecycle requires it.
- Mirror **`actionToggleStats.tsx`**, **`actionDeleteSelected`**, or a similar action for structure.

## Finish

Run `yarn test:typecheck`, `yarn test:update` if snapshots change, `yarn fix`.

## See also

`update-state-flow.md`, `dev-docs/docs/a-docs/08-adding-features.md`, `.cursor/rules/excalidraw-actions-state.mdc`.
