# SOP: Autonomous CS Researcher

**Audience:** a human or AI agent doing long-horizon computer science research  
**Scope:** algorithms, systems, compilers, databases, security, networking, HCI, ML/AI, formal methods, etc.  
**Objective:** finish the mission by moving a **recomputation-stable primary metric** on a held-out **promote** evaluation—while leaving a searchable, branchable record of *why* every move was made.

**Court of law:** machine-readable contract + immutable run artifacts + atomic best pointer.  
**Markdown:** generated views + interpretation. Not a second authority.  
**Opinions:** not evidence.

Domain examples below (latency, throughput, accuracy, proof size, …) are illustrations. The **contract** names the real metric for each project.

---

## 1. Real objective

Keep the research loop alive across sessions:

1. Know the mission metric, eval partition hashes, and current leaderboard #1.  
2. Know what was tried, what it scored, and which bottleneck it targeted.  
3. Propose the next smallest experiment that could **move a number** or falsify a bottleneck belief.  
4. Run it on the agreed harness; record results; update the leaderboard.  
5. Promote, retain incumbent, or branch—never leave an undocumented gap.

Cold-start test: from `research/` + artifacts alone, state *what is #1 and why*, *what failed with what scores*, and *what to try next*.

---

## 2. First principles

### 2.1 Measurement is the product

| Principle | Rule |
|-----------|------|
| **No vibes** | “Feels faster/cleaner/smarter” ≠ result. |
| **Partitioned yardsticks** | `dev` = iterate, ablate, mine failures, tune. `promote` = only path that updates `artifacts/best`. `sealed` = rare certification / paper claim; **no** instance-level feedback into the search loop. |
| **Recomputable** | Same candidate + same partition hash + same resource envelope → same scorecard (within contract tolerance). Else fix the harness first. |
| **One primary metric** | Exactly one canonical ranking number (`primary_metric.name`). Hard gates veto eligibility; they do not crown alternate winners. |
| **Resource envelope** | Identical hard caps for candidate and incumbent (§6.4). Exceed any cap → `ineligible`. Underspend allowed. |
| **Statistical gate** | `promote.decide` follows fully specified `stats_policy` (§6.3)—not a lone mean. |
| **Incumbent for promote** | Hypothesis may use an experimental control; **promotion** always compares to the incumbent at eval start (§7.5). |
| **Gate > narrative** | The decide function wins; docs explain it. |

**Kinds of work product:**
- `ranking_run` → `ranking_score.json` (comparable on promote/sealed)  
- `measurement_study` → `measurement.json` (diagnostics only; no fake primary score)  
- `spike` / `decision` → labeled; not ranked  

### 2.2 Work classes

| Class | Allowed if |
|-------|------------|
| **Research** | Hypothesis + measurable outcome (rank or diagnostic). |
| **Infrastructure** | Explicit acceptance criterion: correctness, reliability, security, cost, or iteration speed. |
| **Unjustified** | Neither. Do not do. |

Unmeasured refactors, multi-knob muddles, and “for later” abstractions without criteria are unjustified.

### 2.3 Bottlenecks (domain-agnostic)

Name the **primary_bottleneck_target** before designing. List `anticipated_secondary_effects[]`; after the run, `observed_effects[]`.

| Class | Question | Example signals (CS) |
|-------|----------|----------------------|
| **Correctness / failure mass** | What error class burns the most metric? | Fail taxonomy, assert rates, counterexamples |
| **Harness / measurement** | Is the score lying or flaky? | Two-run mismatch, nondeterminism |
| **Algorithmic complexity** | Asymptotic or constant-factor bound? | Big-O gap, profile hot spots |
| **Systems resources** | CPU, memory, I/O, lock, network? | Profiles, queueing, cache misses |
| **Data / workload** | Wrong inputs or distribution shift? | Slice tables, coverage gaps |
| **Interface / API** | Bad contracts between components? | Misuse rates, schema errors |
| **Learning / fit** *(if applicable)* | Model/data ceiling? | Oracle gap, train/eval contamination |
| **Iteration speed** | Learning limited by wall clock? | Sec/eval, CI time, cluster queue |
| **Promote safety** | False promotes / overfitting promote set? | Access budget, sealed drift |

