---
name: daybreak-autoresearch
description: "Use for long-horizon autonomous research, writing, building, monitoring, or experiment runs in any agent environment that must continue with near-zero human interaction and still produce trustworthy, grounded, discovery-oriented output. It combines a reliability spine (state files, anti-loop controls, stall detection, heartbeat watchdog, guardian/worker separation) with an epistemic spine (mandatory STORM-style multi-perspective question asking, adaptive subagent fan-out when it materially helps, retrieval grounding, contradiction and gap mining, a persistent argument backbone, external verification, and sufficiency-based completion)."
---

# Daybreak AutoResearch

## The Idea

An unattended research run has two ways to fail.

It can die: the loop stalls, waits for the user, loses context, crashes, or quietly stops after a callback.

It can also stay alive and become hollow: the heartbeat is green, iterations continue, summaries look polished, but the work is narrow, ungrounded, fabricated, or trapped inside one comfortable angle.

Daybreak exists to keep both things true at once:

```text
alive enough to continue
honest enough to trust
wide enough to discover
disciplined enough to finish
```

Use this skill when the run must work for hours, days, or weeks without asking the user to steer it. The protocol is agent-, model-, and tool-agnostic: the backend can change, but the state, grounding, perspective coverage, and verification contracts must remain.

## Fit Check

Use Daybreak when at least one of these is true:

- The agent must keep working without frequent human responses.
- The task spans many iterations, sources, experiments, or artifacts.
- The output must be research-grade, report-grade, paper-like, or decision-grade.
- The run has to monitor, recover, restart, or continue after callbacks.
- The work needs new findings, not only a tidy summary.
- Multiple perspectives are needed to avoid single-angle drift; subagents or source-class fan-out may help execute that coverage.
- Trustworthiness matters: claims must trace to sources, experiments, logs, code, or other external artifacts.

Do not use Daybreak for a short answer, a one-off literature search, a tiny code patch, or a task where the user explicitly wants interactive steering at each step.

## Non-Negotiable Behavior

1. **Zero interaction during the run** - do not stop to ask the user what to do next. Resolve ambiguity locally and log the decision.
2. **Ready means execute** - if setup is complete, start the run, submit the job, start the monitor, or continue the next iteration. Do not ask whether to proceed.
3. **Callback means report-alive** - after any callback or resumed heartbeat, first update `last_seen`, then check liveness and restart failed loops if needed.
4. **State lives in files** - progress, claims, sources, perspectives, and decisions live in `state/` and `logs/`, not in conversation memory.
5.  **The working goal stays explicit** - at startup, write the current goal; after each meaningful evidence shift, pivot, scope change, or completion check, update it. Each update must say how it moves the run closer to closing the original goal; otherwise mark it as `scope_change`, `offshoot`, or `blocked`, not ordinary progress. A run without a live goal becomes motion, not research.
6. **Fresh sessions over resume** - each work iteration starts with curated state injection; avoid accumulating stale context.
7. **Guardian and worker stay separate** - a heartbeat patrol may check liveness, restart, or nudge; it does not silently do the worker's research or rewrite its state.
8. **Grounded means traceable** - every nontrivial claim must trace to an external artifact. Ungrounded claims are held, not published.
9. **Discovery means pressure, not decoration** - a finding is discovery-grade only if it changes the backbone, closes a gap, resolves a contradiction, opens a testable hypothesis, or falsifies a tempting path.

## Autonomy Boundaries

Zero interaction is not unlimited authority. "Ready means execute" covers research moves — submit, resubmit, fix, monitor, continue. It does not cover actions whose damage outlives the run.

Before the run starts, classify the tools it can reach. Under zero interaction, these action classes still require a pre-authorized allowlist or a human gate:

