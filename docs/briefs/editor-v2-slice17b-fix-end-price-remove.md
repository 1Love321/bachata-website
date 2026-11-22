# Brief: Editor v2 slice 17b, End Undo, night Price "Pay at the door", row 48 Remove

Branch: claude/editor-v2-s1c
Size: 4 files, 200 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype (DESIGN-REGISTRY rows 61-81). Prototype wins over this text. Frozen tap table: `plans/editor-v2/frozen/tap-table.md` (rows 48, 82, 83). Slice 17a handover (on this branch) lists the End Undo cause.

## Files (touch only these)
- src/modules/organiser/p3/v2/editorModel.ts (inverseOf: series.end_run case)
- src/modules/organiser/p3/v2/NightCard.tsx (line ~95 priceLabel call)
- src/modules/organiser/p3/v2/WhenSheet.tsx and/or the session-sheet host that closes on Remove (row 48 only)
- src/modules/organiser/p3/v2/__tests__/slice17b.test.tsx (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only. Do not touch tests/e2e.

## What to build (decided; do not ask)
1. **Row 83.1 End has no Undo (no-date End and one-off End).** Add a `series.end_run` case to `inverseOf`. `set_lifecycle` back would NOT restore the dropped coming nights. If no safe inverse exists from the `before` snapshot, do NOT invent one: list cause + file:line under UNBUILT in the handover and keep the toast without Undo. Never claim an Undo that does not restore.
2. **X-09 night Price fact.** `NightCard.tsx` calls `priceLabel` without `doorPrice`, so the night Price fact never says "Pay at the door". Pass the same door-price input the Every card passes (see EveryBody / payMode.ts) so a pay-at-the-door night reads "Pay at the door".
3. **Row 48 Remove this one.** Prototype tap-table row 48: "Sheet closes; tab toast + Undo. Disabled when it is the only one". Built behaviour closes only to the stacked When sheet. Fix so Remove closes the session sheet AND the When/Classes sheet back to the tab, with the tab toast + Undo. Prototype wins.

## Checklist (each tested in slice17b.test.tsx)
- [ ] 83.1 no-date End and one-off End: Undo restores, or UNBUILT line with cause
- [ ] X-09 pay-at-door night Price fact says "Pay at the door"; priced night unchanged
- [ ] row 48 Remove closes to the tab with toast + Undo; disabled when only one session

## Handover
`docs/briefs/editor-v2-slice17b-fix-end-price-remove.handover.md` (new file allowed). Tap table columns `button | destination | prototype/OLD`, any OLD = not done. UNBUILT and Verified / Not verified sections. Run the v2 unit tests. Commit, push to claude/editor-v2-s1c. No PR.
