# Locked defaults for v1

Round-6 decisions locked for the first public cut:

1. **Try log** lives in `AGENTS-FEEDBACK.md` (section); run-objective-loop appends a condensed entry when a loop ends. Dense per-run rows stay in `results.tsv`.
2. **Reasoning trace** is first-class: each `results.tsv` row includes hypothesis, change, interpretation, and keep/discard rationale.
3. **Scope interview** before any objective loop: mutable surface, hard bans, work on a branch, ask before destructive or out-of-scope edits.
4. **`results.tsv` is the universal agent ledger**; mirror to MLflow/W&B/etc. when the feedback map says a tracker exists.
5. **Skill IDs** are verb-shaped workflow actions (no `ml-`/`dl-` split); see ADR 0008. **Repo name** is `ml-wisdom-skills`.
6. **Geo/EO overlays** deferred. CV overlays ship in v1.
