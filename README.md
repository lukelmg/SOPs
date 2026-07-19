# SOPs

standard operating procedures for long-horizon work (using opencode right now with Qwen3.6 27B on my local machine). this gives coding agents a working structure and a small memory system to understand a long term goal, what has been tried so far (with evidence), and be able to branch/promote/kill off/explore different ideas/hypothesis.

## Contents

i'll probably add some more for different types of tasks. this one is focused on trial/error experimentation/research right now

- [`research.md`](research.md) — Autonomous CS research loop: contract → experiment → measure → promote/branch. Keep a searchable record of what was tried, scored, and why.

## Research tree

```text
research/
  INDEX.md                 # catalog of lines and experiments
  STATUS.md                # mission, #1, gap, bottleneck, next
  LEADERBOARD.md           # ranked promote scores
  OPEN_QUESTIONS.md        # open research questions
  metrics_contract.md      # human-readable success contract
  PROJECT_BINDING.md       # project-specific path bindings
  decisions/D-*.md         # design decisions
  lines/L-<slug>/          # research line / bet
    README.md              # mechanism, kill/park, E-ids
    experiments/E-<id>-<slug>/
      hypothesis.md        # one falsifiable claim
      protocol.md          # how to run and measure
      results.md           # outcomes, scores, do-not-retry
      notes.md             # append-only change log
      workspace/           # trial code for this E
  graveyard/               # killed / parked lines
agent/
  AUTHORITY.md             # agent permissions and gates

artifacts/
  contract/success_contract.json   # metric, partitions, stats, promote rules
  best/pointer.json                # current incumbent
  runs/<run_id>/
    manifest.json                  # run identity and hashes
    scoreboard.json                # per-instance outcomes
    ranking_score.json             # comparable primary score
    measurement.json               # diagnostics only
```

