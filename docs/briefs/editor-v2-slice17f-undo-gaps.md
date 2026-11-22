# Brief: Editor v2 slice 17f, Undo for "Save the date" (row 36.1)

Branch: claude/editor-v2-s1c
Size: 5 files, 120 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: FROZEN prototype wins (tap-table.md row 36 `dateDone`: "Else toast "Saved" + Undo"; checklist SH-WHEN-03 toast "Saved"; G-08 Undo puts the last change back, toast "Undone"). This is a gap fix: no visible change except the toast now carries Undo.
Read first: `docs/briefs/editor-v2-slice7b-when-sheet.handover.md`, `slice17a-prog-usual-endundo.handover.md`, `slice17b-fix-end-price-remove.handover.md` on this branch.

## Scope decision (server read, 2026-11-22)
- IN: row 36.1 `occurrence.move_date` Undo. Inverse = a second `occurrence.move_date` back to the previous date. Buildable client-only: the kind is in `OWNER_COMMAND_KINDS` (seriesCommands.ts:19), the old day is free again after the move, and `undoPlan` (saveNow.ts:12-24) already re-bases the occurrence version for the inverse.
- OUT, BLOCKED: row 83.1 `series.end_run` "Event ended" Undo. No owner-admitted inverse exists. Proof: (a) `series.restore_ended` is admin-only (`parity-matrix.test.ts:337` C31 MISSING-ADMIN-ONLY; absent from `v_owner_kinds` in admin 20261108180000_p5_owner_allowlist_recurring_create_v1.sql:871-876) and only goes archived -> ended anyway; (b) `series.set_lifecycle` from `ended` is admin-only (admin 20261109485000_r5_organiser_series_caps_v1.sql:412, "un-ending is admin-only"); (c) `end_run` also clears the recurrence rule, prunes via stop_repeating and cancels every upcoming date (admin 20261104320000..., "THE SWEEP"), none of which a client can replay. Needs an admin migration (owner-callable undo of end_run: restore rule + upcoming dates + lifecycle) and an owner decision. Leave e2e row 83.1 `fixme` and `slice17b.test.tsx:50` as they are. List it under UNBUILT.

## Files (touch only these)
- src/modules/organiser/p3/v2/editorModel.ts (`inverseOf`, add one `case` near the `occurrence.cancel` case, ~line 296; update the doc comment list above it, ~lines 255-261)
- src/modules/organiser/p3/v2/WhenSheet.tsx (`saveDate`, lines 99-107: drop `{ noUndo: true }`, send before `[{ to_date: date.occurrence_date }]` instead of `[null]`)
- src/modules/organiser/p3/v2/__tests__/slice17f.test.tsx (new)
- src/modules/organiser/p3/v2/__tests__/when.test.tsx (test at lines 126-134 pins `noUndo: true` and "no Undo": update it to the new contract)
- tests/e2e/organiser-editor-v2.spec.ts (row 36.1, line ~205: delete the `fixme: '...'` property only)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Build
1. `inverseOf`: `case 'occurrence.move_date': return typeof before.to_date === 'string' ? sameStep(step, { kind: c.kind, payload: { to_date: before.to_date } }) : null;` (never send `acknowledge_people` on the inverse unless the original had it; the sheet does not send it today, keep it that way).
2. `WhenSheet.saveDate`: `onSave([step], [{ to_date: date.occurrence_date }], W.saved)` (no 4th arg). Same-date and empty-date branches unchanged. Failure path unchanged (`setProblem`).
3. Do not touch `saveNow.ts`, `OneOffBody.tsx`, `EveryBody.tsx`.

## Checklist (each tested in slice17f.test.tsx; each test must fail on the old code)
- [ ] 1 `inverseOf` move_date with `before {to_date:'2026-12-03'}` returns a move_date step with `to_date` '2026-12-03' (old: null)
- [ ] 2 `inverseOf` move_date with no `to_date` in before returns null (no unsafe Undo)
- [ ] 3 `undoPlan` of a move_date step on an occurrence at version 3 returns one inverse step at version 4, same `id`
- [ ] 4 WhenSheet one-off, new date: `onSave` called with before `[{ to_date: <old date> }]`, label "Saved", and NO `noUndo` option (old: before `[null]`, `{noUndo:true}`)
- [ ] 5 `useSaveNow` round trip: save move_date, toast has an Undo; tap Undo runs a move_date back to the old date and shows "Undone"
- [ ] 6 same date, or empty date ("Add a date."), still sends nothing (unchanged, keep the existing assertions)
- [ ] 7 existing `when.test.tsx` row at 126-134 updated to the new contract and green
- [ ] 8 e2e row 36.1 has no `fixme` (cannot run here: note "not run" in the handover; `check-e2e-spec-coverage` must still pass)
- [ ] 9 row 83.1 unchanged: `slice17b.test.tsx:50` still green (end_run stays null)

## Handover
`docs/briefs/editor-v2-slice17f-undo-gaps.handover.md`. Tap table `button | destination | prototype/OLD`, any OLD = not done. Rows:
| row | button | destination | built |
|---|---|---|---|
| 36 / 36.1 | Save the date (one-off When sheet) | move_date, sheet closes, toast "Saved" + Undo; Undo = move_date back, toast "Undone" | prototype |
| 83.1 | End the event, no date | series.end_run, toast "Event ended" | OLD: no Undo, BLOCKED (admin RPC missing, see Scope decision) |
Sections: UNBUILT (83.1 plus the missing admin RPC named exactly), Verified / Not verified (390x844 walk and e2e not run unless run). Run `npx vitest run src/modules/organiser/p3/v2` and `npx tsc -p tsconfig.app.json --noEmit`; report failures in files you touched only. Commit, push to claude/editor-v2-s1c. No PR.