- moving money or committing spend beyond the run's own compute;
- sending or publishing outside the run (email, message, post, third-party write);
- deleting or overwriting data that is not the run's own scratch state (drop, truncate, mass-update, history rewrite);
- deploying, merging to a protected branch, or changing production;
- changing access, credentials, permissions, or security settings;
- moving data across a trust boundary.

Everything else proceeds without asking — that is the point of the protocol.

## The Two Axes

| Axis | Question | Failure if missing | Daybreak mechanism |
|------|----------|--------------------|--------------------|
| Reliability | Will the run survive over time? | loop / stall / crash / context loss | heartbeat, state files, anti-loop metrics, guardian/worker split |
| Honesty | Is the work broad, grounded, and checked? | shallow / biased / fabricated / self-graded | STORM-style perspective engine, source ledger, claim checks, external evaluation |

Neither axis substitutes for the other. A reliable agent with weak grounding ships polished fiction. A grounded agent that dies in hour two ships nothing.

## Architecture

```text
orchestrator
  -> derive or refresh perspective space
  -> launch bounded work passes or interrogation agents when they materially help
  -> collect sources, claims, contradictions, gaps, hypotheses
  -> reconcile into backbone.md
  -> classify stagnation vs convergence
  -> verify grounding and external quality
  -> continue, pivot, or complete by sufficiency

guardian
  -> check last_seen and state freshness
  -> restart failed loops
  -> nudge only when stalled
  -> never certify research quality

workers
  -> run one bounded question, perspective, experiment, or verification pass
  -> write append-only evidence
  -> never rely on conversation memory as source
```

Core design rules:

- Separate execution from evaluation.
- Separate production from grounding.
- Derive coverage before deepening one angle.
- Keep one argument backbone, even when many agents fan out.
- Do not confuse perspective coverage with physical subagents: coverage is required; agent fan-out is an execution choice.
- Treat every self-rating as a trend signal, never as proof of quality.

## The STORM Discovery Engine

STORM-style work is not "brainstorm broadly." It is structured multi-perspective question asking against retrieved evidence, tail-search against the first coherent synthesis, and synthesis into one backbone.

Perspective coverage is mandatory whenever Daybreak is in force. Physical subagents are adaptive execution machinery: use them when they materially improve coverage, verification, parallelism, resilience, or context load. If subagents are unavailable or too expensive, the orchestrator must still run explicit perspective passes and record them in state.

The engine has seven moves.

### 1. Frame The Discovery Target

Write `task_spec.md` before the first run:

```text
goal:
audience_or_use:
artifact_to_produce:
what would count as a new finding:
known constraints:
source boundaries:
minimum grounding threshold:
completion threshold:
```

Define discovery carefully. A "new finding" may be:

- new to the run: a source-backed claim not yet in the backbone;
- new to the user/project: a relation, contradiction, or path not previously captured;
- globally novel: only after stronger source, experiment, or expert verification.

Do not claim global novelty from model synthesis alone.

### 2. Discover The Perspective Space

Before deep work, survey how the topic is actually covered.

Mine perspectives from:

- related papers, reports, textbooks, standards, reviews, issue trackers, datasets, codebases, forums, or prior artifacts;
- table-of-contents structures and recurring section shapes;
- stakeholder roles and incentives;
- failure modes, objections, and evaluation criteria;
- adjacent domains that may reveal missing questions.

Write `perspectives.json`:

```json
{
  "perspectives": [
    {
      "id": "p01",
      "name": "example perspective",
      "why_it_matters": "...",
      "seed_sources": ["..."],
      "coverage_status": "unopened|active|covered|blocked",
      "open_questions": [],
      "known_limits": []
    }
  ]
}
```

Use 4-9 perspectives for most research. Fewer risks narrowness; many more usually adds noise unless the topic is huge.

### 3. Run Grounded Interrogation

For each perspective, run a question loop:

```text
asker persona -> asks a sharp question
retriever -> fetches sources
expert -> answers only from retrieved evidence
asker -> asks follow-up based on what changed
worker -> appends claims, sources, gaps, and contradictions
```

