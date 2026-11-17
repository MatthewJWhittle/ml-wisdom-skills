# Split experiment logging from AGENTS-FEEDBACK

Agents were stuffing run narratives into `AGENTS-FEEDBACK.md`, which made the feedback map grow into a junk drawer. We split concerns:

- **`AGENTS-FEEDBACK.md`** — stable router/config: feedback source commands, holdouts, trackers, gaps, pointer to the experiment log, skill routing table.
- **`EXPERIMENTS.md`** — append-only human-readable experiment log (hypothesis, change, signals, decision).
- **`results.tsv`** — dense machine-oriented ledger for one `run-objective-loop` (unchanged).

`wire-feedback` creates/updates the config and scaffolds the experiment log; it does not own experiment narratives. Other skills append to `EXPERIMENTS.md` when they complete a meaningful try.
