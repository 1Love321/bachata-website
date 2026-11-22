# Brief: Editor v2 slice 17k, Undo after "End the event" part 2 of 2: wire the before-state

Branch: claude/editor-v2-s1c
Size: 3 files, 90 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; NO admin change.
Design: FROZEN prototype `~/.claude/plans/editor-v2/frozen/event-editor-c2-prototype.FROZEN-2026-11-21.html` (grep `statusDo`), `tap-table.md` rows 82-83 ("End the event ... both + Undo"), `checklist.md` G-06, G-08. Prototype wins. Read `docs/briefs/editor-v2-slice17j-end-run-undo.md` and its handover on this branch first: 17j added `inverseOf(series.end_run)`: `before.resume === true` gives `series.resume_run` `{}`; else `before.to` string gives `series.set_lifecycle` back.

## Files (touch only these; max 5)
- src/modules/organiser/p3/v2/EveryBody.tsx (`endDo`, the end_run branch ~line 207)
- src/modules/organiser/p3/v2/OneOffBody.tsx (`endDo` ~line 67: only add a comment line if no code change is needed; no behaviour change)
- src/modules/organiser/p3/v2/__tests__/slice17k.test.tsx (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Ground truth
- `series.resume_run` (live on prod) needs the repeat rule saved by the last `series.end_run`, which exists for a weekly event but NOT for a one-off (a one-off refuses `no_saved_rule`). So: Every event Undo = `series.resume_run`; one-off Undo = `series.set_lifecycle` back to the previous state (already works from `before.to`).
- The Every `endDo` end_run branch passes before `[{ to: s.lifecycle_status ?? 'live' }]`: with 17j that alone yields a set_lifecycle Undo, which would bring a weekly event back live WITHOUT its rule or nights. It must carry `resume: true`.

## What to build (decided; do not ask)
1. `EveryBody.tsx endDo`, the `series.end_run` branch ONLY: before becomes `[{ to: s.lifecycle_status ?? 'live', resume: true }]`. The earlier later-last-date branch (`series.set_recurrence`) is unchanged.
2. `OneOffBody.tsx`: no behaviour change (before stays `{to}`).
3. A refused Undo (`invalid_state`, `no_saved_rule`, anything) uses the existing failed-Undo path in `saveNow.ts` (read it; do not invent copy). If no readable line exists for it, add nothing and list the case in the handover.

## Checklist (each item is a test in slice17k.test.tsx that FAILS on the 17j head)
- [ ] Every event, End the event, no date: saveNow/onSave called with `series.end_run` and before `[{to:'live', resume:true}]`; `undoPlan(steps, before)[0].command` equals `{kind:'series.resume_run', payload:{}}`
- [ ] The toast "Event ended" carries an Undo button (the toast has an undo when `undoPlan` is non-null)
- [ ] Every event, later last date: still `series.set_recurrence`, Undo restores the rule (unchanged)
- [ ] One-off, End: before `[{to:'live'}]`, undo plan is `series.set_lifecycle {to:'live'}`; a paused one-off gives `{to:'paused'}`
- [ ] Existing v2 tests all green

## Handover
`docs/briefs/editor-v2-slice17k-end-run-undo-wire.handover.md`. Tap table `button | destination | prototype/OLD`; any OLD row = not done. Rows: End the event (Every, no date), End the event (one-off), Undo on "Event ended", Make it live again (unchanged). Sections UNBUILT and Verified / Not verified (say plainly: resume_run was not exercised against prod; no writes). Run `npm run test:unit -- src/modules/organiser/p3/v2` and `npx tsc --noEmit -p .` and `npm run check:mojibake`. No secrets. Commit, push to claude/editor-v2-s1c. No PR.

## G3 B-N8 raw-Unicode fix (added 2026-11-22, coordinator)
`consistency.matrix` G3 B-N8 is RED from raw U+2014 in source. In `p3/v2/EveryBody.tsx` ~line 232 replace `venue?.name ?? '—'` with the escape `'—'`. No rendered change. Acceptance: G3 B-N8 passes after this slice (17i fixes NightCard.tsx and Facts.tsx).