The asker should be role-specific: skeptic, practitioner, historian, engineer, domain scientist, reviewer, user, adversary, funder, maintainer, or another lens that changes the questions.

Every answer must produce at least one of:

- a source-backed claim;
- a contradiction between sources;
- an unresolved gap;
- a candidate hypothesis;
- a live anomaly or misfit carried forward;
- a reason the perspective is exhausted or blocked.

Append sources to `sources.jsonl`, claims to `claims.jsonl`, and findings to `findings.jsonl`.

### 4. Run The Discovery Tail Pass

Treat the first coherent synthesis as the baseline hypothesis, not the finish line. The most fluent answer is often the consensus continuation; discovery pressure starts by trying to beat it.

Before promoting a finding, run a small tail pass:

1. **Hunt the misfit** - name the source, perspective, datapoint, log, implementation detail, or intuition that does not fit the clean story. Do not smooth it away.
2. **Break the assumption** - ask what must be true for the misfit to be harmless, then drop that load-bearing assumption for one pass.
3. **Transfer structure** - import the relational skeleton of a distant domain, not its surface metaphor. Ask what this problem is isomorphic to far away.
4. **Diverge before judging** - create several genuinely different candidate moves, including at least one low-probability move. Prune only after the spread exists.
5. **Compress** - prefer the candidate that explains more with fewer principles, unifies two things treated separately, or makes the backbone shorter.
6. **Try to kill it** - derive one concrete, checkable consequence and attack it with sources, data, code, logs, experiments, or a focused adversarial pass.
7. **Carry the residual** - write unresolved misfits as live anomalies for the next iteration instead of closing them as "interesting but unclear."

Tail candidates are not discoveries. They remain search candidates until grounded, compressed, and attacked by a real check.

Use existing state files rather than creating a new ledger by default: unresolved misfits belong in `gaps.jsonl`, `contradictions.jsonl`, `hypotheses.jsonl`, `iteration_log.jsonl`, and the relevant section of `backbone.md`.

### 5. Mine Gaps, Contradictions, And Hypotheses

New discoveries usually appear where perspectives do not agree cleanly.

Maintain:

- `gaps.jsonl` - missing evidence, missing perspective, missing experiment, missing implementation path.
- `contradictions.jsonl` - source A says X, source B says not-X, code/log/data says Y, or practice diverges from theory.
- `hypotheses.jsonl` - testable claims that could explain a gap or contradiction.

Each entry needs:

```text
id / perspective / claim / evidence / why it matters / falsifier / next test / backbone impact
```

A hypothesis without a falsifier is only an idea. Keep it, but do not promote it as a finding.

### 6. Reconcile Into The Backbone

`backbone.md` is the answer to lost thread. It is the living argument spine, not a pile of notes.

Every reconciliation pass updates:

- current thesis or central answer;
- outline;
- claims that are grounded;
- claims that are promising but not grounded;
- contradictions and how they are being handled;
- gaps and planned tests;
- live anomalies carried into the next iteration;
- what changed since the last iteration;
- sections ready to write or ship.

Fan-out is useful only if it returns to one voice. A long artifact must feel like one mind learned, not like many summaries were stapled together.

### 7. Write Or Build From The Backbone

Write, build, or decide from `backbone.md`, not directly from raw agent outputs.

For writing:

- each nontrivial claim needs a source id;
- sections should cite the claim ledger, not model memory;
- citations are checked against source content before publication.

For code or experiments:

- claims map to tests, logs, benchmark results, reproductions, or source references;
- generated artifacts record the command, environment, and input state;
- failures become findings when they change the next question.

## State Files

Use this default layout:

