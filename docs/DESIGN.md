# Design — ml-wisdom-skills v1

## Problem

Coding agents can write a PyTorch loop. They struggle to design valid experiments, establish baselines, debug training, interpret results, and decide what to try next. Existing skill packs are dense on **tool/platform how-tos** (Hub, YOLO, package docs). This repo owns the **judgment layer**.

## Goals

1. Encode enduring researcher/engineer judgment (Karpathy recipe, CS231n, FSDL troubleshooting, autoresearch ratchet mechanics).
2. Stay framework-agnostic and portable across consumer repos.
3. Challenge weak process without trapping progress (gates + waivers).
4. Support bounded **objective loops** with multi-signal keep rules and a durable feedback map.

## Non-goals

- Replacing Hugging Face / Ultralytics / TAO / K-Dense tool skills
- Autonomous unbounded “scientist” agents without scope
- Geo/EO overlays in v1
- Shipping large executable training frameworks

## Architecture

```
spine (cross-cutting)          overlays
─────────────────────          ────────
feedback-setup                 cv-data-quality
data-judgment                  cv-augmentation
experiment-discipline          cv-transfer
train-verify                   cv-error-analysis
train-diagnose
evaluate
objective-loop
```

Consumer artifacts:

| Artifact | Role |
|----------|------|
| `AGENTS-FEEDBACK.md` | Durable wiring + Try log + proposals |
| `program.md` | One objective-loop contract + mutable surface |
| `results.tsv` | Agent-readable ledger + reasoning trace fields |

## Principles

1. **Verification before complexity** — overfit tiny batch, loss@init, baselines before architecture search.
2. **One change at a time** — hypothesis → experiment → keep/discard.
3. **Multi-signal judgment** — primary metric + guardrails (see `references/things-to-watch.md`).
4. **Compose, don’t compete** — point at tool packs for CLI/Hub work.
5. **Cite sources; don’t duplicate encyclopedias** — progressive disclosure + external links.

## Distribution

Public GitHub repo; install via `npx skills add MatthewJWhittle/ml-wisdom-skills`.