If bottleneck evidence is missing, next work is a **measurement_study**, not a ranking idea.

**Next-move table** (record when choosing):

| Idea | Est. lift | Confidence 0–1 | Eval cost | Info value | Risk | Bottleneck |
|------|----------:|---------------:|----------:|-----------:|-----:|------------|
| … | … | … | … | … | … | … |

### 2.4 Verification ladder

```text
L0  Unit tests, proofs of harness bits, schema/hash checks
L1  DEV screens / ablations / microbenchmarks     → never updates best
L2  PROMOTE decide() vs incumbent                 → only path to best
L3  SEALED certificate / paper/public claim       → no instance-level fishing
```

Never crown a winner from L1. Shrink candidate count if L2 is expensive—not the definition of winning.  
If the artifact under study can modify its own evaluator/harness, keep those paths outside the self-mod allowlist.

### 2.5 Early misses ≠ dead branch — explore with glimmers, kill zombies

An L2 “not promoted” answers: “did this beat best under the contract?”—not “is the line worthless?”

**Glimmers** = numeric intermediates that the mechanism is real even if the primary metric has not moved (e.g. smaller counterexample class, better profile hotspot, higher hit rate, tighter proof bound, lower p99 on a DEV slice).

Rules:
1. **Not promoted ≠ kill.**  
2. **Park** with reopen condition when glimmer holds but promote isn’t ready.  
3. **Exploration quota** (~1 in 4 experiments or 1 per stall cycle): hard-bottleneck / out-of-box bets—not only easy eps chases.  
4. **Bet card** on every speculative line: mechanism, glimmer metrics, kill/park, `budget_cap`. Kill when N flat experiments + no promote, cap exhausted, or falsifier fires.  
5. No bet card → no long exploration; only ordinary L1→L2 work.

Heuristic: `(mission_gap × bottleneck_share × glimmer_strength) / eval_cost`.

---

## 3. Role and non-goals

**You are:** a lab PI with a stopwatch and a held-out exam. Find bottlenecks, run measurable experiments, write score rows, pick the next move.

**You are not:** a vibes engine, a chat log, or a refactor tourist.

**Deliberately excluded:** novel-length notebooks; wikis disconnected from scores; secondary metrics as alternate winners; undocumented side quests.

---

## 4. Research tree

```text
research/
  INDEX.md                 # generated
  STATUS.md                # generated (+ optional overlays for next/blocked)
  LEADERBOARD.md           # generated (never hand-edited as authority)
  OPEN_QUESTIONS.md        # human-maintained
  metrics_contract.md      # generated from success_contract.json
  decisions/D-*.md
  lines/L-<slug>/
    README.md              # bet card + known E-ids
    experiments/E-<id>-<slug>/
      hypothesis.md
      protocol.md
      results.md
      notes.md             # optional
  graveyard/
agent/                     # if autonomous execution is used
  AUTHORITY.md
```

Machine authority:

```text
artifacts/contract/success_contract.json
artifacts/best/pointer.json
artifacts/runs/<run_id>/manifest.json
artifacts/runs/<run_id>/scoreboard.json      # when instance-level outcomes exist
artifacts/runs/<run_id>/ranking_score.json   # L2/L3 ranking
artifacts/runs/<run_id>/measurement.json     # assays / screens
```

---

## 5. Git practice (standard engineering)

Git = code lineage. `research/` + run artifacts = scientific lineage.

| Work | Branch |
|------|--------|
| Integration | `main` — green enough to run promote eval |
| Ranking experiment | `exp/E-<id>-<slug>` |
| Multi-E line | optional `line/L-<slug>`; still one `exp/...` per ranking code change |
| Infrastructure | `infra/<slug>` with acceptance criterion |
| Hotfix | `fix/<slug>` |

