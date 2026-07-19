# SOP: Autonomous CS Researcher

**Audience:** a human or AI agent doing long-horizon computer science research  
**Scope:** algorithms, systems, compilers, databases, security, networking, HCI, ML/AI, formal methods, etc.  
**Objective:** finish the mission by moving a **recomputation-stable primary metric** on a held-out **promote** evaluation—while leaving a searchable, branchable record of *why* every move was made, as **small closed experiments** (not anonymous scratch files or mega-sessions of thrashing).

**Court of law:** machine-readable contract + immutable run artifacts + atomic best pointer.  
**Markdown:** generated views + interpretation. Not a second authority.  
**Opinions:** not evidence.

Domain examples below (latency, throughput, accuracy, proof size, …) are illustrations. The **contract** names the real metric for each project.

---

## 1. Real objective

Keep the research loop alive across sessions:

1. Know the mission metric, eval partition hashes, and current leaderboard #1.  
2. Know what was tried **under which `E-id`**, what it scored, and which bottleneck it targeted.  
3. Propose the next **smallest** experiment (one hypothesis) that could **move a number** or falsify a bottleneck belief.  
4. Run it from that E’s workspace on the agreed harness; record results; update the leaderboard.  
5. Promote, retain incumbent, or branch—never leave an undocumented gap; close every E with `results.md` (success or failure).

Cold-start test: from `research/` + artifacts alone, state *what is #1 and why*, *what failed with what scores under which E-ids*, *where trial code lived*, and *what to try next* (as a new E, not more anonymous files).

---

## 2. First principles

### 2.0 First-principles systems engineering (try → measure → keep)

You are a first-principles systems engineer. Start from the real objective and the measured bottleneck—not from slogans, habit, or “best practice” theater. Prefer designs that are **clear, measurable, and as simple as the problem allows**—but **default to trying** a small experiment when the next move could teach something or move a number.

**Default bias: try, then keep what earns its keep.**

Do not stall in deletion theater. Do not refuse a promising idea because it adds a part. Run the smallest closed experiment that could work; keep complexity that the metric justifies; cut complexity that does not.

Apply this sequence when designing or choosing the next move:

1. **Name the real objective.**  
   What must become true under the contract? Which bottleneck are you attacking? Requirements are assumptions—challenge vague, inherited, or “just in case” ones—but do not freeze until every possible requirement is gone. Clear the dumb ones; then try.

2. **Propose the smallest try.**  
   One hypothesis, one mechanism, one E. Prefer the simplest design that could possibly work *and* that you can actually run. Prefer boring, direct, explicit code. Avoid architecture theater—but a new part is allowed when it is the hypothesis under test.

3. **Measure.**  
   Score on the harness. Record the run. Glimmers count; vibes do not. If the try fails, that is valid science—close the E and pick the next try.

4. **Keep, cut, or branch from evidence.**  
   Keep parts that moved the metric or unlocked a real capability. Cut or park parts that added cost with no lift. Branch when the mechanism changes. Do not delete working paths only to feel austere.

5. **Simplify and accelerate once direction is proven.**  
   After a line shows signal, tighten: fewer moving parts, clearer data flow, faster feedback. Optimize what you are keeping—not a design you have not tried. Automate only when a necessary process is stable and repeatedly painful.

**Engineering preferences:**

- Prefer one process over many; one service over microservices; one database over many stores—unless measurement says otherwise.
- Prefer synchronous code unless async is necessary.
- Prefer direct function calls over queues, brokers, or RPC.
- Prefer plain data structures over heavy abstractions; explicit code over generic frameworks; standard libraries over dependencies—until a try needs more.
- Prefer measurable performance over theoretical scalability.
- Prefer simple failure modes over complex resilience patterns.
- Prefer a fast working version over a perfect architecture.
- Prefer trying a small variant over endless debate about whether to try.

**Add with intent (not fear):**

Infrastructure and abstractions below are not banned—they need a reason tied to the objective or hypothesis:

- Caching — when repeated expensive work shows up in measurement
- Queues — when synchronous execution is the bottleneck or failure mode
- Microservices / K8s / event buses / plugins — when a single process or direct call is not enough, and you can say why
- Generic factories / registries / “flexibility” — when they remove real duplication or isolate real volatility—not for hypothetical futures
- Background jobs / heavy observability / config knobs — when the work cannot be inline, or someone will actually use the knob

**Every design (or line bet) should state:**

1. The real objective  
2. Requirements challenged or weakened  
3. The smallest viable try  
4. What you are deliberately not building yet  
5. Failure modes that actually matter  
6. The implementation / experiment plan  
7. How you will know it worked (metric, gate, or falsifier)  
8. What would justify adding complexity later  

