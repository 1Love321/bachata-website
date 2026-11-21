# Brief: Editor v2 slice 1, save engine (saveNow, inverse, toast with Undo)

Branch: claude/editor-v2-s1c
Size: 4 files, 330 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.

Prototype (WHOLE spec): C:/Users/ecual/.claude/plans/editor-v2/frozen/event-editor-c2-prototype.FROZEN-2026-11-21.html (read only the toast + undo JS via grep). Checklist ids: frozen/checklist.md G-04..G-08. Tap table row 34 (frozen/tap-table.md). DESIGN-REGISTRY rows 61-81 win over this brief.

## What main lacks
- extends: `useV2Save` in `src/modules/organiser/p3/v2/SaveBar.tsx` runs a plan only on a Save-bar tap; the prototype saves every change at once.
- No inverse of a write step; no toast with Undo (existing `useV2Toast` is 4.5 s, no button).

## Files (touch only these)
- src/modules/organiser/p3/v2/saveNow.ts (new)
- src/modules/organiser/p3/v2/editorModel.ts (add `inverseOf`)
- src/modules/organiser/p3/v2/V2Toast.tsx (new)
- src/modules/organiser/p3/v2/__tests__/saveNow.test.ts (new)
Do NOT edit SaveBar.tsx (slice 2 and 16 own it), screens/*, R4*, or any file outside the list.

## Build
1. `inverseOf(step, before)` in editorModel.ts: from a `WriteStep` plus the value read before the write, return the `WriteStep` that puts it back, for every kind used by `writePlan` (series upsert, set_recurrence, set_lifecycle, set_program, occurrence set_override / set_time / cancel / bring back, programme replace, organisers). A kind with no safe inverse returns null and the toast then shows without Undo. List each kind and its inverse in the test.
2. `saveNow.ts`: `useSaveNow(seriesId, seriesVersion)` wraps the existing `useV2Save().run` (reuse, no second runner): `saveNow(steps, before, label)` runs steps at once, on success shows the toast with the label ("Saved" or the caller's text) and, if every step has an inverse, an Undo that runs the inverse steps in reverse order and then shows toast "Undone" (no Undo on that one). On failure: no toast, return the error text; nothing half-undone. One idempotency key per step kept (reuse `runHeld`).
3. `V2Toast.tsx`: portalled to `document.body`. Dark #1C1C1C, white text 15px, radius 10px, 12px from left and right, 76px above the bottom; with Undo button: min-height 36px, border 1px #FFFFFF66, weight 600, shown 8000 ms; without Undo (cancel night, bring back, cancel event, refund, copy link, switches, "Undone") shown 2200 ms. Role=status. A new toast replaces the old one and resets the timer. Does not scroll the page (`focus({preventScroll:true})` if it focuses anything).

## Prototype checklist
- [ ] G-04 no save bar and no "unsaved changes" sheet anywhere: every change saves at once (engine only here; the bar is removed in slice 16)
- [ ] G-05 toast look: #1C1C1C, white 15px, radius 10px, 12px left/right, 76px above bottom; shows after every save
- [ ] G-06 Undo button: min height 36px, border 1px #FFFFFF66, weight 600; toast shown 8000 ms
- [ ] G-07 toast without Undo shown 2200 ms
- [ ] G-08 tap Undo puts the last change back and shows toast "Undone"
- [ ] tap row 34: Undo | toast | last change put back, toast "Undone"

## Tests (vitest, no network)
Inverse per write kind round-trips (apply then inverse = original); saveNow success shows Undo toast 8000 ms; Undo runs inverses in reverse and shows "Undone" 2200 ms; no-inverse kind = toast without Undo; failure = no toast, error returned; second tap during a run ignored; toast box at 390x844 inside viewport (jsdom bounding math or e2e if cheap).

## Rules
Never merge. Bot-authored push to claude/editor-v2-s1c (existing PR 1865; do not open another). Push once when coherent; PARTIAL marker if context tight. Zero bytes to /app first load (`npm run check:bundle-budget`). Handover ends with a TAP TABLE: button | destination | prototype/OLD (this slice: row 34 only; one OLD row = keep building). A prototype element you cannot build: STOP and write it at the top of the handover; no "remaining differences" list.
