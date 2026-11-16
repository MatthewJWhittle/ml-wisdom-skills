# Objective-loop operator checklist

Use during a run; do not invent a second keep rule.

## Before each try

- [ ] Hypothesis written (will become TSV `hypothesis`)
- [ ] Change is inside mutable surface only
- [ ] Working tree otherwise clean enough to commit intentionally
- [ ] Know exact feedback commands from `AGENTS-FEEDBACK.md`

## After each try

- [ ] Primary parsed from the agreed log/path
- [ ] Every guardrail checked (not “looked fine”)
- [ ] TSV row appended with full reasoning fields
- [ ] Tracker mirror done if required
- [ ] Keep → leave commit; discard/crash → reset to last keep

## Anti-patterns

- Bundling several unrelated edits in one try
- Keeping a primary win that failed a named guardrail
- Changing the evaluator or labels mid-loop
- Comparing a 2-minute debug run to a 30-minute budgeted run without noting it
- Skipping the scope interview because “it’s just LR”
