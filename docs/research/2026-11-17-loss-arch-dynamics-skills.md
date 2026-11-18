# Research: loss / architecture / training-dynamics skills

## Verdict (short)

**Add two spine verbs — `design-loss` and `choose-architecture`.** They close real judgment gaps the current pack only grazes (init-loss checks, “don’t be a hero,” transfer overlay). **Do not ship a fat `tune-dynamics` encyclopedia.** Prefer a **thin** `tune-dynamics` (healthy-run knobs *after* correctness) *or*, if catalog pressure is high, fold that judgment into expansions of `design-experiment` + `diagnose-training` first. Stay frameworks + gates; point at HF / Ultralytics / Alchemist / Orchestra for recipes and CLIs.

## Existing coverage gaps in ml-wisdom-skills

Current spine (from `docs/DESIGN.md`, ADR 0008, skill folders): `wire-feedback` → `audit-data` → `design-experiment` → `verify-training` → `diagnose-training` → `validate-results` → `run-objective-loop`, plus CV overlays.

| Topic | What exists today | Gap |
|-------|-------------------|-----|
| **Loss** | `verify-training` gate “loss @ init” + `references/init-loss-notes.md` (CE / BCE / MSE expectations). `diagnose-training` probes “loss finite / scale.” `validate-results` owns *metrics*, not training objectives. | No skill asks: *What should the objective be?* Task↔loss match, logits vs probs, multi-task weights, ignore-index, custom composite vs stock CE, metric–loss mismatch (optimize CE, ship F1). PyTorch documents API; judgment does not. |
| **Architecture** | `design-experiment` baseline ladder (“simple net” → heavier). `cv-transfer` owns freeze/finetune *given* a backbone. Karpathy “don’t be a hero” is cited but not operationalized as a choose/copy skill. | No skill walks modality → boring baseline family → capacity vs compute → when *not* to invent towers. FSDL “start simple” architecture table is absent from a dedicated verb. |
| **Dynamics** | `diagnose-training` trees F (LR high/low), E (param groups / WD-ish), C (NaN). `design-experiment` “when NOT to tune” + one-change. `cv-transfer` head vs backbone LR. | Forward-looking healthy defaults (Adam/SGD, constant LR first, schedule last, batch↔BN, WD placement, early stopping as last-ish) are scattered. No coherent “set dynamics after verify passes” invocation. |

Design intent already forbids tool encyclopedias (`docs/adr/0001-judgment-layer-not-tooling.md`, `docs/DESIGN.md` non-goals). Any new skills must stay that way.

## Proposal A — `design-loss`

### Scope (judgment framework)

A short decision procedure:

1. **Name the decision the model supports** (class index, multi-label, ranking, regression, dense detect, seq CTC, generative NLL) — then pick the *family* of objective, not a brand name.
2. **Match head ↔ loss contract**: logits into CE / BCEWithLogits; log-probs into NLL; never softmax then CE “because tutorials.”
3. **State init expectation** before training (hand off to `verify-training`): e.g. balanced CE ≈ `-log(1/C)`; binary logits ≈ `ln(2)`; regression vs mean predictor. Custom losses require a **human-stated** init target (already the waiver path in `verify-training`).
4. **Class imbalance / ignore / soft labels**: prefer correct reduction + class weights / focal *as a deliberate one-change* after a plain CE baseline works — not day-one heroics.
5. **Metric ≠ loss**: if the ship metric is F1 / IoU / calibration, say so; either align the objective or treat metric gaps as `validate-results` / error-analysis work, not silent loss shopping.
6. **Composite losses**: one term at a time; record weights; verify each term’s scale at init so one head does not dominate.

### Triggers

- “What loss should I use?”
- New task head, multi-task, detection/seg composites, switching CE↔focal, BCE vs CE for multi-label
- Init loss “looks weird” *and* the objective itself is in question (else `verify-training` / `diagnose-training`)

### Gates

- **Pass**: written task→loss contract; head output type named (logits vs probs); init expectation stated or waived; if composite, per-term scales noted.
- **Hard stop**: changing loss *and* architecture *and* LR in one experiment without waiver (`design-experiment`).
- **Hand off**: after design, always run `verify-training` before long trains.

### What NOT to include

- Catalog of every `torch.nn` loss with formulas (PyTorch / package docs own that).
- Focal-γ tables, YOLO loss flag matrices, TRL preference-loss encyclopedias (Ultralytics / Alchemist / Orchestra).
- Implementing autograd for novel losses unless the consumer repo already owns that code.

