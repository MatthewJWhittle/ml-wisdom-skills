# ML Wisdom Skills

Domain language for this Agent Skills pack. Default training context is deep learning; classical ML still fits under the same spine where the judgment applies.

## Language

**Judgment skill**:
A portable Agent Skill whose primary job is to change how an agent designs, verifies, diagnoses, and decides during ML work — not how to operate a specific tool or Hub API.
_Avoid_: Tool skill, trainer skill, package skill, tutorial

**Tool skill**:
A skill that mainly documents or drives a concrete product/API/CLI (Hub, YOLO, Lightning Trainer flags, etc.). Out of scope for this repo except as something we compose with.
_Avoid_: Calling our judgment skills "integrations"

**Feedback source**:
A concrete, repo-local way the agent can observe training or model behavior (metric logs, eval scripts, plot generators, dashboards, held-out sets, human review hooks).
_Avoid_: Vague "look at the results", "check TensorBoard" without a path or command

**Feedback map**:
A durable, repo-local markdown guide (working filename `AGENTS-FEEDBACK.md`) listing feedback sources, how to run them, what they inform, and recommended wiring gaps. Created/updated by `feedback-setup`; reread by other skills.
_Avoid_: Generic observability, monitoring setup, stuffing loop goals into this file

**program.md**:
A run-specific objective-loop contract (Karpathy/autoloop convention): goal, allowed mutable surface, primary/guardrail signals, keep/discard rules, results/trace schema. Distinct from the feedback map.
_Avoid_: Putting permanent wiring-only notes only in program.md

**results.tsv**:
Append-only experiment ledger for one objective-loop run (metrics, keep/discard/crash). Usually untracked. Complements, does not replace, project tools like MLflow when those already exist.
_Avoid_: Committing the ledger by default; forcing MLflow replacement

**Try log**:
A durable, cross-session record in the consumer repo of hypotheses tried, outcomes, and high-level decisions — survives beyond a single objective-loop run. Lives with or beside the feedback map, not only inside one `results.tsv`.
_Avoid_: Relying on chat history as the only memory

**Reasoning trace**:
Per-experiment (or per-keep) notes the agent writes: hypothesis, what changed, how signals were interpreted, why keep/discard — so a human can reconstruct how score moved across many runs and code changes.
_Avoid_: Dumping full chain-of-thought into git; opaque metric-only rows

**Mutable surface**:
What the objective loop is allowed to change this run (e.g. hyperparameters only, one training file, recipes, feature code) — and what is forbidden (raw data, untracked artifacts, unrelated packages). Established before the loop starts.
_Avoid_: Implicit “edit anything”; silent scope creep

**Gate**:
A pass/fail verification the agent must satisfy (or the human must explicitly waive) before increasing model/training complexity.
_Avoid_: Soft suggestion, tip, best practice (when a hard stop is meant)

**Challenge stance**:
Skeptical collaborator behavior — baselines, correctness gates, evidence demands — available when useful. Must not trap progress: every hard stop needs a clear pass path or an explicit human waiver.
_Avoid_: Challenge mode (implies a rigid personality switch), critic-only

**Objective loop**:
A bounded iterate-keep-discard cycle: change → run via wired feedback → record → keep or revert — in the spirit of Karpathy autoresearch, but guided by the feedback map’s signals and judgment skills, not a single blind metric.
_Avoid_: Autoresearch (reserve for Karpathy’s project unless we adopt the name), autonomous scientist, unbounded agent

**Primary signal**:
The main keep/discard metric for an objective loop (autoresearch uses one: `val_bpb`). Chosen per project via the feedback map.
_Avoid_: The only thing we look at

**Guardrail signal**:
A secondary observation that can veto or warn even when the primary signal improves (e.g. VRAM blow-up, slice collapse, label leak symptoms, plot sanity).
_Avoid_: Secondary metric (too weak), constraint (too formal)

**Spine skill**:
A cross-cutting judgment skill that applies across ML modalities (data → experiment → train verification → diagnostics → evaluation → next decision).
_Avoid_: Generic skill, core utils

**Overlay skill**:
A modality- or domain-specific judgment skill that adds checks and wisdom on top of the spine (e.g. computer vision, geo/EO).
_Avoid_: Plugin, addon, specialisation pack (unless we later adopt pack as a distribution term)

**Baseline ladder**:
An ordered sequence of deliberately simple reference systems (e.g. chance, majority class, linear probe, small CNN) used as gates before heavier models.
_Avoid_: SOTA comparison, leaderboard chase