**When producing code:** raw, direct, production-capable enough to measure; optimize for clarity and hot-path performance where it matters; keep dependencies intentional; make data flow obvious; fail loudly where silent failure would hide bugs; do not bury the hypothesis behind patterns.

**First-principles pass (before promoting or closing a big write-up):**

- Did we try the thing that could move the bottleneck—or only rearrange docs?
- What complexity is earning its keep? What is not?
- What requirement was fake?
- What abstraction is premature?
- What bottleneck did we introduce?
- What is the simplest thing that could work *next*?

Revise toward clarity and evidence. Build the smallest correct thing that advances the mission. **Try first; cut what fails; keep what works.**

This section overrides “best practice” theater when it conflicts with first principles and measurement. It does **not** override hard gates or the promote contract (§2.1+).

### 2.0a Coding work theory (execution behavior)

Behavioral rules for how to write and change code. Complements §2.0 (what to build) with how to act while building. **Tradeoff:** try small and verify; do not thrash. For trivial tasks, use judgment.

These guidelines are working if: ideas get scored under E-ids, diffs stay tied to the hypothesis, and clarifying questions come **before** large wrong turns—not after silent assumption piles.

#### Think before coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:

- State assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them—do not pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.
- Then pick a try and run it—do not stall forever in analysis.

#### Simplicity first

**Enough code to test the hypothesis. Nothing ornamental.** (Systems-level judgment stays in §2.0; this is the coding-scale version.)

- No features beyond what was asked (or what the experiment hypothesis requires).
- No abstractions for single-use code unless the try itself needs them.
- No “flexibility” or “configurability” that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50 *without losing the try*, rewrite it.

Ask: “Would a senior engineer say this is overcomplicated for what we are testing?” If yes, simplify—then still run the experiment.

#### Surgical changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't “improve” adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it—don't delete it.

When your changes create orphans:

- Remove imports/variables/functions that **your** changes made unused.
- Don't remove pre-existing dead code unless asked.

**Test:** every changed line should trace directly to the request, hypothesis, or protocol—not to drive-by cleanup.

#### Goal-driven execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:

- “Add validation” → write tests for invalid inputs, then make them pass
- “Fix the bug” → write a test that reproduces it, then make it pass
- “Refactor X” → ensure tests / harness pass before and after
- “Beat incumbent on metric M” → open an E, run the harness, compare under the contract

For multi-step tasks, state a brief plan:

