# Add locale string (i18n)

Use for **user-visible** text in the **library** (`packages/excalidraw`), not hardcoded English.

## 1. Pick a key

- Prefer existing namespaces: **`labels.*`**, **`toolBar.*`**, **`hints.*`**, etc. — follow keys near similar UI in `packages/excalidraw/locales/` (e.g. `en.json`).

## 2. Add English source

- Edit the appropriate locale JSON under **`packages/excalidraw/locales/`** (start from **`en.json`** or the pattern used for your language).
- Keep placeholders consistent with existing messages (`{variable}` style if the codebase uses interpolation — match siblings).

## 3. Use in UI

- Import **`useI18n`** from `../i18n` (or the established relative path from the component).
- Call **`const { t } = useI18n()`** and use **`t("your.key")`** for labels, titles, buttons, tooltips.

## 4. Actions

- Action **`label`** (and **`keywords`**) should use **translation keys**, not raw English strings, when exposed in menus/command palette.

## 5. Crowdin

- Project translations are managed via **Crowdin** (per project docs); adding keys in English is the usual first step; translators pick up new keys there.

## Do not

- Hardcode user-facing strings in **`packages/excalidraw/components/`** for production UI.
- For **`excalidraw-app/`**-only UI, follow existing app patterns (some strings may be app-specific — mirror nearby files).

## See also

`dev-docs/docs/a-docs/08-adding-features.md`, `.cursor/rules/excalidraw-ui.mdc`.
