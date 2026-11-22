# Brief: Editor v2 slice 16d, finish the tap-walk spec (continuation of 16b/16c)

Branch: claude/editor-v2-s1c
Size: 4 files, 300 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF in app code; zero migrations; no admin change. Tests only: NO `src/` change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81. Tap table in `docs/briefs/editor-v2-slice16b-e2e-walk.md` (branch brief/editor-v2-s1c) is the spec.

Read first: `docs/briefs/editor-v2-slice16c-classify-failures.handover.md` on this branch (69 pass / 42 fail / 2 skip; causes and next steps listed). Spec: `tests/e2e/organiser-editor-v2.spec.ts`.

## Files (touch only these)
- tests/e2e/organiser-editor-v2.spec.ts
- tests/e2e/helpers/organiserFake.ts (per-night override for changedVenue; mocks)
- scripts/check-e2e-spec-coverage.mjs (MANUAL run line gains `VITE_ENABLE_ORGANISER_GUEST_LIST_LIVE=true`)
- docs/briefs/editor-v2-slice16d-finish-walk.handover.md (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`.

## What to do (decided; do not ask)
Follow "Next steps" 1-4 of the 16c handover, in order. Push a checkpoint after each step.
1. Changed-venue per-night override in the fake (unblocks 39, 52, 75, X-02).
2. Tickets screen and Price sheet and Classes-and-party sheet stuck on "Loading...": log `requestfailed` and `/rest/v1` + `/rpc` responses >= 400 / unmocked calls, mock them (read the built hook to get the exact shape). Re-run rows 21, 40-42, 53-56, 88-106, X-09, X-11, X-12.
3. Fix locators: 43, 48, 50, 51 (`getByRole('searchbox')`), 62; classify 83, X-06, X-08.
4. A row the built UI truly cannot satisfy: `test.fixme('row N: reason')` + entry on top of the handover under BUILT UI DIFFERS with file:line. Never weaken an assertion.
Known UNBUILT (list, do not fix): row 36 move_date Undo; weekly per-night sold count; per-night ticket price write.

## Checklist
- [ ] every previously failing row is passing or `test.fixme` with handover entry; 0 unexplained
- [ ] OLD-URL guard still on every tap
- [ ] counts pasted (pass/fail/fixme) at 390x844
- [ ] `npm run check:mojibake` clean; `npm run check:e2e-spec-coverage` passes (or named script)
- [ ] handover: BUILT UI DIFFERS on top, tap table with `built: prototype / OLD / FIXME`

## Handover
Commit, push to claude/editor-v2-s1c. No PR. Verified / Not verified.
