# Copy reviewer

An agent that reviews documentation prose for AI slop, factual drift, and broken mechanics, without flattening the voice of the person who wrote it.

Steal it. The agent is one file of plain instructions, no frontmatter and no vendor bindings. Rewrite the CONFIG block at the top for your company and it works.

## Install

The agent ships without frontmatter so it is not bound to any one tool. Add whatever your harness expects.

### Claude Code or Codex

Both read the same three frontmatter fields, so one command covers either. Run it from your repo root:

```bash
AGENT_DIR=.claude/agents          # Codex: use .codex/agents

mkdir -p "$AGENT_DIR"
{
  printf -- '---\nname: copy-reviewer\ndescription: Reviews changed documentation prose for accuracy, AI slop, voice preservation, microcopy, and mechanics. Read-only.\nmodel: opus\n---\n\n'
  curl -sL https://raw.githubusercontent.com/s3anstanz/copy-reviewer-agent/main/copy-reviewer.md
} > "$AGENT_DIR/copy-reviewer.md"
```

`model` is optional and its accepted values differ by harness. Drop the line to inherit whatever the session is running.

### Anything else

Grab the file and pass it as a system prompt, or paste it into your tool's rules or custom-mode directory:

```bash
curl -sLO https://raw.githubusercontent.com/s3anstanz/copy-reviewer-agent/main/copy-reviewer.md
```

### The calibration log

Optional, and the piece most worth taking. Put it somewhere your team will actually see it:

```bash
curl -sL https://raw.githubusercontent.com/s3anstanz/copy-reviewer-agent/main/feedback-log.md \
  -o docs/copy-reviewer-log.md
```

### Or take the whole repo

```bash
git clone https://github.com/s3anstanz/copy-reviewer-agent.git
```

## Configure

The agent will not work well until you do this. It ships calibrated to Dunder Mifflin, a fictional paper company, and none of that applies to you.

Open the file and replace everything between `# CONFIG` and `# END CONFIG`:

```bash
$EDITOR .claude/agents/copy-reviewer.md
```

Four things to set, in order of how much they matter:

1. **House voice.** The patterns a naive reviewer would flag and a good editor would defend. Read your own docs to find them. This list is what the confidence brake protects, and it separates a reviewer people trust from one they mute.
2. **Scope.** The glob of files to review.
3. **Surface gate.** Which rule classes bind on which surface. Docs headings and button labels usually follow different case conventions, and this table is what stops the agent applying one to the other.
4. **Microcopy, spelling, punctuation, framework mechanics.** Your conventions, your stack.

[`ADAPTING.md`](ADAPTING.md) walks through every section with the reasoning behind it. Budget an hour, most of it on the house-voice list.

## Run it

Point it at a change:

```
Use the copy-reviewer agent on the changes in docs/.
```

The agent reviews changed lines only. To review a file that is already committed, tell it to treat the whole file as a synthetic diff:

```
Use the copy-reviewer agent on docs/ordering/standing-orders.mdx.
Treat the whole file as a synthetic diff.
```

It returns JSON. Findings below confidence 75 are suppressed, and every run reports what it could not verify in `blindSpots`:

```json
{
  "issues": [
    {
      "file": "docs/ordering/standing-orders.mdx",
      "line": 42,
      "category": "accuracy",
      "confidence": 90,
      "violation": "Orders placed after 4pm ship the same day",
      "rule": "Universal claim contradicting a path it covers",
      "precedent": "docs/shipping/cutoffs.mdx:18",
      "suggestion": "Scope to before-cutoff orders",
      "severity": "high"
    }
  ],
  "blindSpots": ["Could not verify the 4pm cutoff against a source of truth"]
}
```

Log the result in your copy of [`feedback-log.md`](feedback-log.md). Expect the first few runs to surface config problems rather than prose problems.

## Why it works this way

Most published slop-detection prompts are a list of banned words and rhetorical patterns. Point one at writing that has an actual voice and it flags the voice. Short punchy closers look like dramatic fragmentation. Concrete positioning claims look like empty binary contrasts. Definition dashes look like AI dash abuse.

So the writer gets a review full of suggestions that would sand their prose down to corporate mush, they reject all of them, and the reviewer never runs again.

This agent treats that as the thing to engineer against. Under-flagging is recoverable. Losing your team's trust is not.

**A confidence brake.** Matching a known house-voice pattern caps confidence at 50, below the posting gate of 75. To clear the gate, a finding needs an independent, quoted defect: a contradiction, an uncheckable claim, obscured meaning, or broken mechanics. "A period would be cleaner" can never clear it on its own.

**A surface gate.** Rules bind to surfaces. Sentence-case CTA rules apply to button labels, not to your Title Case docs headings. Applying UI microcopy rules to narrative prose is the single largest source of false positives, so the agent resolves the surface before it applies any rule.

**Detect, never rewrite.** Findings name the pattern, quote the line, state the defect, and give a short correction direction. The agent never returns rewritten prose and never scores whether something "sounds AI-generated." You keep authorship.

**No stored product facts.** Accuracy claims are verified against your repo every run. The agent carries no fact list that can go stale, and anything it cannot verify goes into `blindSpots` instead of becoming a confident wrong finding.

[`WHY-IT-WORKS.md`](WHY-IT-WORKS.md) covers the reasoning in more depth, including which parts port to reviewers for other subjective work.

## The calibration log

[`feedback-log.md`](feedback-log.md) is worth copying even if you never use the agent.

An agent that reviews subjective work needs a way to prove it deserves trust. The log tracks every run with a verdict of TP, FP, FN, or near-miss, including runs that found nothing, since those are the strongest evidence about false-positive rate. It carries a register of rules deliberately left as judgment calls, and graduation criteria the agent has to clear before it gets shared with anyone else.

It also carries an open question: which finding classes could a `copy-fixer` safely automate? The early hypothesis is that mechanics is safe, accuracy needs product truth a fixer cannot invent, and voice and slop need authorial judgment. That table fills in from real fixes instead of guesses.

## What it needs from the runtime

Three capabilities, none vendor-specific:

- **Repo read access.** Accuracy findings are verified against your corpus on every run, and cross-file drift needs a `file:line` citation. Without file access the agent still catches slop, microcopy, and mechanics, but accuracy findings degrade to guesses.
- **Structured output.** The finding contract is a JSON object.
- **A notion of changed lines.** The agent reviews diffs.

Pick a strong model. The voice-versus-slop judgment is the hardest thing in the file, and a weaker model false-positives on exactly the writing you most want protected.

## Status

Calibrating. This was built for a real documentation corpus and it has a handful of logged runs, not a track record. The shipped `feedback-log.md` is empty because the calibration data is specific to the corpus it came from and would tell you nothing about yours.

Treat it as a working starting point that still has to earn its place in your review process. The graduation criteria in the log are the bar I set for it, and it has not cleared them yet.

## License

MIT.
