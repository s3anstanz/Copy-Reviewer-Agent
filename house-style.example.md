# House style

The copy reviewer reads this file before every review. It holds everything specific to one project; the agent itself holds none of it.

**This is an example.** It describes Dunder Mifflin, a fictional paper company with a customer-facing docs site for its ordering platform. It exists to show the shape of a filled-in house style. Replace all of it with yours.

`docs/ADAPTING.md` walks through each section below and explains what it is for.

---

## Scope

Review `docs/**/*.{md,mdx}`.

Review **changed lines only**. Pre-existing copy is out of scope. To review a whole file, treat it as a synthetic diff where every line counts as changed.

## Surface gate

Apply this before any rule. Misapplying UI-microcopy rules to docs prose is the largest source of false positives.

| Class | Docs prose | Markdown headings | MDX JSX labels / CTAs |
|---|---|---|---|
| 1 Accuracy | Yes | When semantically relevant | Yes |
| 2 Slop | **Primary** | Only if genuinely empty | Longer explanatory copy only |
| 3 Voice | Yes | Yes | Yes |
| 4 Microcopy | **No** | **No** | Yes |
| 5 Mechanics | **Primary** | Yes | Yes (MDX validity) |

Project notes:

- Docs headings use **Title Case** by convention. They are never a sentence-case violation. UI microcopy uses sentence case. The two conventions differ on purpose.
- Body prose is not a toast, CTA, confirmation, placeholder, or form label. Microcopy phrasing rules do not reach it.
- MDX JSX labels and CTA children **are** UI copy, even inside an `.mdx` file.

## House voice — protect these

Deliberate patterns. Identify your own from your live corpus rather than copying this list.

1. **Concrete binary contrasts** carrying a checkable claim. "Big-box suppliers give you a pallet and a phone tree. Dunder Mifflin gives you a rep who picks up." Flag only vague A/B rhythm that carries no information.
2. **Label and definition dashes**, plus `=` glossary notation. `**Case** — 10 reams, 500 sheets each.`
3. **Conversational second person**, including a direct opener aimed at the reader. "You'll need an account before you can order."
4. **Earned punchy closers** stating a responsibility or consequence. "Order by 4pm. It ships that day." Flag only a cute metaphor carrying no information.
5. **Italicized UI quotations and mode names.** Click *Reorder*. Switch to *Standing Order*.
6. **Title Case feature names** in docs prose: Standing Order, Rush Delivery, Volume Pricing.

## Punctuation calibration

Current practice, last reviewed 2026-07-26:

- The **bold-label definitional** dash is standard and stays: `**Ream** — 500 sheets.`
- **Mid-sentence prose-rhythm** dashes are being reduced. Several docs now carry none.
- The AI **spaced hyphen** (` - ` as a sentence connector) is a defect, distinct from a real `—`.

## Spelling and terminology

- `canceled`, `canceling` — single l.
- `catalog`, not `catalogue`.
- An order is **placed**, then **fulfilled**, then **delivered**. Never "processed."

## Microcopy rules

Apply only to MDX JSX component labels, children, and CTA props. Never to headings, narrative prose, frontmatter titles, glossary labels, or feature proper nouns.

- **Sentence case.** Only the first word and proper nouns are capitalized. Exceptions: product names, acronyms (`SKU`, `PO`, `ZIP`).
- **Create vs Add.** "Create" for a CTA opening a creation dialog or form. "Add" only for inline list actions, such as adding a row to a dynamic field array. This rule covers CTAs that open a creation flow, so it does not reach a plain navigation link.
- **Edit submit button** reads "Save changes."
- **Destructive button** reads "Remove." Reserve "Delete" for confirmation message phrasing.
- **Confirmation button** names the action, never the literal word "Confirm": `confirmText="Remove location"`.
- **Toasts** use `{Entity} {past-tense verb}` with no "successfully" and no trailing period: "Order placed."
- **Confirmation messages** describe the consequence, not the question. No "Are you sure?" Use "This standing order will be permanently deleted."
- **Search placeholders** use `Search by {field}` with no trailing ellipsis.

## Framework mechanics

The docs site runs Docusaurus with MDX.

- Valid frontmatter; balanced MDX JSX.
- Valid, balanced admonitions: `:::info|tip|note|warning|danger|caution` … `:::`.
- Relative link targets that exist; valid explicit (`{#anchor}`) or generated anchors; no stale anchors after a heading change.
- The Docusaurus config uses `warn`, not `throw`, on broken links. A passing build does **not** prove links are valid. Validate anchors and internal links yourself.
