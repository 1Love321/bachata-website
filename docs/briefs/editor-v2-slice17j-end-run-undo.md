# Brief: Editor v2 slice 17j, Undo after "End the event" part 1 of 2: the command and the inverse (series.resume_run)

Branch: claude/editor-v2-s1c
Size: 5 files, 90 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations here; NO admin change (the command is LIVE on prod).
Design: owner-picked FROZEN prototype (DESIGN-REGISTRY rows 61-81), `~/.claude/plans/editor-v2/frozen/event-editor-c2-prototype.FROZEN-2026-11-21.html` (grep `statusDo`, `undo`), `tap-table.md` rows 29, 82, 83 ("End the event ... both + Undo"), `checklist.md` G-06..G-08. Prototype wins. Read `docs/briefs/editor-v2-slice17a-prog-usual-endundo.handover.md` on this branch first (83.1 UNBUILT). Owner approved building this.

## Files (touch only these; max 5)
- src/modules/organiser/p3/v2/editorModel.ts (`inverseOf`, add a `series.end_run` case)
- src/modules/organiser/shared/seriesCommands.ts (add `'series.resume_run'` to `OWNER_COMMAND_KINDS`)
- src/modules/organiser/shared/__tests__/seriesCommands.test.ts (`SERVER_OWNER_KINDS` ~line 36: add `'series.resume_run'`, one line; the mirror test goes red otherwise)
- src/modules/organiser/__tests__/parity-matrix.test.ts (`PROD_OWNER_KINDS` ~line 457: add `'series.resume_run'`, one line)
- src/modules/organiser/p3/v2/__tests__/slice17a.test.tsx (the describe 'End the event Undo (row 83)': the "NO inverse" test is replaced by the new behaviour; add the new cases here, no new test file)
Part 2 (slice 17k) wires the before-state in EveryBody; do NOT touch EveryBody.tsx / OneOffBody.tsx here. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Ground truth (read from prod `stsdtacfauprzrdebmzg`, source of `_cmd_series_resume_run_p5`, read-only)
- `series.resume_run`, payload MUST be `{}` (else `invalid_payload`). Allowed: admin, or owner/manager member of the series (it is in `check_owner_command_allowlist_v1`).
- Series must currently be `ended` (else `invalid_state`). It restores the repeat rule from the LAST successful `series.end_run` audit row, sets the series live through `series.set_lifecycle`, and re-mints the upcoming nights. Returns `{from:'ended', to:'live', nights_created, upcoming_nights, rule_from_audit}`.
- If there is no saved rule (the end_run audit row had none, which is the case for a ONE-OFF event) it refuses with `no_saved_rule`. So: a weekly (Every) event Undo = `series.resume_run`; a one-off Undo = `series.set_lifecycle {to: <previous lifecycle>}` (already supported by `inverseOf`, "Ended CAN go live again").
- Today `editorModel.inverseOf` has no `series.end_run` case, so the toast after "End the event" has no Undo (tested as "NO inverse").

## What to build (decided; do not ask)
1. `inverseOf`: case `'series.end_run'`: if `before.resume === true` return `sameStep(step, { kind: 'series.resume_run', payload: {} })`; else if `typeof before.to === 'string'` return `sameStep(step, { kind: 'series.set_lifecycle', payload: { to: before.to } })`; else null. Update the doc comment above `inverseOf` (the bullet list) with the new line.
2. `seriesCommands.ts`: add `'series.resume_run'` to `OWNER_COMMAND_KINDS` (comment: admin 20261121200531 C2, owner/manager). Mirror tests updated as above.

## Checklist (each item is a test in slice17a.test.tsx that FAILS on the 17j parent)
- [ ] `undoPlan([end_run step], [{to:'live', resume:true}])` gives `{kind:'series.resume_run', payload:{}}` (the old "NO inverse" test now asserts this)
- [ ] `undoPlan([end_run step], [{to:'live'}])` gives `series.set_lifecycle` `{to:'live'}`; before `{to:'paused'}` gives `{to:'paused'}`; before null/`{}` gives null
- [ ] `OWNER_COMMAND_KINDS` contains `series.resume_run`; seriesCommands.test.ts and parity-matrix.test.ts green
- [ ] Existing v2 tests all green (the "NO inverse" test is updated, not deleted)

## Handover
`docs/briefs/editor-v2-slice17j-end-run-undo.handover.md`. Tap table `button | destination | prototype/OLD`; any OLD row = not done. Rows at least: none are UI taps in this slice (model only); state that and list the pure-function cases instead. Sections UNBUILT and Verified / Not verified (say plainly: resume_run was not exercised against prod; no writes). Run `npm run test:unit -- src/modules/organiser/p3/v2 src/modules/organiser/shared src/modules/organiser/__tests__/parity-matrix.test.ts` and `npx tsc --noEmit -p .` and `npm run check:mojibake`. No secrets. Commit, push to claude/editor-v2-s1c. No PR.