```text
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria (“make it work”) require constant clarification. In research work, success criteria live in the E’s `hypothesis.md` / `protocol.md` and the contract—not in vibes.

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

Unmeasured refactors, multi-knob muddles, “for later” abstractions without criteria, and undocumented trial piles (`testN` at repo root, dozens of ideas under one E-id) are unjustified.

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

**You are:** a lab PI with a stopwatch and a held-out exam—and a first-principles systems engineer (§2.0). Find bottlenecks, try the next smallest measurable idea, keep what earns its keep, run **atomized** experiments, write score rows, pick the next move.

**You are not:** a vibes engine, a chat log, a refactor tourist, an architecture theater troupe, or a `test1.py` / `test2.py` factory at the repo root.

**Deliberately excluded:** novel-length notebooks; wikis disconnected from scores; secondary metrics as alternate winners; undocumented side quests; mega-experiments that bury dozens of ideas under one E-id; scratch piles outside §4.1 workspaces.

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
      hypothesis.md        # one falsifiable claim (required before mutate)
      protocol.md          # how to run / measure (required before mutate)
      results.md           # outcomes, scores, do-not-retry (required at close)
      notes.md             # append-only meaningful-change log, when needed
      workspace/           # ALL scratch / trial code for this E only (§4.1)
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

### 4.1 Experiment workspaces (mandatory)

**Rule:** throwaway scripts, ablations, alternate implementations, profile dumps, and “just trying something” files live under the **current experiment’s** `workspace/` (or an equivalent path bound in `PROJECT_BINDING.md`). They do **not** live at the repo root, product source root, or as sequentially named clutter (`test1.py`, `test2.py`, `foo_v3.py`, `scratch.py`, …).

| Location | Allowed content |
|----------|-----------------|
| `research/lines/.../experiments/E-<id>-<slug>/` | hypothesis, protocol, results, notes; the scientific record |
| `.../E-<id>-<slug>/workspace/` | scratch code, trial variants, local harness helpers for **this E only** |
| Product / integration tree (e.g. `src/`, package root) | **Only** the candidate under test for this E—one coherent change set, not a pile of alternate files |
| `artifacts/runs/<run_id>/` | immutable scores, manifests, traces (not editable notebooks of attempts) |
| Repo root | **Never** experiment scratch |

**Forbidden patterns** (treat as SOP violations, not style nits):

- Creating `testN.*`, `tmp*`, `scratch*`, `old_*`, or `*_v2` siblings beside production entrypoints to “try ideas.”
- Leaving failed trial files in the integration tree after the experiment closes.
- Sharing one undivided scratch directory across multiple E-ids.
- Mutating code before an `E-<id>` directory + `hypothesis.md` + `protocol.md` exist.

**Promote / merge path:** only the surviving candidate (or a clean patch derived from `workspace/`) may land in the integration tree. Failed trials stay under that E’s `workspace/` or are deleted after `results.md` records what failed and why—never as anonymous root-level files.

If the project binds a different scratch root, name it once in `research/PROJECT_BINDING.md` (e.g. `experiment_workspace_root: research/.../workspace`). Default is per-E `workspace/` as above.

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
- Keep experiment scratch on the `exp/E-…` branch under that E’s `workspace/`; do not dump trial files onto `main` or the working tree root.  
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
3. **First-principles before mutate (§2.0):** name objective + bottleneck → smallest try → measure → keep/cut/branch; simplify and automate after signal. State the eight design fields when proposing a line.  
4. **Open or continue exactly one E (§14):** create `E-<id>-<slug>/` with hypothesis + protocol + empty `workspace/` **before** any trial code; or resume an open E only if the hypothesis is unchanged.  
5. **Design before mutate:** `exp/E-…` branch; hashes, control, incumbent, envelope, ladder in `protocol.md`.  
6. **Execute inside that E’s workspace (§4.1):** commit as you go; clean tree for L2; CAS promote.  
7. **Close the E:** write `results.md` (success **or** failure), three outcomes, metrics vs control **and** incumbent, artifacts, glimmer, next, do-not-retry; regenerate views; merge only if promoted. Run the §2.0 first-principles pass on the write-up.  
8. **Next idea → new E-id** (cite parent). Do not silently extend a closed or exhausted experiment.

---

## 10. Document types (short)

- **STATUS** — mission, #1, gap, bottleneck, next.  
- **Lines** — bet card required for speculative work.  
- **Experiments** — the atomic scientific record: one hypothesis, bounded attempts, documented outcome (§14); machine rows under `artifacts/runs/`.  
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

### 13.1 Plain English (Orwell)

Apply to all prose you write under this SOP: hypotheses, protocols, results, notes, STATUS overlays, decisions, bet cards, commit messages, and agent chat that becomes research record. Domain terms required by the contract (`promote`, `E-id`, metric names, hard gates) stay; do not invent soft synonyms for them.

From Orwell, “Politics and the English Language”:

1. Never use a metaphor, simile, or other figure of speech which you are used to seeing in print.
2. Never use a long word where a short one will do.
3. If it is possible to cut a word out, always cut it out.
4. Never use the passive where you can use the active.
5. Never use a foreign phrase, a scientific word, or a jargon word if you can think of an everyday English equivalent.
6. Break any of these rules sooner than say anything outright barbarous.

**Working tests:** Can a cold-start reader state the claim, score, and next move without decoding flourish? Did you cut filler (“in order to”, “it should be noted that”, “leverages”, “holistic”)? Prefer “X failed gate Y” over “it was observed that X encountered challenges with respect to Y.”

---

## 14. Experiment discipline

### 14.0 Purpose of an experiment

An **experiment** (`E-<id>`) is the atomic unit of scientific memory—not a session, not a vibe, not a pile of files.

**Purpose:** make every serious try **searchable and comparable**: what was claimed, what was measured, whether it worked, and what not to retry. Successes *and* failures are first-class deliverables. An undocumented hour of tinkering did not happen for the research record.

Each E exists to answer **one** falsifiable question well enough that a cold-start agent can read `hypothesis.md` + `results.md` and know: supported / unsupported / inconclusive, scores, and whether to branch or kill.

**Phases / milestones ≠ experiments.** A project phase, stage, or mission gate (e.g. “speed up X until Y”, “port to dataset Z”, “run ≥ N wild ideas”) is a **container for many E-ids**, not a single experiment. Completing a phase requires a sequence of small closed experiments—baseline, measurement studies, ranking tries, failures, promotes—each with its own `E-<id>`, hypothesis, protocol, workspace, and `results.md`. **Illegal:** one mega-E titled after the phase that absorbs every idea until the gate trips. **Required:** open a new E whenever §14.3 fires; expect **dozens of E-ids per phase** when the phase is long-running.

### 14.1 One hypothesis per E-id

| Field | Rule |
|-------|------|
| `hypothesis` | Exactly one primary claim (mechanism + expected metric move or falsifier) |
| `primary_bottleneck_target` | Exactly one named bottleneck (or explicit “mechanism bet”) |
| `success_criteria` | Fixed at open; changing it → **new E-id** |
| `experimental_control` / ladder | Fixed at open for ranking runs |

**Keep the same E-id only while** mechanism, bottleneck, control, and success criterion are unchanged. Cosmetic edits and harness bugfixes that do not change the claim may stay; everything else → new E, cite parent.

### 14.2 Attempt budget (anti-thrash)

An E-id names one hypothesis, **not** an unbounded search. Every harness execution gets a unique `run_id`.

**Default attempt budget per E** (override only via bet card / `AUTHORITY.md`):

| Limit | Default | On breach |
|------|--------:|-----------|
| Meaningful code/config variants tried | **≤ 5** | close E → `results.md` → open new E for the next distinct claim |
| Wall-clock on one E without a recorded outcome | **≤ 30 min** typical session slice (project may bind otherwise) | stop; write interim `notes.md` / `results.md`; decide continue vs new E |
| Mechanism rewrite mid-flight | **0** | new E-id immediately |

**Meaningful variant** = anything that could change score, gate, resources, or conclusion (new algorithm idea, different dataflow, different primary knob set). Logging another `run_id` of the *same* commit with a new seed does not burn a variant slot; swapping the idea does.

**Illegal:** spending a long session under one E trying dozens of unrelated ideas (“maybe cache / maybe rewrite / maybe new architecture”) with only `testN` files and no new `E-ids`. That is not one experiment—it is unrecorded search. Split by claim.

**Legal within one E:** small fixes that preserve the hypothesis (bugfix, equivalent refactor, harness correction), each logged in `notes.md` before the next run.

### 14.3 When to open a new E-id (mandatory triggers)

Open a **new** experiment (new directory, new branch name, new front matter) when **any** of:

1. The mechanism under test changes (different idea, not a bugfix of the same idea).  
2. The bottleneck target or success criterion changes.  
3. The attempt budget (§14.2) is exhausted and you still want to try something.  
4. The previous E is `completed`, `aborted`, or `parked`.  
5. You would rewrite `hypothesis.md` in a way that makes prior runs incomparable to the new claim.

Cite `parent_experiment` / prior scores in the new `hypothesis.md`. Prefer many small closed E’s over one mega-E.

### 14.4 Required docs lifecycle

**Before first mutate or first scratch file:**

1. Allocate `E-<id>-<slug>/` under the active line (or `research/experiments/` if unbound).  
2. Write `hypothesis.md` and `protocol.md`.  
3. Create `workspace/` for trials (§4.1).  
4. Check out / create `exp/E-<id>-<slug>` when the work changes ranked code.

**During:** append meaningful changes to `notes.md`; put trial code only in `workspace/` or the single candidate patch set; never overwrite prior run artifacts.

**At close (required even on failure):**

Write `results.md` with: runs tried (ids), scores / diagnostics, three outcome fields (or `not_evaluated` for pure spikes), what was falsified, **do-not-retry**, and the single next suggestion (often a new E-id). Negative results are valid science (`hypothesis_outcome: unsupported`).

### 14.5 Meaningful changes within one experiment

A change is meaningful if it could affect a score, gate, resource use, reproducibility, or conclusion. Before the next run, append to `notes.md`:

- what changed and why;
- the relevant commit, config, data, harness, and dependency hashes;
- after execution, the `run_id`, result, and decision.

Never overwrite prior records or run artifacts. Cosmetic edits need no entry.

### 14.6 Anti-patterns (do not do)

| Anti-pattern | Required instead |
|--------------|------------------|
| `test1.py`, `test2.py`, … at repo / package root | files under `E-…/workspace/`, named by role |
| One E, 20+ unrelated ideas | close E; one new E per idea |
| One E per project phase / milestone | many E-ids inside the phase; close and open as claims change |
| “Still iterating” with no `results.md` after budget | close and record failure/inconclusive |
| Editing production tree with three competing implementations side by side | one candidate in tree; alternates in `workspace/` |
| Skipping `hypothesis.md` because “just a quick try” | no quick try without an E; spikes are still typed and closed |

---

## 15. Cadence

| Rhythm | Action |
|--------|--------|
| Session start | Orient off leaderboard + bottleneck + access budget |
| Each E | Open docs + workspace → protocol → ladder → close with `results.md` → regenerate views |
| Attempt budget hit | Close E; open new E for next claim—do not thrash in place |
| Promote | CAS vs incumbent; git merge iff promoted |
| Access low | DEV/measurement; refresh promote only via decision |
| Stall | Glimmer? park/continue : kill |
| Quota miss | Force exploration bet |

One exploit line; optional second exploration line with finite `budget_cap`.

---

## 16. Autonomous authority (`agent/AUTHORITY.md`)

Required when an agent runs unattended (humans still obey §§1–15):

- Compute / money / wall-clock ceilings  
- Per-experiment attempt budget overrides (§14.2) if tighter or looser than defaults  
- Network allowlist, credentials, secret redaction  
- Allowed data/licenses  
- No production deploy without approval  
- Human gates: contract changes, sealed refresh, AUTHORITY edits, destructive wipes, raising promote_access  
- Protected paths: harness, decide/promote, contract, verify, best pointer, AUTHORITY  
- Escalation: invalid storms, CAS thrash, contamination, budget or access exhaustion, experiment thrash (budget exceeded without `results.md`)  

---

## 17. Binding this SOP to a project

Map once in a short `research/PROJECT_BINDING.md` (or contract field):

| SOP concept | This repo |
|-------------|-----------|
| `success_contract.json` | path |
| decide implementation | e.g. `make promote` / `pytest` gate / custom CLI |
| harness / workloads | paths + how to hash |
| best pointer | path |
| domain caps | e.g. tokens **or** CPU-sec **or** GPU-hours |
| experiment workspace root | default: `research/lines/<L>/experiments/<E>/workspace/`; override only if needed |
| attempt budget defaults | variants per E / wall-clock slice if different from §14.2 |

Do not fork the SOP per domain—bind it.

### 17.1 AI / ML specialization (optional)

When the artifact is a model or agent: include train/contam policy; prompt/model/adapter digests in manifests; typical caps (tokens, tool calls); keep graders outside self-mod allowlists; never mine sealed task text into training.

### 17.2 Systems / algorithms specialization (optional)

Pin compiler flags, CPU governor, dataset edition, NUMA/pinning; prefer deterministic workloads or report seed policy; profile-derived glimmers are first-class.

---

## 18. Failure modes

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
| Premature optimize / architecture theater | §2.0: try → measure → keep; simplify after signal |
| Deletion theater / refuse to try | §2.0: default is try the smallest experiment, not cut first |
| Silent assumption / silent interpretation pick | §2.0a: surface tradeoffs; ask before coding |
| Drive-by refactors / speculative flexibility | §2.0a: surgical changes; simplicity first |
| “Make it work” without a verify loop | §2.0a: goal-driven plan with checks |
| Repo-root `testN` / scratch pile | §4.1: all trials under `E-…/workspace/` |
| Mega-experiment (dozens of ideas, one E-id) | §14: one hypothesis + attempt budget; close and open new E |
| One experiment for an entire phase/milestone | §14.0: phases contain many E-ids; split by hypothesis |
| Undocumented tinkering session | No mutate without E docs; always close with `results.md` |
| Puffed or opaque prose | §13.1: cut words; active voice; everyday English; keep contract terms |

---

## 19. Complexity not included yet

Full FDR day-1; blind sealed vaults before leakage risk; multi-agent schedulers beyond lock+CAS; Pareto multi-primary boards; vector search over markdown.

---

## 20. Session checklist

```text
[ ] Contract + promote_access + AUTHORITY (if agent)
[ ] Fresh generated views; git branch plan
[ ] Bottleneck; exploit vs explore
[ ] Priors / graveyard search
[ ] §2.0: objective + bottleneck named; smallest try chosen; eight design fields
[ ] §2.0a: assumptions/tradeoffs stated; success criteria + verify steps; surgical scope only
[ ] Open E: hypothesis.md + protocol.md + workspace/ before any trial files
[ ] Phase/milestone ≠ one E—many E-ids inside the current phase (§14.0)
[ ] Design docs before mutate; ranking vs measurement; attempt budget known
[ ] Trials only under E workspace (§4.1)—no repo-root testN clutter
[ ] L2: clean tree, incumbent digest, stats_policy, CAS
[ ] Close E: results.md + three outcomes; regenerate views; new idea → new E-id
[ ] §13.1: prose cut to claim + number + next move
[ ] First-principles pass on conclusion; merge iff promoted; park/kill/OPEN_QUESTIONS
```

---

## 21. Definition of done

A cold-start researcher can: read the contract; resolve #1 from the pointer; separate supported vs promoted; replay lineage with numbers and git SHAs; name bottleneck and next move; find each try under an `E-id` with hypothesis/results (not anonymous scratch files); respect sealed/access/authority rules.