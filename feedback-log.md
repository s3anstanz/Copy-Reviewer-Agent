# copy-reviewer — calibration log

A reviewer nobody trusts gets ignored, and a reviewer that flags deliberate voice earns distrust fast. This log exists to measure that before the agent is turned loose on other people's work.

Keep it next to the agent. Update it every run.

## How to run

Invoke on a real change, or on a whole document treated as a synthetic diff:

> Use the copy-reviewer agent on `<file or diff>`.

Then log the run below. **Every run gets a row, including clean ones.** A run that finds nothing is the strongest evidence you have about false-positive rate, and skipping those rows quietly biases the log toward looking worse than the agent is.

## Verdict vocabulary

- **TP** — flagged, and it was a real defect worth a comment.
- **FP** — flagged, but it was deliberate voice, out of scope, or preference. *The metric that matters.*
- **FN** — a real defect it missed.
- **Near-miss** — right call, wrong category, severity, or confidence, or a weak `suggestion`.

## Calibration log

| Date | Target | Findings (cat/conf) | Verdict | Rule change made |
|---|---|---|---|---|
| | | | | |

## Rules under test

Deliberate judgment calls. Watch how they land and tighten only with evidence.

1. **Model tier.** Running on a top-tier model, because the voice-versus-slop distinction is the hardest judgment in the file and false-positive rate is the whole ballgame. Revisit once the rules stop moving. If a cheaper model matches across a batch of logged runs, downgrade and note which model in the log.
2. **Punctuation calibration.** The dash rules in your house style are a snapshot of current practice, not a law. If your team's habits move, this is the first section to re-source.
3. **Accuracy without stored facts.** The agent derives product truth from the corpus every run rather than carrying a fact list. Watch for it missing real contradictions (FN) or asserting stale claims. If it turns out to need a fact catalog, that is a finding worth acting on, but the catalog belongs in your docs, not in the agent.
4. **Confidence cap of 50 on a house-voice match alone.** This is the false-positive brake. If FPs still appear, tighten here before adding new rules.

## Fixer requirements

No `copy-fixer` exists. Every finding is applied by hand.

Each time you apply one, note what an automated fixer would have needed. This accumulates into a spec if you ever build one, and the honest answer may be that only some classes are safely automatable.

| Date | Finding class | What the fix actually required | Safely mechanical? | What a fixer would need to know |
|---|---|---|---|---|
| | | | | |

Early hypothesis to test rather than assume: `mechanics` (anchors, admonitions, terminology) looks mechanically safe. `accuracy` needs product truth a fixer cannot invent. `voice` and `slop` need authorial judgment, and they are the reason a fixer is risky at all.

## Graduation criteria

Promote the agent from personal use to team-shared only when:

- [ ] It has run against **real** changes, not only fixtures, across several PRs.
- [ ] **Zero** unexplained high-confidence FPs on deliberate house voice in the last several runs.
- [ ] It has caught at least one real defect a human reviewer would have wanted flagged.
- [ ] No rule change was needed for two consecutive runs. The rules have stopped moving.
- [ ] Scope is still what you decided it was. Expanding the glob at graduation is how a calibrated reviewer becomes an uncalibrated one.
