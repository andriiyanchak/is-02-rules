# A/B rule validation: `excalidraw-element-model.mdc`

This document records a **Rule Validation: A/B method** check for the Cursor rule  
`.cursor/rules/excalidraw-element-model.mdc`.

**Method (summary):**

1. **A — Rule ON:** Same prompt with the rule active (`.mdc` present, globs match).
2. **B — Rule OFF:** Rename rule to `.mdc.off` (or otherwise disable), **same prompt**.
3. **Compare** outputs → conclude **rule works** vs **rewrite rule**.

---

## Rule under test

| Field | Value |
|-------|--------|
| File | `.cursor/rules/excalidraw-element-model.mdc` |
| Globs | `packages/element/**/*.ts`, `packages/element/**/*.tsx` |
| `alwaysApply` | `false` |

**Intent of the rule:** Immutable elements, use `mutateElement`, Scene for z-order, schema → `types` / `newElement` / `restore`, collaboration versioning.

---

## Documented test scenario

| Item | Value |
|------|--------|
| **Context** | User attached `@packages/element/src/mutateElement.ts` and had element-package context (glob match). |
| **Prompt** | “How should I move a rectangle in Excalidraw’s element package?” |

---

## Result A (rule ON)

- **Debug line:** Reply began with  
  `RULE excalidraw-element-model APPLIED 🧱`  
  (per **Important (debug)** section in the rule).
- **Framing:** Opened with not relying on raw `element.x = …` alone; tied behavior to **collaboration, history, and version / versionNonce / updated** early.
- **Structure:** Numbered sections — (1) core `mutateElement`, (2) `Scene.mutateElement` for React updates, (3) `newElementWith`.
- **Technical content:** `mutateElement(element, elementsMap, { x, y })`, cited warning about re-renders, cited `Scene.mutateElement` + `triggerUpdate`, cited `newElementWith`, code references to `packages/element/src/mutateElement.ts` and `Scene.ts`.

---

## Result B (rule OFF)

- **Debug line:** **Absent**; reply noted that **`excalidraw-element-model`** appeared disabled (e.g. `excalidraw-element-model.off`) and that the rule’s debug line was therefore skipped.
- **Framing:** Went straight into **how** to move (x/y, `mutateElement`), then re-render caveat, then immutable alternative.
- **Structure:** Headings “Move a rectangle”, “Re-renders in the editor”, “Immutable alternative”, closing “Avoid …”.
- **Technical content:** **Same** APIs, same files, same citations — `mutateElement`, `elementsMap`, `Scene.mutateElement`, `newElementWith`, avoid `rectangle.x = …` without versioning.

---

## Comparison

| Dimension | A (rule ON) | B (rule OFF) |
|-----------|-------------|--------------|
| **RULE … APPLIED line** | Yes | No |
| **Upfront immutability / collab story** | Stronger, earlier | Present but later / lighter |
| **Correct move API (`mutateElement`, `Scene`)** | Yes | Yes |
| **Substantive technical difference** | **Small** for this prompt | — |

---

## Conclusion

- **The rule clearly “fires” from a validation perspective** when the **debug line** appears in A and not in B — that is a reliable **A/B signal**.
- For **this specific prompt**, the **substantive** answers were **largely the same**, because **`mutateElement.ts`** (attached / in context) already documents the correct pattern, version bumps, and the “use `scene.mutateElement` for component updates” warning. The model does not need the `.mdc` text to give a correct low-level answer in that situation.
- **Recommendation for future A/B tests of this rule:** use a prompt **not fully answered by a single open file**, for example:
  - *“I’m adding a new optional field on frame elements; list every file to touch and in what order.”*  
  Here the rule’s **`restore.ts`**, **`types.ts`**, **`newElement.ts`**, rendering/export, and collab/version checklist should create a **larger** gap between rule ON vs OFF.

**Verdict:** **Rule works** for **enforcement visibility** (debug banner + framing). For **content-only** checks, pair with prompts where the rule adds information **beyond** the currently focused source file.

---

## Related

- Other project rules: `.cursor/rules/*.mdc`
- Onboarding / element conventions: `dev-docs/docs/a-docs/` (e.g. `08-adding-features.md`, `07-common-pitfalls.md`)
- Agent overview: `AGENT.md` (repo root)
