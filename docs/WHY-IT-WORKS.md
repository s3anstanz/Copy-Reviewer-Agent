# Why it works

Notes on the design reasoning. Useful if you want to modify the engine, or if you are building a different reviewer for subjective work and want the parts that generalize.

## Prose review has an asymmetric error budget

A false positive in code review costs a few seconds. You read the comment, decide it is wrong, resolve the thread, move on. The reviewer stays useful.

A false positive in prose review lands differently. Telling a writer their sharpest line is a defect is a claim about their judgment, and it is wrong in a way they can feel. Two or three of those and the reviewer is noise. People stop reading the output, then stop running it.

That asymmetry drives every other decision here. The agent is tuned to miss defects rather than risk flagging good writing, because a missed defect costs one unedited line and a false positive costs the entire tool.

## Slop and good writing share shapes

This is the technical problem underneath.

Every published slop heuristic is a shape. Short declarative fragments in sequence. A noun phrase followed by a colon and a reveal. Two clauses in an X-versus-Y contrast. Em dashes mid-sentence.

Good writing produces all of those shapes:

| Shape | Slop | Not slop |
|---|---|---|
| Fragment sequence | "That's it. That's the whole thing." | "Order by 4pm. It ships that day." |
| Binary contrast | "It's not a tool, it's a philosophy." | "Big-box suppliers give you a phone tree. We give you a rep who picks up." |
| Definition dash | decorative rhythm dash | `**Ream** — 500 sheets.` |

A shape-matching detector cannot tell these apart, because the difference is not in the shape. It is in whether the line carries checkable information. The first column says nothing. The second column tells you when your paper ships, what the competitor does, and how many sheets are in a ream.

So the agent is built to look for emptiness rather than for shape. Shape only nominates a candidate. The finding requires quoting what is actually empty, contradictory, or broken.

## The confidence brake

The mechanism that makes the above operational.

Findings post at confidence 75 or above. Matching a documented house-voice pattern caps confidence at 50. To get from 50 to 75, a finding needs an independent, quoted defect that is not the pattern match: a contradiction, an uncheckable claim, obscured meaning, or broken mechanics.

This inverts the usual default. Most detectors flag unless something suppresses the flag. This one declines to flag unless something proves the flag. Three phrasings are explicitly named as never sufficient on their own, because they are the three that kept sneaking through: "a period is cleaner", "this feels promotional", "there are a lot of dashes."

The brake only works if the house-voice list is real. An empty list means nothing gets capped and the agent behaves like every other slop detector. That is why [`ADAPTING.md`](ADAPTING.md) spends the most space there.

## Rules have surfaces

A rule applied outside its scope is a category error, and it is the most common failure in copy tooling.

Sentence-case CTA rules are correct for button labels and wrong for Title Case documentation headings. Toast-message punctuation rules mean nothing in narrative prose. A reviewer that learned one project-wide style guide and applies it uniformly will flag half the corpus.

So the agent resolves the surface before it applies any rule. The gate is a table because the answer is genuinely two-dimensional: rule class against surface, with a different verdict in most cells.

## Detect, never rewrite

Three reasons, in order of weight.

Rewriting imposes the reviewer's voice. Even a good rewrite replaces the writer's phrasing with the model's, and applied across a corpus that converges everything toward the same register. The house voice dies by a thousand improvements.

A rewrite also hides the reasoning. "Here is a better sentence" gives the writer nothing to evaluate. Naming the pattern and quoting the defect lets them disagree, and disagreement is how the config gets fixed.

Third, rewriting makes the false-positive cost worse. A wrong finding is annoying. A wrong finding with a confident rewrite attached is insulting.

## No stored product facts

The agent verifies accuracy claims against the repo on every run and carries no fact list.

A fact list embedded in an agent goes stale the first time the product changes, and a stale fact list produces confident wrong findings, which are worse than no findings. Anything the agent cannot verify from the corpus goes into `blindSpots` rather than becoming a guess.

The tradeoff is real: this costs a corpus read every run, and it means the agent misses contradictions that would need knowledge outside the repo. That is the right trade, but it is a trade.

## Trust is measured, not asserted

Any agent doing subjective work can claim a low false-positive rate. The claim is worth nothing without data.

[`feedback-log.md`](../feedback-log.md) is how the claim gets tested. Every run gets a row with a verdict of TP, FP, FN, or near-miss. Clean runs get rows too, which matters more than it sounds: a log containing only runs that found something will overstate the false-positive rate badly, because the denominator is missing.

The log also holds a register of rules deliberately left as judgment calls, so that when a false positive appears there is a documented list of the most likely culprits before anyone starts adding new rules. Adding rules in response to a false positive is usually the wrong move. Tightening the brake is usually the right one.

## What it cannot do

It cannot verify claims that depend on knowledge outside the repo. Those land in `blindSpots` and need a human.

It cannot tell you whether your house voice is any good. It protects the voice you documented. If you document a bad one, it defends that.

It is one model with one perspective, and it agrees with itself. It is a first pass that catches mechanical defects and obvious slop, not a substitute for an editor.

## What generalizes

If you are building a reviewer for other subjective work, four pieces port directly: the asymmetric error budget as an explicit design constraint, a confidence brake that requires independent evidence beyond a pattern match, surface-scoped rules, and a calibration log that records clean runs.

The slop patterns and the house-voice list do not port. Those are yours to write.