- Commit early/often; why-focused messages; include `E-id` when applicable.  
- No secrets. **No huge binaries** in git—put checkpoints, traces, datasets, profiles under `artifacts/` (gitignore or LFS) and record hashes in the manifest.  
- Before L2: prefer clean tree; record `HEAD` (+ dirty hash if dirty).  
- **Promoted** → merge to `main`. **Not promoted** → do not merge as best; keep branch/tag.  
- Bad merge → `git revert`. Never force-push `main`.  
- Failures stay in history and in `research/`.

---

## 6. Contract, stats, resources, promote access

### 6.1 Source of truth

| Role | Path |
|------|------|
| Authority | `artifacts/contract/success_contract.json` |
| Incumbent | `artifacts/best/pointer.json` |
| Runs | `artifacts/runs/<run_id>/` (immutable) |
| Generated | `metrics_contract.md`, `LEADERBOARD.md`, STATUS metric fields, `INDEX.md` |

### 6.2 Required contract fields

1. `schema_version`, `contract_version`  
2. Singular `primary_metric`:

```yaml
primary_metric:
  name: p99_latency_ms          # or throughput_qps, macro_f1, proof_size, ...
  estimator: mean_over_workload_items   # define precisely
  higher_is_better: false
```

3. `partitions{dev,promote,sealed}` + content hashes + sizes  
4. `baselines[]`  
5. `eps` / margin + exact comparator (§7.5)  
6. `stats_policy` (§6.3)  
7. `resource_envelope` (§6.4)  
8. `hard_gates[]` (correctness, safety, memory ceiling, license, …)  
9. `promote_access` (§6.5)  
10. `mission_target` (usually on sealed)  
11. `repro_tolerance`  
12. `contamination_policy` when fit/train/search can leak promote/sealed instances  
13. `public_claim_requires_audit` for external claims  

### 6.3 `stats_policy`

Name one decide function. Required keys: `method`, `confidence`, `sidedness`, `pairing_unit` (if paired), bootstrap/`seeds_min` as applicable, `seed_aggregation`, exact `decide_rule`, `missing_outcomes`, and multiple-comparison stance (default: **finite promote_access**, not full FDR on day one).

Deterministic benchmarks may use exact paired deltas + eps; stochastic systems need seeds + interval/LCB as specified.

### 6.4 Resource envelope (default)

```text
Identical hard caps for candidate and incumbent
  (wall_sec, cpu_sec, peak_rss, disk, network_bytes, dollars, domain-specific caps…).
Exceed any cap → ineligible.
Underspend allowed.
Retries, extra search width, and parallel attempts count against caps.
Harness overhead excluded unless contract says otherwise.
```

### 6.5 Promote-access policy

Adaptive reuse of `promote` overfits it. Treat access as a finite resource:

```yaml
promote_access:
  max_decisions_per_contract_era: 30   # project-set example
  instance_level_feedback: false       # aggregates + gates only, by default
  aggregate_feedback_allowed: [score, paired_delta, lcb, hard_gate_results, coarse_fail_mass]
  refresh_trigger: [access_budget_exhausted, sealed_vs_promote_gap_exceeds_threshold]
  refresh_requires_decision: true
```

---

## 7. Leaderboard, outcomes, atomic promote

### 7.1 Authority

L2 `ranking_score.json` + `pointer.json`. `LEADERBOARD.md` is generated.

### 7.2 Separated outcomes

| Field | Enum |
|-------|------|
| `run_status` | `valid` \| `invalid` \| `aborted` |
| `hypothesis_outcome` | `supported` \| `unsupported` \| `inconclusive` \| `not_applicable` |
| `promotion_outcome` | `promoted` \| `retained_incumbent` \| `ineligible` \| `not_evaluated` |

Hypothesis ↔ experimental control. Promotion ↔ incumbent. Never overload one “verdict.”

### 7.3 Leaderboard example (normative shape)

After E-0142 promotes over incumbent E-0138:

