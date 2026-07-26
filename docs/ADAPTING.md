# Adapting the agent to your project

Everything you need to change lives between `# CONFIG` and `# END CONFIG` in [`copy-reviewer.md`](../copy-reviewer.md). The engine below that line has no project-specific content.

Budget an hour. Most of it goes into the house-voice list, which is the section that determines whether anyone trusts the output.

## Scope

Set the glob to the files you actually want reviewed.

Resist widening it. A glob calibrated for docs prose will misfire on your changelog, your marketing pages, and your API reference, because those surfaces have different conventions and the agent has no way to know that unless you tell it. Add surfaces deliberately, one at a time, and recalibrate when you do.

The agent reviews **changed lines only**. To review an existing file, tell it to treat the whole file as a synthetic diff.

## Surface gate

List the surfaces that exist in your project and mark which rule classes bind on each.

The example config has three: docs prose, markdown headings, and MDX JSX labels. Yours might have frontmatter descriptions, code comments in examples, or sidebar labels.

The table earns its place because surfaces genuinely disagree. In the example, docs headings use Title Case and UI microcopy uses sentence case. Both are correct. Without the gate, a reviewer that learned the sentence-case rule flags every heading in the corpus and produces a review nobody will read.

For each cell, ask whether the rule applies on that surface. Whether the rule is any good is a different question, and answering it here is how the gate gets filled in wrong.

## House voice

The most important section, and the one you cannot copy from anyone else.

Read your own docs. Look for patterns a naive reviewer would flag and a good editor would defend. Those are your house voice, and the confidence brake exists to protect them.

The example config lists six, and they cover the patterns most often misread as slop:

| Pattern | Why a naive reviewer flags it |
|---|---|
| Concrete binary contrast | Looks like "not X, it's Y" empty contrast |
| Label and definition dashes | Looks like AI dash abuse |
| Conversational second person | Looks informal next to institutional prose |
| Earned punchy closers | Look like dramatic fragmentation |
| Italicized UI quotations | Look like decorative emphasis |
| Title Case feature names | Look like sentence-case violations |

Write yours as specifically as you can, with a real example line from your corpus for each. "We use a conversational tone" gives the agent nothing to protect. "Direct second-person openers aimed at the reader, such as *You'll need an account before you can order*" gives it a pattern it can match.

If you skip this section, every finding the agent produces about your best writing will be a false positive.

## Punctuation calibration

Write down what your team actually does right now, and date it.

This section rots faster than any other. The example says label dashes stay and mid-sentence prose dashes are being reduced, which is a snapshot of one team's habits at one moment. Re-source it when the habits move.

The distinction worth keeping regardless of your house style: a real em dash used deliberately is a style choice, and a spaced hyphen used as a sentence connector is an AI tell. The agent treats them differently.

## Spelling and terminology

Only list terms where drift actually causes problems. Spelling variants your team argues about, a workflow vocabulary that has to stay consistent, product names with a canonical form.

Do not restate a general style guide here. The agent reads this section on every run, and padding it with rules nobody violates costs context and buys nothing.

## Microcopy rules

These apply only to UI strings, and only on the surfaces your gate marked Yes.

The shipped examples are conventional across most design systems: sentence case, "Create" for a CTA that opens a creation flow, "Save changes" on edit submits, consequence-phrasing on confirmations instead of "Are you sure?", no "successfully" in toasts. Keep the ones that match your product and delete the rest.

State each rule's scope inside the rule. The example's Create-versus-Add rule says it covers CTAs that open a creation dialog, which stops the agent from applying it to a plain navigation link that happens to start with "Add."

## Framework mechanics

Whatever breaks silently in your stack.

The example covers Docusaurus and MDX: admonition syntax, frontmatter, relative links, heading anchors. If you run VitePress, Nextra, or plain markdown, replace it.

One item worth checking in your own setup: whether your build actually fails on broken links or only warns. The example config notes that Docusaurus warns by default, which means a green build proves nothing about link validity and the agent has to check anchors itself. Most static site generators have an equivalent trap.

## First run

Point it at a real change and log the result in [`feedback-log.md`](../feedback-log.md).

Expect the first few runs to surface config problems rather than prose problems. A false positive on your best writing means the house-voice list is incomplete. A missed defect means a surface is gated wrong or a rule is missing. Both are cheap to fix early and expensive to fix after people have decided the reviewer is noisy.
