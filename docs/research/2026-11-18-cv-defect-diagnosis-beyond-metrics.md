# Research: CV defect diagnosis beyond aggregate metrics

Date: 2026-11-18  
Question: How should Agent Skills for computer vision help diagnose cases where **aggregate metrics look good but the model is bad for its intended use** — and how does that wisdom generalize across the ml-wisdom-skills judgment stack?

## Verdict

Treat this as a **metric–utility gap** (Goodhart + spatial composition): the keep signal must be defined by the decision the product makes, not by the leaderboard default. Overlap metrics (Dice/IoU) and per-tile scores systematically under-penalize soft boundaries and mosaic seams; catching that is not a new encyclopedia skill — it is **guardrail design + visualization + deployment-geometry eval**, owned mainly by `validate-results` / `cv-error-analysis` / `wire-feedback`, with geo mosaic checks as a thin future overlay rather than a tool dump. If primary Dice/mAP rises while boundary, stitch, or intended-use rubrics fail, the loop must **discard**.

## The generalized problem

**Name:** metric–utility gap (Goodhart on CV metrics) **compounded by spatial composition** (the unit you score is not the unit you ship).

Two orthogonal failures:

1. **Proxy mismatch** — the scalar you optimize/report (Dice, mAP@0.5, accuracy) is insensitive to the error class that kills utility (boundary fidelity, small objects, calibrated threshold, class taxonomy, mosaic consistency).
2. **Composition mismatch** — eval units (patches, images, crops) look fine, but the composed artifact (mosaic, video track, multi-view product) fails.

