# Daybreak AutoResearch

An agent-agnostic autonomous research protocol for long-horizon, grounded,
multi-perspective investigation, packaged as a Codex-compatible skill.

Daybreak AutoResearch is a single-file agent protocol in `SKILL.md`. It is
designed for runs that must continue for hours, days, or weeks without constant
human steering while still keeping research honest: persistent state, source and
claim ledgers, perspective coverage, contradiction and gap mining, heartbeat
watchdogs, and sufficiency-based completion.

## Lineage

This project is an adapted and extended version of **Deli AutoResearch** /
`Deli_AutoResearch`, originally created by **Deli Chen (DeepSeek)**.

Original references:

- Deli Chen / Victor Chen on X: <https://x.com/victor207755822>
- Original framework page: <https://victorchen96.github.io/auto_research/framework.html>

This adaptation preserves the reliability spine of the original framework:

- zero-interaction long-running work;
- state persisted to files rather than chat memory;
- stall detection and structural pivots;
- heartbeat watchdogs;
- guardian / worker separation;
- bounded subagent patterns;
- verification between iterations.

The adaptation adds a stronger discovery and grounding spine:

- STORM-style multi-perspective question asking;
- explicit perspective coverage via `perspectives.json`;
- retrieval-grounded source and claim ledgers;
- gap, contradiction, and hypothesis mining;
- a persistent `backbone.md` argument spine;
- source-quality and claim-confidence labels;
- sufficiency-based completion;
- the **Discovery Tail Pass**, which treats the first coherent synthesis as a
  baseline hypothesis and searches for live anomalies before accepting a result.

See [ATTRIBUTION.md](ATTRIBUTION.md) and [MODIFICATIONS.md](MODIFICATIONS.md)
for more detail.

## License

The Daybreak AutoResearch adaptation and original additions in this repository
are released under the [Apache License 2.0](LICENSE).

This repository also includes a [NOTICE](NOTICE) file with lineage and
attribution notes. Deli AutoResearch has been publicly announced as open source;
however, no explicit upstream license type was located while packaging this
repository. If an upstream license is later published, review this repository
for compatibility.

## Agent-Agnostic Protocol

Daybreak is not tied to Codex. Codex support is the packaging layer in this
repository, not the boundary of the idea. The protocol can be adapted to any
agent environment that can:

- persist state to files or another durable store;
- read curated state at the start of each iteration;
- run bounded work loops without constant human steering;
- call retrieval, search, source, artifact, or experiment tools;
- cover multiple perspectives through subagents, personas, source classes, or
  structured question roles;
- validate outputs against external checks;
- maintain a heartbeat, watchdog, scheduler, or equivalent liveness signal.

Codex users can install `SKILL.md` and `agents/openai.yaml` directly. Other
agent systems can treat `SKILL.md` as the canonical protocol and translate the
state files, watchdogs, perspective coverage, and verification gates into their
own runtime.

## What It Is For

Use Daybreak AutoResearch when a task needs more than a one-pass answer:

- autonomous or long-horizon research;
- paper-like or report-like synthesis;
- source-backed investigation;
- multi-iteration writing, building, or experiment loops;
- discovering new project-specific relationships, contradictions, or next paths;
- recovering from stalls without waiting for the user to restart the work;
- keeping a durable audit trail of what was tried, found, checked, and left open.

Do not use it for tiny questions, one-off summaries, trivial code edits, or work
where the user explicitly wants to steer every step interactively.

## Core Loop

The skill keeps two axes alive at once:

```text
alive enough to continue
honest enough to trust
wide enough to discover
disciplined enough to finish
```

At a high level:

1. Frame the discovery target.
2. Discover the perspective space.
3. Run grounded interrogation for each perspective.
4. Run the Discovery Tail Pass against the first coherent synthesis.
5. Mine gaps, contradictions, and hypotheses.
6. Reconcile results into one `backbone.md`.
7. Write, build, or decide from the backbone rather than raw agent output.