```text
{task}/state/
  task_spec.md
  progress.json
  perspectives.json
  backbone.md
  sources.jsonl
  claims.jsonl
  findings.jsonl
  gaps.jsonl
  contradictions.jsonl
  hypotheses.jsonl
  directions_tried.json
  iteration_log.jsonl

{task}/logs/
  work.jsonl
  orchestrator.jsonl
  heartbeat.jsonl
```

`progress.json` should include:

```json
{
  "iteration": 0,
  "status": "running",
  "last_seen": "",
  "total_findings": 0,
  "perspectives_total": 0,
  "perspectives_covered": 0,
  "grounding_ratio": 0.0,
  "open_gaps": 0,
  "open_contradictions": 0,
  "active_hypotheses": 0,
  "stale_count": 0,
  "completion_candidate": false
}
```

Log line format:

```json
{"ts":"...","source":"...","level":"info|warn|error|decision","event":"...","detail":"..."}
```

## Grounding Contract

Grounding is not a vibe. It is a trace.

Each `sources.jsonl` entry should include:

```json
{
  "id": "s001",
  "title": "",
  "url_or_path": "",
  "source_type": "paper|book|docs|code|dataset|log|interview|web|other",
  "retrieved_at": "",
  "quote_or_span": "",
  "quality": "primary|secondary|tertiary|weak|unknown",
  "limitations": ""
}
```

Each `claims.jsonl` entry should include:

```json
{
  "id": "c001",
  "claim": "",
  "source_ids": ["s001"],
  "confidence": "proven|supported|plausible|speculative|stale",
  "perspective_id": "",
  "backbone_section": "",
  "falsifier": "",
  "status": "held|accepted|rejected|needs_check"
}
```

Rules:

- A claim with no source, test, log, dataset, or artifact is held.
- Citation-like content is checked against the retrieved source text every 20 claims, never only at the end.
- Source quality is part of the claim. A high `grounding_ratio` against weak sources is false comfort.
- Generated summaries, old memory, and model confidence are leads, not sources.

## Stall, Convergence, And Pivot

Do not punish honest convergence. Do not mistake silence for completion.

| Signal | Classify as | Action |
|--------|-------------|--------|
| No new findings and uncovered perspectives remain | Stagnation | increment `stale_count`, pivot to uncovered perspective |
| No new findings and current perspective is grounded and covered | Convergence | mark perspective covered; move to next perspective or completion check |
| Many findings but low grounding ratio | Epistemic drift | stop writing; run source and claim verification |
| Many claims from one perspective only | Coverage imbalance | open a neglected perspective |
| Repeated weak hypotheses | Frame failure | change perspective space or source class |
| Contradictions unresolved across iterations | Discovery pressure | assign focused contradiction resolution |

Pivot rules:

- `stale_count >= 2`: pivot structure, not tactics. Change source class, perspective, artifact boundary, or evaluation method.
- `stale_count >= 4`: flag for owner attention, but keep a clean report and recovery proposal.
- Next direction is the most valuable uncovered perspective or unresolved contradiction, not random novelty.

## Completion By Sufficiency

The run finishes when the work is sufficient, not when the timer runs out.

Completion requires:

- perspective coverage meets threshold;
- `backbone.md` is coherent and complete enough for the requested artifact;
- grounding ratio meets threshold and weak-source limitations are named;
- open contradictions are resolved, downgraded, or explicitly carried as uncertainty;
- key hypotheses have tests, falsifiers, or clear "not enough evidence" labels;
- an outside evaluation pass has reviewed the artifact or claim ledger;
- the final output states residual risk.

Round and time caps are safety ceilings against runaway work, not the primary definition of done.

## Heartbeat Watchdog

Use three layers when the run must survive unattended:

| Layer | Form | Role |
|-------|------|------|
| L0 | resident shell or external guard | detect stale heartbeat and start emergency patrol |
| L1 | durable scheduled patrol | check `last_seen`, restart timed-out loops, classify stall vs convergence |
| L2 | business loop | update its own `last_seen` first on every callback |

Stall detection:

