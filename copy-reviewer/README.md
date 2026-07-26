# copy-reviewer

A copy reviewer agent. It reviews documentation prose for AI slop, factual drift, and broken mechanics, without flattening the voice of the person who wrote it.

Steal it. The agent is one file of plain instructions, no frontmatter and no vendor bindings. Rewrite the CONFIG block at the top for your company and it works.

## The problem it solves

Most published slop-detection prompts are a list of banned words and rhetorical patterns. Point one at writing that has an actual voice and it flags the voice. Short punchy closers look like dramatic fragmentation. Concrete positioning claims look like empty binary contrasts. Definition dashes look like AI dash abuse.

So the writer gets a review full of suggestions that would sand their prose down to corporate mush, they reject all of them, and the reviewer never runs again.

This agent treats that as the thing to engineer against. Under-flagging is recoverable. Losing your team's trust is not.

## How it avoids that

**A confidence brake.** Matching a known house-voice pattern caps confidence at 50, below the posting gate of 75. To clear the gate, a finding needs an independent, quoted defect: a contradiction, an uncheckable claim, obscured meaning, or broken mechanics. "A period would be cleaner" can never clear it on its own.

**A surface gate.** Rules bind to surfaces. Sentence-case CTA rules apply to button labels, not to your Title Case docs headings. Applying UI microcopy rules to narrative prose is the single largest source of false positives, so the agent resolves the surface before it applies any rule.

**Detect, never rewrite.** Findings name the pattern, quote the line, state the defect, and give a short correction direction. The agent never returns rewritten prose and never scores whether something "sounds AI-generated." You keep authorship.

**No stored product facts.** Accuracy claims are verified against your repo every run. The agent carries no fact list that can go stale, and anything it cannot verify goes into `blindSpots` instead of becoming a confident wrong finding.

## The calibration log

[`feedback-log.md`](feedback-log.md) is the part worth copying even if you never use the agent.

An agent that reviews subjective work needs a way to prove it deserves trust. The log tracks every run with a verdict of TP, FP, FN, or near-miss, including runs that found nothing, since those are the strongest evidence about false-positive rate. It carries a register of rules deliberately left as judgment calls, and graduation criteria the agent has to clear before it gets shared with anyone else.

It also carries an open question: which finding classes could a `copy-fixer` safely automate? The early hypothesis is that mechanics is safe, accuracy needs product truth a fixer cannot invent, and voice and slop need authorial judgment. That table fills in from real fixes instead of guesses.

## Adapt it

1. Load [`copy-reviewer.md`](copy-reviewer.md) into your harness (see Running it below).
2. Rewrite everything between `# CONFIG` and `# END CONFIG`. The shipped example is Dunder Mifflin, a fictional paper company. Nothing in it applies to you.
3. Build the house-voice list by reading your own docs, not by guessing. Find the patterns a naive reviewer would flag and a good editor would defend. This list is what the confidence brake protects, and it is the difference between a reviewer people trust and one they mute.
4. Copy [`feedback-log.md`](feedback-log.md) next to the agent and log your first run.

[`ADAPTING.md`](ADAPTING.md) walks through each CONFIG section. [`WHY-IT-WORKS.md`](WHY-IT-WORKS.md) covers the false-positive reasoning in more depth.

## Status

Calibrating. This was built for a real documentation corpus and it has a handful of logged runs, not a track record. The shipped `feedback-log.md` is empty because the calibration data is specific to the corpus it came from and would tell you nothing about yours.

Treat it as a working starting point that still has to earn its place in your review process. The graduation criteria in the log are the bar I set for it, and it has not cleared them yet.

## Running it

The agent ships as a plain instruction document with no frontmatter, so it is not bound to any one tool.

| Harness | Where it goes | Frontmatter |
|---|---|---|
| Claude Code | `.claude/agents/copy-reviewer.md` | block below |
| Codex | `.codex/agents/copy-reviewer.md` | same block |
| Cursor, Continue, and similar | that tool's rules or custom-mode directory | that tool's format |
| Direct API, or a chat session | nowhere | none, pass the document as the system prompt |

Claude Code and Codex read the same three fields, so one block covers both. Paste it above the `# Copy reviewer` heading:

```yaml
---
name: copy-reviewer
description: Reviews changed documentation prose for accuracy, AI slop, voice preservation, microcopy, and mechanics. Read-only.
model: opus
---
```

`model` is optional and its accepted values are harness-specific. Omit it to inherit whatever the session is running.

## What it needs from the runtime

Three capabilities, none vendor-specific:

- **Repo read access.** Accuracy findings are verified against your corpus on every run, and cross-file drift needs a `file:line` citation. Without file access the agent still catches slop, microcopy, and mechanics, but accuracy findings degrade to guesses.
- **Structured output.** The finding contract is a JSON object.
- **A notion of changed lines.** The agent reviews diffs. To review an existing file, tell it to treat the whole file as a synthetic diff.

Pick a strong model. The voice-versus-slop judgment is the hardest thing in the file, and a weaker model false-positives on exactly the writing you most want protected.

MIT licensed.
