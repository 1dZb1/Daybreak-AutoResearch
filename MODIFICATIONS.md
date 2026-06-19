# Modifications From Deli AutoResearch

This file documents repository lineage and adaptation notes. The skill body
itself intentionally stays focused on how to work, not on its own version
history.

## Preserved From The Original Direction

Daybreak AutoResearch keeps the core reliability ideas from Deli AutoResearch:

- long-horizon autonomous work;
- zero-interaction during a run;
- state persisted to files;
- fresh-session style state injection;
- stall detection;
- structural pivots after repeated stalls;
- heartbeat watchdogs;
- guardian / worker separation;
- bounded subagent patterns;
- validation between iterations.

## Added In This Adaptation

### STORM-style discovery

The skill now requires a perspective map before deepening one angle. The goal is
to prevent single-angle drift and turn research into a structured question
process rather than a pile of summaries.

Key additions:

- `perspectives.json`;
- role-specific askers;
- retrieval-grounded expert answers;
- follow-up questions driven by what changed;
- source-backed claims, gaps, contradictions, and hypotheses;
- coverage status for each perspective.

### Grounding contract

Nontrivial claims must trace to an external artifact:

- source;
- dataset;
- code;
- log;
- experiment;
- interview;
- other durable evidence.

The adaptation adds source-quality and claim-confidence labels so a high count
of weakly grounded claims cannot masquerade as truth.

### Backbone synthesis

Raw agent outputs are not the final artifact. Findings return to a single
`backbone.md`, which owns the current thesis, outline, grounded claims, open
gaps, contradictions, hypotheses, and residual risks.

### Discovery Tail Pass

The first coherent synthesis is treated as the baseline hypothesis, not the
finish line.

The tail pass asks the run to:

- hunt the misfit;
- break a load-bearing assumption;
- transfer deep structure from a distant domain;
- diverge before judging;
- compress the best candidate;
- try to kill it with a concrete check;
- carry unresolved anomalies forward.

Tail candidates are not discoveries. They become candidates only after they
compress the backbone or produce a checkable consequence.

### Perspective coverage vs. subagents

The adaptation separates two things that are easy to confuse:

- perspective coverage is mandatory when Daybreak is active;
- physical subagents are an execution choice.

Agent count does not prove coverage. Coverage is proven by `perspectives.json`,
grounded claims, contradictions, gaps, hypotheses, and backbone changes.

### Agent-agnostic packaging

The adaptation is packaged as a Codex-compatible skill, but the protocol is not
Codex-only. Any agent environment may use the same state discipline,
perspective coverage, source grounding, watchdog behavior, Discovery Tail Pass,
and sufficiency checks if those contracts are translated into its own runtime.

## What Not To Claim

Do not claim that Daybreak AutoResearch guarantees truth, novelty, or publication
quality. It improves the working loop, evidence discipline, and discovery
pressure, but reality still decides.

Do not claim global novelty from model synthesis alone. A globally novel claim
needs stronger source, experiment, or expert verification.

Do not claim affiliation with Deli Chen, DeepSeek, Stanford OVAL, or the STORM
authors unless such affiliation is separately established.

Do not claim that the upstream Deli AutoResearch framework is Apache-2.0 unless
that license is explicitly published upstream. This repository releases the
Daybreak adaptation and original additions under Apache-2.0 while preserving
upstream attribution.

Do not describe Daybreak AutoResearch as Codex-only. Codex support is a
packaging choice; the protocol is agent-agnostic.