```markdown
| rank | id | score | Δ/LCB vs incumbent | hypothesis | promotion | bottleneck |
|------|----|------:|-------------------:|------------|-----------|------------|
| 1 | E-0142 | 12.4 | better | supported | promoted | lock_contention |
| 2 | E-0138 | 15.1 | — | supported | retained_incumbent (displaced) | — |
| — | E-0140 | 16.0 | worse | unsupported | retained_incumbent | allocator |
| — | E-0131 | 11.0 | better | supported | ineligible (mem gate) | … |
```

Higher or lower “better” follows `primary_metric.higher_is_better`. The **promoted** candidate is rank 1.

### 7.4 Ranking score (sketch)

Include: ids, contract/partition hashes, `primary_metric`, numeric score (+ counts if discrete), `experimental_control_id` + `delta_to_control`, `incumbent_*` + `delta_to_incumbent`, stats fields, resource use, hard gates, three outcome fields, manifest/scoreboard paths.

`measurement.json`: diagnostics only; `promotion_outcome: not_evaluated`.

### 7.5 Decide + CAS

Eps form (when stats_policy uses it):

```text
improvement iff delta_to_incumbent is better by >= eps
tie iff within eps → retained_incumbent
else retained_incumbent
```

(Define “better” via `higher_is_better`. Boundary `== eps` counts as improvement.)

```text
1. Contract + promote_access OK
2. Capture incumbent_digest
3. Validate run + recompute
4. Hard gates else ineligible
5. stats_policy vs incumbent
6. Lock; if digest changed → stale/re-decide
7. Else CAS pointer.json → promoted
8. hypothesis_outcome vs control (independent)
```

### 7.6 Run manifest

**Required:** run/experiment/contract ids; git commit + dirty state; resolved config hash; dependency lock hash; suite/harness hashes; seeds/order if any; resource envelope hash; hardware/runtime summary; raw result hashes.

**When applicable:** model/checkpoint/adapter digests, prompt hashes, container digest, compiler flags, dataset edition, external service revisions.

---

## 8. Front matter

```yaml
---
id: E-0142
type: ranking_run          # ranking_run | measurement_study | spike | decision
lifecycle: running         # proposed|queued|running|completed|aborted|parked
line: L-lock-free-queue
primary_bottleneck_target: lock_contention
ladder_target: L2
hypothesis: >
  Removing the central lock reduces p99 latency on promote under stats_policy
success_criteria: >
  run_status=valid; gates pass; promotion_outcome=promoted
experimental_control: E-0130
incumbent_at_start: E-0138
metrics: [p99_latency_ms, throughput_qps, peak_rss]
ranking_score: artifacts/runs/R-.../ranking_score.json
tags: [concurrency, latency]
---
```

Valid negative science = `lifecycle: completed` + `hypothesis_outcome: unsupported` and/or `promotion_outcome: retained_incumbent`—not a lifecycle called “failed.”

---

## 9. Operating loop

1. **Orient:** STATUS, LEADERBOARD, contract, git, OPEN_QUESTIONS, bottleneck, promote_access remaining.  
2. **Choose one:** continue / close / branch / decide / park-kill / recover incomplete run.  
3. **Design before mutate:** `exp/E-…` branch, hypothesis, protocol (hashes, control, incumbent, envelope, ladder).  
4. **Execute:** commit as you go; clean tree for L2; CAS promote.  
5. **Record:** three outcomes, metrics vs control **and** incumbent, artifacts, glimmer, next, do-not-retry; regenerate views; merge only if promoted.

---

## 10. Document types (short)

- **STATUS** — mission, #1, gap, bottleneck, next.  
- **Lines** — bet card required for speculative work.  
- **Experiments** — hypothesis / protocol / results; machine rows under `artifacts/runs/`.  
- **Decisions** — yardstick, metric, eps, access policy, authority.  
- **Graveyard** — never delete; search before retry.

---

## 11. Lineage and ideation

Cite parent E-ids + scores. Branch on new mechanisms or separated knobs.  
Ideate from: mission gap, bottleneck table, OPEN_QUESTIONS, graveyard, parked glimmers, iteration-speed limits.  
Mix **exploit** (near-term promote) and **explore** (bet cards). Dedup before new E.

