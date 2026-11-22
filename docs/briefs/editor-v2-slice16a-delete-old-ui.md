# Brief: Editor v2 slice 16a, delete the old v2 event page and night screen

Branch: claude/editor-v2-s1c
Size: 5 files, 120 lines (mostly deletions)
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81. This slice adds no UI; it removes the pre-prototype v2 UI that `EditorV2` replaced.

Read first: `docs/briefs/editor-v2-slice15-one-off-page.handover.md` (on this branch) for current state.

## Facts checked on the branch head 5227ad2a1
- `EditorGate.tsx` mounts `EditorV2` when `v2 === 'editor'`, else the old `EventPageV2`. The only caller (`shell/P3Routes.tsx` line ~112) passes `v2="editor"`.
- `EventPageV2.tsx` (225 lines) and `NightV2.tsx` (383 lines) are used only by `EditorGate` / each other.
- `SaveBar.tsx` (`useV2Save`, `useV2Toast`, `UnsavedSheet`), `editorDraft.ts` and `scope.ts` are STILL IMPORTED by live code (`useEditor.tsx`, `saveNow.ts`, `editorModel.ts`, `screens/EditTray.tsx`, tests). Do NOT delete them in this slice; 16b handles them if anything is left over.

## Files (touch only these)
- DELETE src/modules/organiser/p3/v2/EventPageV2.tsx
- DELETE src/modules/organiser/p3/v2/NightV2.tsx
- src/modules/organiser/p3/v2/EditorGate.tsx: mount `EditorV2` always (keep the `isV2Format` and festival fallback); remove the `v2` prop and the `EventPageV2` import.
- src/modules/organiser/p3/shell/P3Routes.tsx: drop the `v2="editor"` prop only.
- src/modules/organiser/p3/v2/__tests__/routes.flag.test.tsx: update to the new gate; any test that only exercised the deleted components is deleted, not rewritten. Any other test file that imports the two deleted files: fix or delete it (list each in the handover).
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Behaviour
Flag OFF: unchanged (old R4 screens). Flag ON: weekly and one-off events open `EditorV2`; festival, course and festival-series formats still get `fallback`. No other behaviour change.

## Checklist (each done)
- [ ] X-a both files gone, `git grep -n "EventPageV2\|NightV2" src tests` returns nothing (CSS comment hits allowed, list them)
- [ ] X-b gate always mounts EditorV2 for v2 formats; fallback for the rest (test for both)
- [ ] X-c no orphan exports: run `npx knip`-style check if the repo has one (`npm run` list), else grep each deleted file's exports
- [ ] X-d `npx vitest run src/modules/organiser/p3/v2` green; `npx tsc -p tsconfig.app.json --noEmit` 0 new errors; `npm run check:mojibake` clean

## Tap table
None new (no UI). State this in the handover.

## Handover
Write `docs/briefs/editor-v2-slice16a-delete-old-ui.handover.md`, commit, push to claude/editor-v2-s1c. No PR. List verified / not verified.
