# Attribution

Daybreak AutoResearch is an adapted skill derived from the original
**Deli AutoResearch** / `Deli_AutoResearch` framework.

This repository packages Daybreak for Codex, but the operating model is
agent-agnostic. Other agent systems may adapt the protocol if they preserve
lineage, licensing notices, and the grounding / verification contracts that make
the method trustworthy.

## Original Framework

Original creator credited for this repository:

- **Deli Chen (DeepSeek)**
- X profile: <https://x.com/victor207755822>
- Original framework page:
  <https://victorchen96.github.io/auto_research/framework.html>

The original framework page presents `Deli_AutoResearch` as a protocol for
long-horizon autonomous tasks with a self-contained Markdown skill, state files,
stall detection, heartbeat watchdogs, guardian / worker separation, and
multi-agent patterns.

## STORM Influence

This adaptation uses a STORM-style discovery layer inspired by Stanford OVAL's
STORM work:

- Research project page:
  <https://storm-project.stanford.edu/research/storm/>
- GitHub repository:
  <https://github.com/stanford-oval/storm>
- Paper:
  <https://arxiv.org/abs/2402.14207>

In this repository, "STORM-style" means structured multi-perspective question
asking, retrieval-grounded answers, follow-up interrogation, and synthesis into
one coherent backbone. This repository is not affiliated with Stanford OVAL.

## Daybreak Adaptation

The Daybreak adaptation modifies the original Deli AutoResearch direction by
adding:

- mandatory perspective coverage for research-grade work;
- `perspectives.json`;
- `sources.jsonl` and `claims.jsonl`;
- `gaps.jsonl`, `contradictions.jsonl`, and `hypotheses.jsonl`;
- source-quality and claim-confidence labels;
- persistent `backbone.md` synthesis;
- the Discovery Tail Pass;
- clearer separation between perspective coverage and physical subagents;
- sufficiency-based completion checks.

## License Note

The Daybreak AutoResearch adaptation and original additions in this repository
are released under the Apache License, Version 2.0.

Deli AutoResearch was publicly announced as open source by its creator, but at
packaging time no explicit upstream license type was located in the original
framework page or visible repository paths. If an upstream license is later
published, review this repository for compatibility.

If you publish a derivative, keep this attribution file, preserve the original
links, and include the repository `NOTICE` file.
