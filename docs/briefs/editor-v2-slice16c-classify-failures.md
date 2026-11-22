# Brief: Editor v2 slice 16c, classify the 50 failing tap-walk rows (continuation of 16b)

Branch: claude/editor-v2-s1c
Size: 3 files, 300 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF in app code; zero migrations; no admin change. Tests only: NO `.tsx` / `src/` change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81. The tap table (frozen/tap-table.md, also in `docs/briefs/editor-v2-slice16b-e2e-walk.md` on branch brief/editor-v2-s1c) is the spec.

Read first: `docs/briefs/editor-v2-slice16b-e2e-walk.handover.md` (on this branch: failing-row table + likely real diffs). Spec: `tests/e2e/organiser-editor-v2.spec.ts`. Last run: 61 pass / 50 FAIL / 2 skip, 0 fixme.

## Files (touch only these)
- tests/e2e/organiser-editor-v2.spec.ts
- tests/e2e/helpers/organiserFake.ts (minimum: only if a state is missing, e.g. changed-venue night, weekly with `recurrence_rule` end date)
- docs/briefs/editor-v2-slice16c-classify-failures.handover.md (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`.

## What to do (decided; do not ask)
For EACH of the 50 failing tests, open the built component (src/modules/organiser editor v2 files), find the real label/control, then:
1. Spec locator or setup wrong (guessed label, missing fake state, wrong expected text): FIX the spec/fake so the assertion matches the PROTOTYPE tap-table destination. Never weaken an assertion to match the built UI.
2. Built UI really differs from the prototype row: `test.fixme('row N: <reason>')`, and list the row under `BUILT UI DIFFERS` at the top of the handover with: row, prototype says, built does, file:line of the built code.
3. Known UNBUILT (list, do not fix): row 36 move_date Undo; weekly per-night sold count; per-night ticket price write.
Check first the likely real diffs from the 16b handover: X-08 one-off still shows `ev2-tab-night`; X-06 "ends after" absent; X-02 changed-venue setup; row 83 End toast has no Undo; row 50 venue toast text.

## Checklist
- [ ] all 50 failures classified: each is now passing, or `test.fixme` with a handover entry
- [ ] zero unexplained failures; run at 390x844 and paste pass/fail/fixme counts
- [ ] OLD-URL guard still on every tap; no assertion weakened
- [ ] `npx tsc -p tsconfig.app.json --noEmit` 0 new errors; `npm run check:mojibake` clean; e2e spec coverage script still passes
- [ ] handover: BUILT UI DIFFERS list on top, then the tap table with `built: prototype / OLD / FIXME` column

## Handover
`docs/briefs/editor-v2-slice16c-classify-failures.handover.md`, commit, push to claude/editor-v2-s1c. No PR. Verified / Not verified. Push early (checkpoint commit after each 15 rows).