Karpathy’s recipe already encodes the practitioner response: neural nets fail silently; obsess over visualizations; visualize tensors immediately before `model(x)`; watch **prediction dynamics** on a fixed batch; add significant digits on a full eval; compare to a human baseline ([Karpathy 2019](https://karpathy.github.io/2019/04/25/recipe/)). FSDL L10 adds: evaluate on **all metrics, datasets, and slices you care about**, not only the validation score ([FSDL L10](https://fullstackdeeplearning.com/spring2021/lecture-10/)). The pack already names multi-signal judgment (`docs/DESIGN.md`, `validate-results`, `run-objective-loop` keep rule); this note sharpens **which** CV failures look like “good metrics.”

## Failure mode catalog

| Failure mode | Why metric can look good | What to inspect | Typical domain |
|--------------|--------------------------|-----------------|----------------|
| **Dice/IoU vs boundary utility** | Overlap counts interior mass; boundary pixels grow ~linearly, interior ~quadratically → soft/blobby edges barely move Dice while use case needs crisp contours ([Boundary IoU](https://openaccess.thecvf.com/content/CVPR2021/papers/Cheng_Boundary_IoU_Improving_Object-Centric_Image_Segmentation_Evaluation_CVPR_2021_paper.pdf); Taha & Hanbury: prefer distance/contour metrics when contour matters ([BMC Med Imaging 2015](https://link.springer.com/article/10.1186/s12880-015-0068-x))) | Overlay GT vs pred; Hausdorff / HD95 / Surface Dice / Boundary F / Boundary IoU (MONAI: `HausdorffDistanceMetric`, `SurfaceDistanceMetric`, `SurfaceDiceMetric` — [docs](https://docs.monai.io/en/stable/metrics.html)); trimap/band around contour | Medical seg, cadastral/building footprints, surgical planning |
| **Soft Dice / soft masks at deploy** | Soft Dice trains on continuous probs; reported hard Dice after argmax can still look strong while edges stay fuzzy or threshold is uncalibrated | Probability heatmaps + hard masks at **deploy threshold**; entropy along boundaries; “would a human accept this edge?” | Seg with Dice loss (user’s case) |
| **Tile OK, mosaic bad** | Per-patch Dice/accuracy ignores translational variance + seam disagreements (crop vs grassland across adjacent tiles) ([Huang et al. 2018](https://arxiv.org/abs/1805.12219); torchgeo: stride & overlap to reduce stitching artifacts — [GriddedPatchSampler](https://docs.torchgeo.org/en/stable/api/samplers/GriddedPatchSampler.html)) | Stitched mosaic viz; disagreement map on overlaps; metrics on **full mosaic** not only chips; seam strip stats | Geo/EO land cover, any sliding-window dense pred |
| **Accuracy under imbalance** | Majority class dominates; rare class can be near-random while accuracy looks high | Confusion matrix; per-class recall/PR; macro-F1; cost-weighted error | Classification, rare-defect industrial CV |
| **mAP vs deployment threshold** | COCO AP averages many IoUs/recalls/maxDets; product uses one score threshold + NMS config; AP↑ can hide deploy-PR collapse ([COCO detection eval](https://cocodataset.org/dataset/detection-eval.htm)) | PR at **operating point**; AP@0.75 vs @0.5; COCO `analyze()` FP breakdown (Loc / Sim / Oth / BG / FN) | Detection, tracking seeds |
| **Small-object blindness** | Global mAP diluted; COCO explicitly reports AP_small (area < 32²) because ~41% of objects are small ([COCO](https://cocodataset.org/dataset/detection-eval.htm)) | Size-stratified AP/IoU; grids of smallest quartile | Aerial detection, retail SKU, medical lesions |
| **Label taxonomy / soft-label mismatch** | Metric treats GT as gold; human disagreement or coarse taxonomy makes high score meaningless or unachievable | Double-annotation agreement; confusion pairs humans also mix; “intended-use” rubric, not only IoU | Fine-grained classes, EO LC schemes, clinical grades |
| **Leakage inflating val** | Near-dups / same scene / same patient across splits → metric tracks memorization | Near-dup search across splits; scene/patient IDs; train-vs-val identity grids (`cv-data-quality`) | All CV; video frames especially |
| **Train/serve preprocess skew** | Val uses clean tensors; serve crops/norm/resize differ → metrics on wrong distribution | Visualize **serve path** tensors; side-by-side train-eval-serve | Mobile/edge, tiled EO pipelines |
| **Background-dominated Dice** | Including background channel swamps small foreground; MONAI warns small non-background segs can be overwhelmed ([DiceMetric](https://docs.monai.io/en/stable/metrics.html)) | `include_background=False`; per-class Dice; ignore empty | Multi-class medical / EO seg |

User motivating examples map cleanly: (1)+(soft masks) → row 1–2; (2) visualize vs intended use → wisdom section + FSDL/Karpathy; (3) stitch → row 3 + Geo section.

## Wisdom: how practitioners catch this

Enduring checklist (judgment, not tooling):

1. **Name the decision, then the metric** — If the action is “delineate organ for dose” or “map field boundary for subsidy,” primary cannot be mean Dice alone. Put overlap as reported; put boundary / mosaic / cost-at-threshold as primary or hard guardrail (`validate-results` already: decision-matched metrics).
2. **Always visualize predictions against use** — Fixed-size grids: image + GT + pred (+ confidence). Karpathy: visualize before the net and **prediction dynamics** on a fixed batch ([recipe](https://karpathy.github.io/2019/04/25/recipe/)). Pack already: `cv-data-quality` visualization checklist; `cv-error-analysis` FP/FN/hard-TP grids ([`skills/cv-error-analysis/references/error-grids.md`](../../skills/cv-error-analysis/references/error-grids.md)).
3. **Slice metrics that match failure physics** — Size, class, sensor, site, edge-band IoU, AP_small. Global win + critical slice fail = guardrail veto (`validate-results`, FSDL L10 slices).
4. **Add boundary-sensitive numbers when boundaries matter** — Hausdorff/HD95, average surface distance, Normalized Surface Dice (MONAI), Boundary F (Csurka et al. BMVC 2013 contour matching for semantic seg), Boundary IoU / Boundary AP (Cheng et al. CVPR 2021). Do not replace Dice; **pair** them (Taha & Hanbury: combine metrics when objectives conflict).
5. **Eval at composition scale** — For tiled EO: score mosaics / AOIs, not only chips. Visual seam boards across tile borders. Overlap disagreement rate as a guardrail.
6. **Holdout that matches deployment geometry** — Same tile size, stride, norm, CRS, and stitch policy as serve. “Chip val” that never mosaics is a known lie.
7. **Human intended-use rubric** — Cheap: 20–50 cases scored pass/fail by domain rule (e.g. “boundary within 2 px of fence,” “no class flip across contiguous parcel”). Karpathy human baseline; FSDL behavioral/robustness tests.
8. **Cheap probes before architecture thrash** — Swap soft→hard threshold; recompute Dice in a 5-px boundary band; mosaic two adjacent tiles and eyeball; check `include_background`; compare AP@0.5 vs operating-point PR. Attribute via `cv-error-analysis` table (data / model / aug / threshold) before `run-objective-loop`.

## Geo/EO stitching specifics

Primary facts to encode as judgment (not a torchgeo tutorial):

- **Why seams exist:** modern SNs are not perfectly translationally equivariant; label for a pixel depends on its position in the patch (non-unary strides, zero-padding). Huang et al. survey stitch variants: overlap+average, edge clip+concat, naive concat ([arXiv:1805.12219](https://arxiv.org/abs/1805.12219)).
- **Library practice:** torchgeo documents gridded sampling with `stride` slightly smaller than `size` so overlap ≈ receptive field, explicitly “to prevent stitching artifacts” (citing Huang et al.) — [GriddedPatchSampler](https://docs.torchgeo.org/en/stable/api/samplers/GriddedPatchSampler.html). Full stitch/blend APIs are still evolving (torchgeo discussions / TerraTorch blend masks); **skills should require mosaic-level feedback sources**, not re-implement fold/unfold.
- **What to demand in eval:**
  - Overlap or edge-clip policy stated and matched train↔serve.
  - Consistent radiometric norm **across** tiles (per-tile z-score can invent seams).
  - Prefer larger inference windows when memory allows (Huang recommendation family: reduce padding-dominated edges).
  - Weighted blend in overlaps is common engineering; still **verify** with mosaic viz — averaging does not fix systematic class flips from missing context.
  - Contextual / larger-FOV models vs tiny per-tile CNNs: if adjacent tiles disagree on land cover that is spatially continuous, the failure is often **insufficient context or inconsistent preprocess**, not “need more Dice.”
- **Spatial autocorrelation:** adjacent chips are not i.i.d.; splitting by random chips leaks geography. Prefer scene/AOI/block holdouts (already in `things-to-watch` leakage spirit + `cv-data-quality` near-dups).

ADR 0002 defers geo overlays in v1 — correct for catalog sharpness. Wisdom still belongs in spine CV overlays as **optional checks** when deployment is tiled; a future `cv-geo-tiling` (name TBD) should stay thin: geometry of eval, seam guardrails, pointer to torchgeo/rasterio — not sliding-window code.

## Implications for ml-wisdom-skills stack

### Ownership map (opinionated)

| Concern | Owns | Refine later (do not implement in this note) |
|---------|------|-----------------------------------------------|
| Primary vs guardrail; Goodhart; decision-matched metrics | **`validate-results`** | Explicit CV examples: Dice+boundary pair; mAP vs operating-point; “composition-scale” eval as a guardrail class. Extend [`metric-and-slices.md`](../../skills/validate-results/references/metric-and-slices.md). |
| Vision failure attribution; FP/FN grids; boundary vs instance miss | **`cv-error-analysis`** | Elevate “metrics look good / use looks bad” as a first-class entry: soft edges, seam strips, taxonomy disagreement. Expand [`error-grids.md`](../../skills/cv-error-analysis/references/error-grids.md) with mosaic-adjacent and boundary-band grids. |
| Viz before train; mask/box sanity; leakage | **`cv-data-quality`** | Keep pre-model; add “labels that are coarse for the utility metric” as a data finding. |
| Feedback sources for plots / stitch scripts / holdouts | **`wire-feedback`** | Template rows: `pred-grid`, `boundary-metric`, `mosaic-stitch`, `operating-point-PR`. Inventory checklist already asks for plot scripts ([`inventory-checklist.md`](../../skills/wire-feedback/references/inventory-checklist.md)). |
| Keep iff primary↑ ∧ ¬guardrail | **`run-objective-loop`** | Document example guardrails: HD95↑, seam disagreement↑, critical slice↓. No evaluator edits to chase Dice. |
| Hypothesis / one change when fixing boundary or stitch | **`design-experiment`** | Loss change (Dice→boundary-aware) is **one** lever after diagnosis; cite metric≠loss from prior research note. |
| Loss choice when Dice optimizes mass not edges | **`design-loss`** *(proposed)* | Prior note already: metric≠loss; hand off stubborn boundary gaps to validate/error-analysis ([`2026-11-17-loss-arch-dynamics-skills.md`](./2026-11-17-loss-arch-dynamics-skills.md)). |
| Sick training vs bad utility | **`diagnose-training` / `verify-training`** | Stay out of utility diagnosis if loss@init / overfit-batch fail; if training healthy and Dice fine but edges bad → not a LR bug. |
| Aug creating edge/label bugs | **`cv-augmentation`** | Visualize post-aug masks; don’t “fix” seams with random crop alone. |
| Backbone/finetune when domain gap | **`cv-transfer`** | Only after error analysis attributes representation/domain gap. |
| Scan list | **`references/things-to-watch.md`** | Add bullets: “overlap metric OK, boundaries fail use”; “per-tile OK, mosaic inconsistent”; “mAP OK, deploy threshold PR fails.” |

### Templates

- **`AGENTS-FEEDBACK.md`**: require at least one **visual** guardrail for CV repos; for tiled EO, require a mosaic/stitch command or an explicit gap.
- **`EXPERIMENTS.md`**: interpretation field should answer “did intended-use / viz / composition checks pass?” not only primary scalar.
- **`program.md` / `results.tsv`**: guardrails column must be able to veto a Dice/mAP win.

### Optional future skills (thin)

1. **`cv-geo-tiling`** (or fold into `cv-error-analysis` until demand is proven) — deployment geometry, seam/overlap checks, AOI holdouts. Aligns with ADR 0002 “geo later.”
2. Do **not** add `cv-metrics-encyclopedia` — MONAI/COCO/mmseg own formulas.

### What already almost covers this

- `validate-results` Goodhart rule + slices.
- `cv-error-analysis` “Boundary failures vs missed instances for masks.”
- `things-to-watch`: “Plots/scripts missing for the modality (e.g. CV with numbers only).”
- DESIGN principle: multi-signal judgment.

Gap is **explicit naming** of metric–utility and mosaic composition as veto-class failures, plus wiring expectations for viz/stitch feedback sources.

## What NOT to rebuild

- Ultralytics / HF / FiftyOne / MONAI metric API encyclopedias (compose; cite).
- Full tiling inference frameworks (torchgeo/TerraTorch/rasterio windowed reads).
- Another “list of every segmentation loss.”
- Autonomous scientist that optimizes Dice until HD95 is worse.

## Open questions

1. Should boundary/mosaic checks be **hard gates** in `validate-results` for CV, or always product-specific guardrails listed in `AGENTS-FEEDBACK.md`? (Lean: product-specific, but CV template should **prompt** for them.)
2. When v1 ships without geo overlay, is a single paragraph in `cv-error-analysis` enough for EO consumers, or do we need `cv-geo-tiling` before first EO adopter?
3. Soft Dice vs hard Dice reporting: should `design-loss` mandate stating train objective vs eval binarization contract?
4. How formal should “intended-use rubric” be (checkbox sheet vs free text in EXPERIMENTS)? Prefer a 5–10 line rubric artifact path registered in the feedback map.
5. Interaction with proposed `audit-repo`: readiness fail if CV feedback map has metrics but no pred visualization source?

## Sources

### External (fetched / opened for this note)

- Karpathy, A. (2019). [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/).
- CS231n course notes index / [Neural Networks Part 3](https://cs231n.github.io/neural-networks-3/) (sanity checks, babysitting training, weight viz).
- FSDL. [Lecture 10: Testing & Explainability](https://fullstackdeeplearning.com/spring2021/lecture-10/) (metrics beyond val score; slices).
- FSDL. [Lecture 7: Troubleshooting DNNs](https://fullstackdeeplearning.com/spring2021/lecture-7/).
- MONAI. [Metrics documentation](https://docs.monai.io/en/stable/metrics.html) (`DiceMetric`, `HausdorffDistanceMetric`, `SurfaceDistanceMetric`, `SurfaceDiceMetric`; background overwhelm warning).
- Cheng et al. (2021). [Boundary IoU](https://openaccess.thecvf.com/content/CVPR2021/papers/Cheng_Boundary_IoU_Improving_Object-Centric_Image_Segmentation_Evaluation_CVPR_2021_paper.pdf) (CVPR).
- Csurka et al. (2013). [What is a good evaluation measure for semantic segmentation?](https://www.bmva-archive.org.uk/bmvc/2013/Papers/paper0032/paper0032.pdf) (BMVC; boundary F).
- Taha & Hanbury (2015). [Metrics for evaluating 3D medical image segmentation](https://link.springer.com/article/10.1186/s12880-015-0068-x) (BMC Med Imaging).
- COCO. [Detection Evaluation](https://cocodataset.org/dataset/detection-eval.htm) (AP/AR, size strata, analyze() FP taxonomy).
- Huang et al. (2018). [Tiling and Stitching Segmentation Output for Remote Sensing](https://arxiv.org/abs/1805.12219).
- TorchGeo. [GriddedPatchSampler](https://docs.torchgeo.org/en/stable/api/samplers/GriddedPatchSampler.html) / [samplers API](https://docs.torchgeo.org/en/stable/api/samplers.html) (overlap ≈ receptive field; stitching artifacts).

### Local pack

- [`docs/DESIGN.md`](../DESIGN.md)
- [`docs/adr/0001-judgment-layer-not-tooling.md`](../adr/0001-judgment-layer-not-tooling.md)
- [`docs/adr/0002-spine-overlays-no-geo-v1.md`](../adr/0002-spine-overlays-no-geo-v1.md)
- [`references/things-to-watch.md`](../../references/things-to-watch.md)
- [`references/sources.md`](../../references/sources.md)
- [`skills/validate-results/`](../../skills/validate-results/), [`skills/cv-error-analysis/`](../../skills/cv-error-analysis/), [`skills/cv-data-quality/`](../../skills/cv-data-quality/), [`skills/cv-augmentation/`](../../skills/cv-augmentation/), [`skills/cv-transfer/`](../../skills/cv-transfer/)
- [`skills/wire-feedback/`](../../skills/wire-feedback/), [`skills/run-objective-loop/`](../../skills/run-objective-loop/), [`skills/design-experiment/`](../../skills/design-experiment/), [`skills/verify-training/`](../../skills/verify-training/), [`skills/diagnose-training/`](../../skills/diagnose-training/)
- [`templates/AGENTS-FEEDBACK.md`](../../templates/AGENTS-FEEDBACK.md), [`templates/EXPERIMENTS.md`](../../templates/EXPERIMENTS.md)
- Prior notes: [`2026-11-17-loss-arch-dynamics-skills.md`](./2026-11-17-loss-arch-dynamics-skills.md), [`2026-11-17-audit-repo-readiness.md`](./2026-11-17-audit-repo-readiness.md)