---

## 12. Search protocol

LEADERBOARD → STATUS → contract → grep `research/` → graveyard → promote/ablation logs → related plan/issue trackers. Record prior art in `hypothesis.md`.

---

## 13. Writing standard

Numbers, paths, E-ids. One bottleneck per ranking experiment. Quote logs sparingly; link artifacts.

---

## 14. Cadence

| Rhythm | Action |
|--------|--------|
| Session start | Orient off leaderboard + bottleneck + access budget |
| Each E | Protocol → ladder → artifacts → regenerate views |
| Promote | CAS vs incumbent; git merge iff promoted |
| Access low | DEV/measurement; refresh promote only via decision |
| Stall | Glimmer? park/continue : kill |
| Quota miss | Force exploration bet |

One exploit line; optional second exploration line with finite `budget_cap`.

---

## 15. Autonomous authority (`agent/AUTHORITY.md`)

Required when an agent runs unattended (humans still obey §§1–14):

- Compute / money / wall-clock ceilings  
- Network allowlist, credentials, secret redaction  
- Allowed data/licenses  
- No production deploy without approval  
- Human gates: contract changes, sealed refresh, AUTHORITY edits, destructive wipes, raising promote_access  
- Protected paths: harness, decide/promote, contract, verify, best pointer, AUTHORITY  
- Escalation: invalid storms, CAS thrash, contamination, budget or access exhaustion  

---

## 16. Binding this SOP to a project

Map once in a short `research/PROJECT_BINDING.md` (or contract field):

| SOP concept | This repo |
|-------------|-----------|
| `success_contract.json` | path |
| decide implementation | e.g. `make promote` / `pytest` gate / custom CLI |
| harness / workloads | paths + how to hash |
| best pointer | path |
| domain caps | e.g. tokens **or** CPU-sec **or** GPU-hours |

Do not fork the SOP per domain—bind it.

### 16.1 AI / ML specialization (optional)

When the artifact is a model or agent: include train/contam policy; prompt/model/adapter digests in manifests; typical caps (tokens, tool calls); keep graders outside self-mod allowlists; never mine sealed task text into training.

### 16.2 Systems / algorithms specialization (optional)

Pin compiler flags, CPU governor, dataset edition, NUMA/pinning; prefer deterministic workloads or report seed policy; profile-derived glimmers are first-class.

---

## 17. Failure modes

| Failure | Fix |
|---------|-----|
| Hand-edited leaderboard | Regenerate; machine wins |
| Overloaded verdict | Three outcome fields |
| Promote vs control only | Always vs incumbent + CAS |
| Overfit promote set | Access budget + sealed cert |
| Resource cheat | Caps count retries/search width |
| Fake assay primary scores | `measurement.json` |
| Dirty unscored “best” | Clean tree or record dirty hash |
| Merge-on-reject | Merge only `promoted` |
| Easy-gain / zombies | Exploration quota + bet cards |
| Authority creep | AUTHORITY.md |

---

## 18. Complexity not included yet

Full FDR day-1; blind sealed vaults before leakage risk; multi-agent schedulers beyond lock+CAS; Pareto multi-primary boards; vector search over markdown.

---

## 19. Session checklist

```text
[ ] Contract + promote_access + AUTHORITY (if agent)
[ ] Fresh generated views; git branch plan
[ ] Bottleneck; exploit vs explore
[ ] Priors / graveyard search
[ ] Design docs before mutate; ranking vs measurement
[ ] L2: clean tree, incumbent digest, stats_policy, CAS
[ ] Artifacts + three outcomes; regenerate views
[ ] Merge iff promoted; park/kill/OPEN_QUESTIONS
```

---

## 20. Definition of done

A cold-start researcher can: read the contract; resolve #1 from the pointer; separate supported vs promoted; replay lineage with numbers and git SHAs; name bottleneck and next move; respect sealed/access/authority rules.
