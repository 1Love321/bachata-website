# Brief: Editor v2 slice 17a, mount Classes and party, Back to the usual times, End toast Undo

Branch: claude/editor-v2-s1c
Size: 5 files, 300 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype (DESIGN-REGISTRY rows 61-81). Prototype wins over this text. Spec rows are in `docs/briefs/editor-v2-slice16b-e2e-walk.md` (branch brief/editor-v2-s1c) and `tests/e2e/organiser-editor-v2.spec.ts`; the rows below are fixme there and must pass after this slice.

Read first: `docs/briefs/editor-v2-slice16d-finish-walk.handover.md` on this branch (BUILT UI DIFFERS section).

## Files (touch only these)
- src/modules/organiser/p3/v2/EveryBody.tsx (mount ProgSheet on the Every "When" fact; End Undo)
- src/modules/organiser/p3/v2/WhenSheet.tsx (Back to the usual times render site, if the night When sheet is where it belongs)
- src/modules/organiser/p3/v2/OneOffBody.tsx (End Undo only)
- src/modules/organiser/p3/v2/copy.ts (only if a string is missing)
- src/modules/organiser/p3/v2/__tests__/slice17a.test.tsx (new)
Do NOT edit saveNow.ts, editorModel.ts, Facts.tsx, shared/. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only. Do not touch `tests/e2e` (slice 16e un-fixmes).

## What to build (decided; do not ask)
1. **Rows 21, 40, 41, 42, "Classes and party" on the Every tab.** `ProgSheet` (WhenSheet.tsx:119) is exported and rendered nowhere. Tap 21 = the Every card "When" fact "Change" opens sheet "Classes and party" (title `W.progTitle`). Session row (40) opens `SessionSheetV2` for that usual session; "Add a class or party" (41) opens `SessionSheetV2` new; "Done" (42) closes with no toast. Read the usual sessions with the same hook `WhenSheet` uses (`prog`), saving through the same path the night sheet uses for sessions (ctx `usual`, not `night`). Read how `SessionSheetV2` takes `ctx` and make the usual context write the series programme, not a night override. Prototype tap rows: see tap-table rows 21, 40, 41, 42 in the frozen tap table (`plans/editor-v2/frozen/tap-table.md`; copy the rows into your handover).
2. **Row 39 "Back to the usual times".** Copy string `W.backToUsualTimes` (copy.ts:321) has no render site. In the night When sheet, when the night has its own times (night patch / override_keys contains a times key), show the button; tap closes the sheet, runs the Back to usual step and toasts `V2.backToUsualFor(dateWords)` ("Back to usual for <date>") with Undo. There is NO writer for this yet (LIVE gap 7b): if no existing command clears the night times, do NOT invent one. Build the button only if a writer exists; otherwise leave it, and list "row 39 needs writer" under UNBUILT in the handover. Check `NightCard.tsx` `backStep` (line ~40) for how other facts do it.
3. **Row 83 End the event toast Undo.** Prototype: later date given = stays Live, toast "Ends after <date>" + Undo; no date = pill "Ended", toast "Event ended" + Undo. Both paths already pass an undo entry to `saveNow` (EveryBody.tsx:161-162, 150-157; OneOffBody.tsx:67-68). Find why no Undo button shows (does `saveNow` skip Undo for lifecycle commands, or is the entry shape wrong?) and fix at the narrowest layer. If the cause is in `saveNow.ts` (not in your file list), STOP, put cause + file:line under UNBUILT in the handover, and do the rest.

## Checklist (each tested in slice17a.test.tsx)
- [ ] row 21 opens Classes and party from Every When
- [ ] row 40 session row opens the usual session sheet
- [ ] row 41 Add a class or party opens a new session sheet (usual context)
- [ ] row 42 Done closes, no toast
- [ ] row 39 button present only when the night has own times, and only if a writer exists (else UNBUILT line)
- [ ] row 83 both End paths show Undo and Undo restores

## Handover
`docs/briefs/editor-v2-slice17a-prog-usual-endundo.handover.md` (new file counts as a 6th file: allowed). Tap table columns `button | destination | prototype/OLD`, any OLD = not done. UNBUILT and Verified / Not verified sections. Commit, push to claude/editor-v2-s1c. No PR.
