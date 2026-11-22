# Brief: Editor v2 slice 16e, fix the 5 failing walk rows and un-fixme (tests only)

Branch: claude/editor-v2-s1c
Size: 4 files, 300 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; tests only: NO `src/` change; zero migrations.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81. Prototype wins.

Read first: `docs/briefs/editor-v2-slice16d-finish-walk.handover.md` and `docs/briefs/editor-v2-slice17a-prog-usual-endundo.handover.md` on this branch. Spec: `tests/e2e/organiser-editor-v2.spec.ts`.

## Files (touch only these)
- tests/e2e/organiser-editor-v2.spec.ts
- tests/e2e/helpers/organiserFake.ts
- scripts/check-e2e-spec-coverage.mjs (only if needed)
- docs/briefs/editor-v2-slice16e-fix-walk.handover.md (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`.

## What to do (decided; do not ask)
1. Fix the 5 failing: X-06 ("Every Friday, ends after ": check fake `recurrence_rule.until` field vs the Repeats row text built), X-09 (door state Price fact; check Facts price label), X-12 (scope first `tap(/Class|Party/)` to the sheet, not the footer `/classes` link), X-12c, X-13 (built hub raffle label). Spec or fake is wrong unless proven otherwise; if the built UI truly differs, `test.fixme('row N: reason')` with file:line in the handover, never weaken an assertion.
2. Row 56 "Change the link": prototype says one-off only; weekly night sheet showing "Change every <day>" is correct. Rewrite the row to run on a one-off and un-fixme.
3. Un-fixme rows 21, 40, 41, 42 (17a built Classes and party on Every When), 39 (17a built Back to the usual times; needs a night with its own start time in the fake), 83 later-date path (Undo exists). Row 83 no-date path: 17a found no Undo (`inverseOf` has no `series.end_run` case): keep that one `test.fixme` with that reason.
4. Row 48: seed a second session in the fake so Remove is enabled; row 62: per-night gallery override read-back in the fake. Un-fixme both.
5. Run the full spec at 390x844; paste pass/fail/fixme counts. Also re-run the other `tests/e2e/organiser-*.spec.ts` that share `organiserFake.ts` (16d changed it) and report.

## Checklist
- [ ] 0 unexplained failures; every remaining skip is `test.fixme` with reason in handover
- [ ] OLD-URL guard still on every tap
- [ ] counts pasted; other organiser specs re-run
- [ ] `npm run check:mojibake` clean; `npm run check:e2e-spec-coverage` passes
- [ ] handover: BUILT UI DIFFERS on top; tap table `built: prototype / OLD / FIXME`

## Handover
Commit, push to claude/editor-v2-s1c. No PR. Verified / Not verified.