### Composition

| Skill | Relationship |
|-------|----------------|
| `verify-training` | Proves the *chosen* loss is wired (init, tiny-batch). `design-loss` chooses; verify audits. |
| `validate-results` | Owns eval metrics / Goodhart. If loss and metric diverge, design-loss names the gap; validate owns reporting honesty. |
| `diagnose-training` | Wrong loss *wiring* (logits vs softmax) shows up in probes; wrong *choice* for the task shows up as stubborn underfit after probes pass → return to design-loss. |
| `cv-error-analysis` | Failure modes can motivate a loss change (e.g. hard negatives) — still one change, after analysis. |

## Proposal B — `choose-architecture`

### Scope

Operationalize Karpathy + FSDL + CS231n capacity advice without becoming Model Zoo:

1. **Don’t be a hero**: copy the simplest architecture from the closest working paper / Hub recipe for the *modality*, not a novel Lego stack ([Karpathy recipe](https://karpathy.github.io/2019/04/25/recipe/)).
2. **Modality defaults (start simple)** — FSDL L7: images → LeNet-like then ResNet-class; sequences → shallow LSTM/temporal conv then attention; else small MLP; multi-modal → encode separately → concat → MLP ([FSDL spring 2021 L7](https://fullstackdeeplearning.com/spring2021/lecture-7/)). Update the *examples* over time (ConvNeXt / ViT / small Transformer) but keep the *rule*: boring baseline first.
3. **Capacity**: prefer large-enough nets + regularization over tiny nets out of fear of overfitting ([CS231n neural-networks-1](https://cs231n.github.io/neural-networks-1/)); climb capacity only after underfit is diagnosed (FSDL bias step: make bigger before exotic architecture).
4. **Transfer before scratch** when a pretrained family exists (`cv-transfer` for vision policy; this skill decides *whether* a standard pretrained backbone is the baseline).
5. **Constraints from pretrained weights**: architecture is not free once you transfer ([CS231n transfer learning](https://cs231n.github.io/transfer-learning/)).
6. **Complexity dial**: architecture change is one click; never bundled with LR + aug + loss.

### Triggers

- Greenfield model choice; “should we use X backbone?”; temptation to invent towers; multi-input fusion design; capacity debates before baselines exist.

### Gates

- **Pass**: named baseline family + why; ladder rung (`design-experiment`) stated; pretrained vs scratch decision explicit; compute/VRAM soft budget noted.
- **Fail / challenge**: custom architecture before chance/linear/simple-net baselines; changing stem before `verify-training` on a dumb model.

### What NOT to include

- Weight-name tables (Ultralytics `yolo-models`, HF Hub skills).
- LitGPT / Mamba / TorchTitan how-tos (Orchestra Model Architecture skills).
- Novel block designs, NAS, or paper-chase SOTA stacks as defaults.

### Composition

| Skill | Relationship |
|-------|----------------|
| `design-experiment` | Owns hypothesis / ladder / one-change; `choose-architecture` supplies the architecture *option* under that discipline. |
| `cv-transfer` | Overlay for freeze/LR/norm once a CV backbone is chosen. |
| `verify-training` | Required after any architecture swap. |
| `diagnose-training` | Out of scope for “try a new architecture” until probes clear (already stated in diagnose skill). |
| `run-objective-loop` | Architecture edits allowed only inside the mutable surface (autoresearch-style). |

## Proposal C — `tune-dynamics` vs fold into existing skills

### What “dynamics” means here

Forward-looking training knobs once the stack is *correct*: LR and schedule, optimizer choice, batch size (noise vs BN), weight decay placement, warmup, early stopping, “leave it training,” when to anneal. Sources: Karpathy recipe §§3–6; CS231n neural-networks-3 babysitting / annealing / Adam; FSDL L7 defaults + hyperparameter ranking.

### Option 1 — Expand only (`design-experiment` + `diagnose-training`)

**Pros:** Smaller catalog; ADR 0008 spine stays seven verbs; dynamics already partially live in diagnose tree F and design “when NOT to tune.”  
**Cons:** Agents invoke skills by verb. “Training is sick” ≠ “Set a healthy LR schedule.” Stuffing babysitting into diagnose muddies the challenge stance (stop thrashing). Stuffing defaults into design-experiment dilutes hypothesis/ladder focus. agentskills best practices warn against skills that are too broad *and* against forcing multiple skills to load for one coherent unit ([agentskills.io best practices](https://agentskills.io/skill-creation/best-practices.md)).

### Option 2 — Add thin `tune-dynamics`

**Pros:** Clear speech act after `verify-training` passes: set / revise dynamics *one knob at a time*. Owns enduring rules: Adam `3e-4` as early safe default (Karpathy/FSDL); SGD often wins for ConvNets once tuned; **disable borrowed LR decay until the end** (Karpathy — epoch-based ImageNet schedules silently zero LR); random/coarse-to-fine search over grid (Karpathy + FSDL); early stopping after you can overfit; WD/dropout as regularization dial after capacity exists.  
**Cons:** Overlap risk with AlchemistPlaybook (recipe numbers) and Ultralytics `yolo-tuning` (CLI playbooks) if the skill grows tables of γ, β₂, warmup ratios.

### Recommendation

**Prefer Option 2 with a hard thinness budget** (SKILL.md core &lt; ~150 lines; references only for babysitting cartoons / default table). If the maintainers want zero spine growth beyond A/B, take Option 1 but *split sections explicitly* in those skills (“Healthy dynamics” vs “Sick probes”) so agents still find the material.

**Do not** make `tune-dynamics` a second `diagnose-training`. Contract:

| | `diagnose-training` | `tune-dynamics` | `design-experiment` |
|--|---------------------|-----------------|---------------------|
| When | Loss NaN, cannot overfit, silent bugs | Stack verified; seeking better healthy training | Planning changes / ladder |
| Stance | Stop tuning; probe | One knob; log LR; schedules last | Hypothesis + keep/discard |
| Out of scope | Architecture shopping | Loss redesign; Hub flags | Correctness debugging |

Autoresearch note: Karpathy `program.md` makes architecture *and* optimizer *and* batch fair game inside a fixed-time loop with keep/discard — that is `run-objective-loop` territory. `tune-dynamics` supplies *judgment priors* for what to try; it does not replace the ledger.

## Recommended verb names + catalog order

| Verb | Why this name |
|------|----------------|
| `design-loss` | Speech act for objective *design*, including composites — not `pick-loss` (too shopping) or `loss-functions` (noun encyclopedia). |
| `choose-architecture` | Emphasizes selection/copy over invention; matches “don’t be a hero.” |
| `tune-dynamics` | Forward-looking knob work; parallel to diagnose’s reactive verb. Acceptable deferral name if folded: document as sections, not a skill. |

**Catalog order (spine):**

1. `wire-feedback`  
2. `audit-data`  
3. `design-experiment`  
4. `choose-architecture` ← new (early, with experiment design)  
5. `design-loss` ← new (before verify so init expectations exist)  
6. `verify-training`  
7. `diagnose-training`  
8. `tune-dynamics` ← new *or* deferred fold  
9. `validate-results`  
10. `run-objective-loop`  

CV overlays unchanged: `cv-data-quality`, `cv-augmentation`, `cv-transfer`, `cv-error-analysis`.

Naming stays hyphenated lowercase per [Agent Skills specification](https://agentskills.io/specification.md); descriptions must carry discovery triggers (ADR 0008).

## Ecosystem: do not rebuild

| Ecosystem | Owns | Our boundary |
|-----------|------|--------------|
| [huggingface/skills](https://github.com/huggingface/skills) | Hub, datasets, Trainer/CLI, eval workflows | Compose for *how to run*; we own *whether the experiment is valid* |
| [ultralytics/skills](https://github.com/ultralytics/skills) | YOLO family/size names, train/tune/export CLIs, Platform | No weight tables or `model.tune()` encyclopedias here |
| [K-Dense-AI/scientific-agent-skills](https://github.com/K-Dense-AI/scientific-agent-skills) | Package/domain science skills (incl. Lightning, sklearn, etc.) | Do not duplicate package ops; judgment layer only |
| [Orchestra-Research/AI-Research-SKILLs](https://github.com/Orchestra-Research/AI-Research-SKILLs) | Autoresearch orchestration + architecture *implementations* (LitGPT, Mamba, …), fine-tune stacks, optimization kernels | Point out; do not fork 98 tool skills |
| [voidful/AlchemistPlaybook](https://github.com/voidful/AlchemistPlaybook) | Evidence-labeled LLM/speech training *recipes* (LR ladders, DPO β, NeMo defaults) | Cite/compose for LLM recipe numbers; our dynamics skill stays modality-agnostic principles |
| [Karpathy recipe](https://karpathy.github.io/2019/04/25/recipe/) | Enduring process | Encode principles; **flag dated bits** (below) |
| [karpathy/autoresearch](https://github.com/karpathy/autoresearch) | Fixed-budget keep/discard loop, `program.md`, `results.tsv` | Already mirrored by `run-objective-loop` + templates — do not re-home |

### Dated Karpathy (2019) advice — keep principle, update instance

| Recipe claim | Status |
|--------------|--------|
| Unsupervised pretrain “doesn’t help modern CV” | **Dated** for foundation/SSL backbones; pack already notes this in `design-experiment`, `cv-transfer`, `references/sources.md`. Enduring rule: no *exotic* pretrain before a standard checkpoint + working feedback map. |
| Adam 3e-4 as early default | Still a good *starting* heuristic (also FSDL); not the final answer for large LLM pretrain (defer numbers to Alchemist). |
| Prefer SGD over Adam for finished ConvNets | Directionally still taught; treat as “try after Adam baseline,” not dogma. |
| Constant LR; schedules only at the end | **Enduring** — especially “don’t trust foreign epoch decay.” |
| Don’t be a hero / copy simplest related arch | **Enduring.** |
| Smaller batch ≈ stronger BN regularization | Context-dependent; principle OK, don’t oversell. |
| Winter-break “leave it training” → SOTA | Anecdote; keep “train longer than you think” lightly, not as a gate. |

CS231n notes remain excellent for babysitting curves and capacity/regularization intuition; activation defaults (ReLU) and “FC depth rarely helps” are era-scoped — principles over cited layer counts.

## Sources

Primary sources actually consulted for this note:

- https://karpathy.github.io/2019/04/25/recipe/ — full recipe (data → skeleton → overfit → regularize → tune → squeeze).
- https://github.com/karpathy/autoresearch — README; https://raw.githubusercontent.com/karpathy/autoresearch/master/program.md — mutable `train.py`, fixed time budget, keep/discard, simplicity criterion.
- https://cs231n.github.io/neural-networks-1/ — activations, capacity, prefer large nets + regularization.
- https://cs231n.github.io/neural-networks-3/ — sanity checks, loss babysitting, LR annealing, Adam/RMSprop, hyperparameter search.
- https://cs231n.github.io/transfer-learning/ — four data-size×similarity scenarios; smaller LR on pretrained weights.
- https://fullstackdeeplearning.com/course/2022/lecture-3-troubleshooting-and-testing/ — memorization tests, make-it-run/fast/right, stick to proven architectures.
- https://fullstackdeeplearning.com/spring2021/lecture-7/ — start-simple architectures, Adam 3e-4 defaults, overfit batch, bias–variance next steps, HP tuning methods.
- https://docs.pytorch.org/docs/stable/nn.html#loss-functions — intended canonical index (HTML fetch blocked in this environment); **fallback primary:** https://raw.githubusercontent.com/pytorch/pytorch/main/torch/nn/modules/loss.py — documents API classes (`CrossEntropyLoss`, `BCEWithLogitsLoss`, `MSELoss`, `CTCLoss`, …), shapes, reductions, soft-target caveats — **not** task judgment.
- https://agentskills.io/ — overview; https://agentskills.io/specification.md — name/description/progressive disclosure; https://agentskills.io/skill-creation/best-practices.md — coherent units, omit what the agent knows, progressive disclosure, defaults not menus.
- Local pack: `/Users/work/Data Science/ml-wisdom-skills/docs/DESIGN.md`, `docs/adr/0001-judgment-layer-not-tooling.md`, `docs/adr/0008-verb-shaped-spine.md`, `docs/adr/0002-spine-overlays-no-geo-v1.md`, `README.md`, `references/sources.md`, `references/things-to-watch.md`, and current `skills/*/SKILL.md` (especially `design-experiment`, `verify-training`, `diagnose-training`, `validate-results`, `cv-transfer`).
- Ecosystem READMEs/pages: huggingface/skills, ultralytics/skills, K-Dense-AI/scientific-agent-skills, Orchestra-Research/AI-Research-SKILLs, voidful/AlchemistPlaybook (via fetch / search of first-party repo pages).
