# Research: audit-repo / agent-driven DL readiness

Date: 2026-11-17  
Question: Do public Agent Skills / ML tooling already cover “audit this repo for agent-driven deep learning readiness” (feedback loops, fast iteration, SAFE use: GPU spend caps, job storms, unbounded objective loops)?

## Verdict

**Yes — a new spine verb (`audit-repo` or similar) is justified for ml-wisdom-skills.**

No surveyed pack delivers a **consumer-repo readiness audit** aimed at *agent-driven DL iteration*: feedback wiring present, metrics readable without drowning context, experiment log discipline, mutable-surface / evaluator immutability, GPU/job storm caps, and skill-router presence — without also *starting* loops or submitting jobs.

Closest pieces exist in fragments (env probe, cost estimate, production readiness, scientific-compute preflight, AGENTS.md hygiene). ml-wisdom already owns the *setup* half (`wire-feedback`) and the *run* half (`run-objective-loop`); it lacks a **read-only go/no-go** that answers “is this repo safe and ready for agents to iterate?” before either.

## Existing near-neighbors (what NOT to rebuild)

| Name | Repo / path | What it audits / does | Gap vs agent-DL workflow readiness |
|------|-------------|------------------------|-----------------------------------|
| `wire-feedback` | local `skills/wire-feedback/` | Inventories feedback sources; **creates** `AGENTS-FEEDBACK.md` + scaffolds `EXPERIMENTS.md` | Creator/setup, not a pass/fail readiness report; does not cover GPU caps, job storms, mutable surface, or “don’t start loops yet” severity |
| `audit-data` | local `skills/audit-data/` | Dataset go/no-go (leakage, labels, tensors) | Data quality only, not repo/agent contract |
| HF Skills catalog | [huggingface/skills](https://github.com/huggingface/skills/) | Hub CLI, datasets, TRL/Jobs training, Trackio, Gradio, local models, etc. | Tool/platform how-tos; `estimate_cost.py` + timeouts advise spend but skill **submits** jobs; no consumer-repo readiness audit |
| `experimental-design` | [K-Dense-AI/scientific-agent-skills](https://github.com/K-Dense-AI/scientific-agent-skills) | Classical DOE / randomization / blocking before data collection | Not DL training loops; not coding-agent repo contract |
| K-Dense (other) | same | EDA, hypothesis, peer review, ISO “standards readiness,” Modal cloud | Domain science / compliance evidence prep — not agent-DL safety or feedback maps |
| `ml-env-probe`, `ml-vram-estimator` | [qhjqhj00/Auto-MLE-skills](https://github.com/qhjqhj00/Auto-MLE-skills) | Machine HW/SW capability JSON; fits/tight/OOM VRAM verdicts | Host readiness, not *this repo’s* feedback/loop/safety contract |
| param087 catalog (15 skills) | [param087/agent-ml-skills](https://github.com/param087/agent-ml-skills) | sklearn/pandas/PyTorch loop patterns, EDA, RAG, serving, ml-debugging | Classical/prod ML judgment snippets; no repo audit verb; no objective-loop safety |
| Orchestra `autoresearch` | [Orchestra-Research/AI-Research-SKILLs](https://github.com/Orchestra-Research/AI-Research-SKILLs) `0-autoresearch-skill/` | Two-loop autonomous research; routes to tool skills; `/loop` + cron continuity | **Opposite** of a readiness gate: “never stop,” “use whatever compute,” continuous wall-clock loops |
| Orchestra MLOps/infra | same | W&B/MLflow/TB, Modal/Lambda/SkyPilot, distributed stacks | Tracker/infra encyclopedias, not consumer readiness checklist |
| `alchemist-playbook` | [voidful/AlchemistPlaybook](https://github.com/voidful/AlchemistPlaybook) | Evidence-based recipe/Hparam diagnosis; FLOPs/`estimate.py`; experiment dashboard template | Recipe advice after you have a run; not “is repo agent-ready?” |
| Ultralytics skills | [ultralytics/skills](https://github.com/ultralytics/skills) | YOLO lifecycle (models/datasets/train/tune/export) + Platform CLI | Product-specific tooling; Platform billing awareness ≠ generic agent-DL repo audit |
| `deployment-readiness-for-ml`, `ml-governance-and-production-readiness`, `mlops-opportunity-framing` | [deepak-karkala/production-mlops-skills](https://github.com/deepak-karkala/production-mlops-skills) | Serving rollout, model cards, business→model framing, `.agentic/config.yml` setup | **Production/serving** readiness, not overnight train/eval agent loops |
| `ml-cost-optimization` | [timwukp/MLOps-agent-skills](https://github.com/timwukp/MLOps-agent-skills) | GPU pricing, FinOps, spot, quantization | Infra cost encyclopedia; no repo feedback/loop contract |
| `compute-budget-planner` | [a-green-hand-jack/ml-research-skills](https://github.com/a-green-hand-jack/ml-research-skills) (feed mirror) | Per-plan GPU-hours, smoke ≤5%, ablation matrix costing; **does not submit** | Strong pattern for *one experiment plan*; not full repo readiness (feedback map, evaluator immutability, skill router) |
| `plan-research-compute`, `check-formulation-readiness` | [ProfesseurHaipeng/ai-research-preflight](https://github.com/ProfesseurHaipeng/ai-research-preflight) | Deterministic preflight of **sanitized JSON manifests** (DAG, envelopes, hard limits); formulation gates | Closest *shape* (audit ≠ execute); different domain (scientific compute / formulation); requires manifests — does not scan a random PyTorch repo for AGENTS-FEEDBACK / train entrypoints |
| Karpathy `program.md` | [karpathy/autoresearch](https://github.com/karpathy/autoresearch) | Fixed contract: edit only `train.py`, immutable `prepare.py`/eval, 5‑min budget, `results.tsv`, grep metrics from `run.log`, dedicated branch | **Assumes** a tiny, already-ready repo; is the *runtime contract*, not a readiness auditor for arbitrary consumer repos |
| getsentry `agents-md` | [getsentry/skills `agents-md`](https://github.com/getsentry/skills/blob/main/skills/agents-md/SKILL.md) | Concise AGENTS.md (commands, paths, &lt;60 lines) | General coding-agent hygiene; no ML feedback/loop/GPU storm checks |
| agentskills.io | [agentskills.io](https://agentskills.io/specification), [best practices](https://agentskills.io/skill-creation/best-practices) | Skill format, progressive disclosure, description triggers | Meta for *authoring* skills, not auditing consumer ML repos |
| `fai-agent-governance` | [frootai/frootai](https://github.com/frootai/frootai) | Agent token/iteration/spend caps, approval lists | LLM-agent ops governance, not DL training job storms |
| automated-researcher plugins | [antondelafuente/automated-researcher](https://github.com/antondelafuente/automated-researcher) | GPU pod deploy/teardown, verify-claims gates, experiment lifecycle | Strong *lab* compute-safety; proprietary scaffold — not a portable judgment skill for arbitrary repos |
| local research note | `docs/research/2026-11-17-loss-arch-dynamics-skills.md` | Proposes `design-loss` / `choose-architecture` / thin `tune-dynamics` | Adjacent spine growth; does not cover repo readiness |

## What “ready for agent-driven DL” should mean

Checklist categories (audit report sections):

1. **Feedback wiring** — Runnable primary + ≥1 guardrail; holdouts named; trackers registered or honestly “none”; `AGENTS-FEEDBACK.md` short/config-only (or explicit missing).
2. **Experiment log** — Append-only path (`EXPERIMENTS.md` or equivalent); chat not the only memory; no narrative dump into the feedback router.
3. **Iteration speed / context hygiene** — Train/eval commands redirect to logs; metrics extractable via grep/JSON/TSV (Karpathy pattern); no requirement to load TB UI or megabyte stdout into context.
4. **Safety / compute** — Declared per-try budget (wall-clock or steps); kill/over-budget = crash; max concurrent jobs / ban on parallel storms; cloud job submit requires human or hard cap; stop rule for loops (contrast Orchestra/Karpathy “never stop” — consumer default should be **bounded**).
5. **Mutable surface & honesty** — Named editable files; evaluator/metrics/data immutable by default; secrets/raw data out of scope; dedicated branch preference.
6. **Skill / router presence** — Situation→skill map present (or pack installed + documented); clear “do not run `run-objective-loop` until…” blockers listed.
7. **Data / train entrypoints** — Documented train, eval, data-prep commands; smoke path exists or flagged; `audit-data` status known/waived separately.

## Proposed skill shape (if shipping)

### Verb name options

| Option | Pros | Cons |
|--------|------|------|
| **`audit-repo`** | Parallel to `audit-data`; speech-act clear | Slightly generic (any repo?) |
| `audit-agent-readiness` | Precise | Long; less verb-punchy |
| `preflight-loop` | Emphasizes gate before `run-objective-loop` | Collides mentally with HF/Jobs “preflight”; less about full repo |
| `check-loop-readiness` | Explicit | Weaker brand vs existing `audit-*` |

**Recommendation:** `audit-repo` with description keywords: agent-driven DL, objective loop readiness, GPU spend, job storm, feedback map, AGENTS-FEEDBACK.

### Inputs / outputs

- **Inputs:** consumer repo root; optional human intent (“overnight loop” vs “manual design-experiment only”).
- **Output:** Markdown report (and optional short machine block), e.g.:

```text
Repo: …
Intent: overnight objective loop | human-gated experiments only
Overall: READY | READY_WITH_WAIVERS | NOT_READY

## Findings
| Category | Severity | Finding | Evidence path/cmd | Remediation skill |
...

## Blockers (must fix or waive before run-objective-loop)
...

## Recommended next skills
wire-feedback | audit-data | verify-training | …
```

### Severity levels

| Level | Meaning |
|-------|---------|
| **warn** | Proceed with eyes open; human should acknowledge (default “hard” level — **do not hard-block**) |
| **info** | Nice-to-have / compose with tool packs |
| **pass** | Category satisfied |

Overall: `READY` / `READY_WITH_WARNINGS` / `NEEDS_SETUP`. This is a **readiness + recommendation** skill, not a gate that forbids work. Prefer warnings and next-skill pointers over `blocker` language.

### Probes (allowed when safe)

Audit may run **cheap probes** when they are safe and informative (e.g. import check, one-batch CPU dry-run, metric file parse, tiny local eval). Prefer **local** probes when the repo supports them (faster feedback).

**Remote / expensive compute is allowed only when:**

1. That is the repo’s normal convention (documented in `AGENTS-FEEDBACK.md` or equivalent), **or**
2. The human has granted permission for this audit session.

Record the permission (or “repo convention: remote-only”) in the report and, when setup runs, in the feedback config (e.g. `compute.default = local|remote`, `remote.submit_requires = ask|allowed`). Never surprise-submit cloud/GPU jobs.

### Relationship to `wire-feedback`

| | `audit-repo` | `wire-feedback` |
|--|--------------|-----------------|
| Mode | **Audit + optional safe probes** | **Creator / setup** |
| Writes | Readiness report (prefer not to bloat `AGENTS-FEEDBACK.md`); may record compute permission/convention notes | Writes `AGENTS-FEEDBACK.md`, scaffolds `EXPERIMENTS.md`; **ask compute preference** (local-first vs remote-convention) |
| When | First touch; before loops; after major repo change | When wiring missing/stale; after audit says “feedback missing” |
| Hand-off | “Missing map → run `wire-feedback`” | “Wiring ready → `design-experiment` / `verify-training` / `run-objective-loop`” |

Do **not** fold audit into `wire-feedback`: agents need a cheap readiness pass that does not own the full wiring write.

### What it must NOT do

- Surprise-submit HF Jobs / SLURM / cloud GPU pods without recorded permission or documented repo convention
- Start `run-objective-loop` or overnight “never stop” autonomy as part of the audit
- Install packages or rewrite training code “to help”
- Replace `ml-env-probe` / VRAM estimators (point at them; may invoke as compose)
- Duplicate production serving readiness (`deployment-readiness-for-ml`)
- Claim cost/compliance/safety guarantees (compose with preflight/FinOps skills for numbers)
- **Hard-block** the human/agent from continuing — warn and recommend instead

## Local pack overlap (already owned)

From `docs/DESIGN.md`, ADRs 0003/0006/0008/0009, templates:

- Judgment layer + verb spine already defined; `wire-feedback` + `run-objective-loop` encode Karpathy-style contract (`program.md`, `results.tsv`, multi-signal keep, evaluator immutability).
- Safety rules live *inside* `run-objective-loop` §7 — but only after the loop starts. **Pre-loop repo audit is the missing gate.**
- Loss/arch research note proposes more spine verbs; `audit-repo` is orthogonal (process readiness vs model design).

## Karpathy autoresearch — assumed contract (implication)

Primary: https://github.com/karpathy/autoresearch/blob/master/program.md

Assumes: tiny editable surface (`train.py` only); immutable eval in `prepare.py`; fixed 5‑min budget; redirect logs + `grep` metrics; untracked `results.tsv`; dedicated branch; timeout kill. Consumer repos rarely have this contract *pre-declared* — that is exactly what readiness audit should detect (present / partial / missing).

## Locked decisions (2026-11-17)

From product discussion after the survey:

1. **Probes OK** when safe; prefer local for speed when possible.
2. **Not local-only** — remote is fine if that is how the repo works.
3. **Remote submit = explicit** — clear to the human; record as permission for this session **or** as repo convention in setup/`AGENTS-FEEDBACK`.
4. **`wire-feedback` asks** compute preference (local-first vs remote-convention) when wiring.
5. **Warn, don’t block** — readiness recommendations, not a hard stop on other work.
6. **Name leaning:** `audit-repo` (still confirm at implement time).

## Still open

1. **Default loop posture:** Consumer default = bounded stop even though Karpathy/Orchestra say “never stop”?
2. **Write policy:** Chat-only report vs also `AGENT-READINESS.md` (avoid bloating `AGENTS-FEEDBACK.md`)?
3. **Catalog pressure:** Ship `audit-repo` before or after `design-loss` / `choose-architecture`?

## Sources checked (paths / URLs)

### Local

- `/Users/work/Data Science/ml-wisdom-skills/docs/DESIGN.md`
- `/Users/work/Data Science/ml-wisdom-skills/AGENTS.md`
- `/Users/work/Data Science/ml-wisdom-skills/skills/wire-feedback/SKILL.md`
- `/Users/work/Data Science/ml-wisdom-skills/skills/wire-feedback/references/inventory-checklist.md`
- `/Users/work/Data Science/ml-wisdom-skills/skills/run-objective-loop/SKILL.md`
- `/Users/work/Data Science/ml-wisdom-skills/skills/audit-data/SKILL.md`
- `/Users/work/Data Science/ml-wisdom-skills/templates/{AGENTS-FEEDBACK,program,EXPERIMENTS,results}.md` (as present)
- `/Users/work/Data Science/ml-wisdom-skills/docs/adr/0003-feedback-setup-first-class.md`, `0008-verb-shaped-spine.md`
- `/Users/work/Data Science/ml-wisdom-skills/docs/research/2026-11-17-loss-arch-dynamics-skills.md`
- `/Users/work/Data Science/ml-wisdom-skills/references/{sources,things-to-watch}.md`

### Remote primary / first-party

- https://github.com/huggingface/skills/
- https://huggingface.co/docs/hub/agents-skills
- https://github.com/huggingface/skills/blob/main/skills/huggingface-llm-trainer/SKILL.md (+ estimate_cost / hardware_guide)
- https://huggingface.co/docs/hub/jobs-pricing
- https://github.com/K-Dense-AI/scientific-agent-skills (+ `docs/skills.md`, `experimental-design`)
- https://github.com/qhjqhj00/Auto-MLE-skills
- https://github.com/param087/agent-ml-skills
- https://github.com/Orchestra-Research/AI-Research-SKILLs (+ `0-autoresearch-skill/SKILL.md`)
- https://github.com/voidful/AlchemistPlaybook
- https://github.com/ultralytics/skills
- https://github.com/deepak-karkala/production-mlops-skills
- https://github.com/timwukp/MLOps-agent-skills (`ml-cost-optimization`)
- https://github.com/a-green-hand-jack/ml-research-skills (compute-budget-planner via public feed mirror)
- https://github.com/ProfesseurHaipeng/ai-research-preflight
- https://github.com/karpathy/autoresearch (+ raw `program.md`)
- https://agentskills.io/specification
- https://agentskills.io/skill-creation/best-practices
- https://github.com/getsentry/skills/blob/main/skills/agents-md/SKILL.md
- https://github.com/antondelafuente/automated-researcher
- https://github.com/frootai/frootai (fai-agent-governance)