## State Files

A typical Daybreak run writes inspectable state:

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

These files are not decorative. They are the memory, evidence trail, recovery
surface, and completion check for the run.

## Installation / Adaptation

### Codex

Copy this folder into your Codex skills directory:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.codex\skills\daybreak-autoresearch"
Copy-Item -Force ".\SKILL.md" "$env:USERPROFILE\.codex\skills\daybreak-autoresearch\SKILL.md"
Copy-Item -Recurse -Force ".\agents" "$env:USERPROFILE\.codex\skills\daybreak-autoresearch\agents"
```

On Unix-like systems:

```bash
mkdir -p ~/.codex/skills/daybreak-autoresearch
cp SKILL.md ~/.codex/skills/daybreak-autoresearch/SKILL.md
cp -R agents ~/.codex/skills/daybreak-autoresearch/agents
```

Then invoke it explicitly:

```text
Use $daybreak-autoresearch to run a grounded autonomous research pass on ...
```

Or let another routing skill call it when a task becomes research-grade,
multi-iteration, source-backed, or not obvious enough for a local answer.

### Other Agent Environments

Use `SKILL.md` as the reference protocol. Port the state folder shape, source
and claim ledgers, perspective coverage, Discovery Tail Pass, watchdog behavior,
and sufficiency checks into the target agent framework. The exact tool names can
change; the contracts should not.

## Suggested Prompt

```text
Use $daybreak-autoresearch to investigate [topic]. Keep state in a task folder,
cover multiple perspectives, ground nontrivial claims in sources or artifacts,
mine gaps and contradictions, and finish only when the backbone is sufficient.
```

## Limits

Daybreak AutoResearch is a protocol, not a guarantee of truth.

- A live heartbeat does not prove research quality.
- Simulated review does not replace external evaluation.
- Novelty pressure can create fiction if ungrounded.
- Retrieval bounds truth; missing corpora must be reported.
- Tail-search candidates are not discoveries until grounded, compressed, and
  attacked by a real check.
- Codex-compatible packaging does not make the protocol Codex-only.
- Deli AutoResearch was publicly announced as open source, but the exact
  upstream license type should still be checked if it is later published.

## Repository Layout

```text
.
|-- SKILL.md
|-- agents/
|   `-- openai.yaml
|-- README.md
|-- ATTRIBUTION.md
|-- MODIFICATIONS.md
|-- LICENSE
|-- NOTICE
|-- CITATION.cff
`-- .gitignore
```

## Citation Request

If Daybreak AutoResearch helps your work, research, product, repo, article, or
agent framework, please cite the repository and keep the upstream lineage
visible. This is not only formal credit; it helps people trace where the ideas
came from and how the protocol evolved.

Please credit:

- the original **Deli AutoResearch / Deli_AutoResearch** framework by Deli Chen
  (DeepSeek);
- the STORM research line when referring to STORM-style multi-perspective
  question asking;
- this **Daybreak AutoResearch** adaptation when using its grounding spine,
  backbone synthesis, sufficiency checks, or Discovery Tail Pass.

Suggested text:

```text
Based on Deli AutoResearch / Deli_AutoResearch by Deli Chen (DeepSeek):
https://victorchen96.github.io/auto_research/framework.html

Daybreak AutoResearch adaptation: adds STORM-style multi-perspective grounding,
Discovery Tail Pass, evidence ledgers, backbone synthesis, and sufficiency-based
completion. Released under Apache-2.0 for the Daybreak adaptation and original
additions in this repository.
```

Also credit STORM when referring to the STORM-style research engine:

- Stanford OVAL STORM project: <https://storm-project.stanford.edu/research/storm/>
- STORM code: <https://github.com/stanford-oval/storm>
- Paper: <https://arxiv.org/abs/2402.14207>

This repository also includes [CITATION.cff](CITATION.cff), so GitHub can show a
"Cite this repository" option after publication.
