# Brief: Editor v2 slice 17c, pre-preview cleanup and un-fixme X-09

Branch: claude/editor-v2-s1c
Size: 5 files, 150 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype (DESIGN-REGISTRY rows 61-81). Prototype wins. Read `docs/briefs/editor-v2-slice17b-fix-end-price-remove.handover.md` on this branch first.

## Files (touch only these; max 5)
- src/modules/organiser/p3/v2/__tests__/eventPage.proto.test.ts (fix its tsc error)
- src/modules/organiser/p3/v2/V2Sheet.tsx (fix its eslint error, no behaviour change)
- src/modules/organiser/p3/v2/EveryBody.tsx and/or Facts.tsx (Every card Price fact, item 3)
- tests/e2e/organiser-editor-v2.spec.ts (un-fixme X-09 only; 83.1 stays fixme, it is UNBUILT)
- src/modules/organiser/p3/v2/__tests__/slice17c.test.tsx (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## What to build (decided; do not ask)
1. `npx tsc --noEmit -p .` error in eventPage.proto.test.ts: fix with the narrowest type change; behaviour of the test unchanged.
2. `npx eslint src/modules/organiser/p3/v2/V2Sheet.tsx` error: fix it, no visual or behaviour change.
3. Every card Price fact: the 17b handover notes it shows "Add a ticket" for a pay-at-the-door series while the night card says "Pay at the door". Make the Every card Price fact label follow the same rule as the night card (door price, no link => "Pay at the door"; prototype wording wins). If the prototype shows something else for the Every card, build the prototype and say so.
4. Un-fixme the X-09 e2e row; it must pass. Do not touch the 83.1 fixme.

## Checklist (tested in slice17c.test.tsx where unit-testable)
- [ ] tsc: 0 errors in v2 files
- [ ] eslint V2Sheet.tsx clean
- [ ] Every Price fact for a door-price, no-link series reads the door label; priced and linked series unchanged
- [ ] X-09 e2e un-fixme passes

## Handover
`docs/briefs/editor-v2-slice17c-cleanup-unfixme.handover.md`. Tap table `button | destination | prototype/OLD`, any OLD = not done. UNBUILT and Verified / Not verified. Run v2 unit tests. Commit, push to claude/editor-v2-s1c. No PR.