- heartbeat stale over threshold and last output was a question -> stalled, nudge or restart;
- task has entered completion check -> not stalled; do not nudge;
- three consecutive nudges with no progress -> structurally stuck; reopen perspective space or source boundary.

The guardian may check, restart, and nudge. It must not certify research quality or rewrite worker state.

## Subagent Patterns

| Pattern | Use | Key idea |
|---------|-----|----------|
| Goal-driven worker | ordinary research iteration | one bounded question, verifiable findings, append-only state |
| Parallel exploration | independent subproblems | separate perspectives, hypotheses, or source classes |
| Experiment runner | long jobs | submit, poll, diagnose, fix, resubmit when routine |
| Verification worker | quality gate | audit claims against `sources.jsonl` and artifacts |
| Perspective interrogator | STORM breadth | role-specific Q&A grounded in retrieval |
| Backbone reconciler | synthesis | merge findings into one argument and resolve contradictions |
| Skeptic or adversary | discovery pressure | attack assumptions and search for falsifiers |

Every subagent prompt should include:

```text
role / question / source paths / writable paths / forbidden actions / evidence standard / output format / exit condition
```

Use subagents when they materially reduce uncertainty, time, risk, verification cost, or context load. Keep one integrator responsible for the final answer. Do not treat agent count as coverage; coverage is proven by `perspectives.json`, grounded claims, contradictions, gaps, and backbone changes.

## Engineering Constraints

- Read or write at most 5 large files per iteration unless the task explicitly needs more.
- Avoid single generated files over 300 lines unless the artifact format requires it.
- Inject state via files, not conversation history.
- Run validation between iterations when the task has tests, compilers, linters, source checks, or evaluators.
- Verify citation-like content against source text every 20 claims.
- Prefer coverage over depth until the perspective map is sufficiently covered.
- Deepen only when an under-supported claim requires it.
- Escalate external dependency failures with a report; do not abandon silently.

## What An Iteration Produces

Each iteration should leave a small, inspectable trail:

```text
iteration summary:
perspective touched:
new sources:
new claims:
new gaps:
new contradictions:
new or changed hypotheses:
live anomaly carried forward:
backbone change:
grounding ratio:
stagnation/convergence classification:
next action:
```

If an iteration cannot name what changed in the backbone, it probably produced activity rather than progress.

## Limits

- Retrieval bounds truth. If the retriever cannot reach the right corpus, the run must say `corpus-limited`.
- Perspective discovery needs material. Narrow or emerging topics may require hand-seeded perspectives.
- External checking catches more errors; it does not remove the model's ability to hallucinate.
- Separation of duties is protocol, not virtue. If grounding, external eval, or guardian/worker split is removed, drift returns.
- Novelty pressure can create fiction. Discovery is encouraged, but ungrounded novelty is held, not shipped.
- Tail-search can overvalue strangeness. A low-probability candidate earns attention only when it compresses the backbone or creates a concrete test.

## What To Resist

- Do not keep version history, changelogs, or "what changed" sections inside the skill. The skill should describe how to work.
- Do not treat liveness as quality.
- Do not treat self-rating as evaluation.
- Do not let one perspective dominate because it was first or easy.
- Do not accept the first coherent synthesis as final when live anomalies remain.
- Do not force novelty when convergence is honest.
- Do not publish claims that are only model memory.
- Do not batch all source checks at the end.
- Do not let fan-out become a patchwork artifact with no center.

## The Closing Check

Before calling the run ready, ask:

- Is the run alive and restartable?
- Is the state inspectable without conversation memory?
- Are perspectives covered enough for the task?
- Are nontrivial claims grounded?
- Were contradictions and gaps mined, not smoothed over?
- Did the backbone change because of evidence?
- Did an outside evaluation path check the result?
- Is the final artifact coherent in one voice?
- Are residual limits stated plainly?

If any answer is unstable, continue the run or downgrade the claim.
