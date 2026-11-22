# Brief: Editor v2 slice 17h, test + unicode fixes and venue "coming soon" row

Branch: claude/editor-v2-s1c
Size: 4 files, 60 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype (DESIGN-REGISTRY rows 61-81), `~/.claude/plans/editor-v2/frozen/` (checklist.md, tap-table.md). Owner click 2026-11-22: typed new venue is NOT built; a disabled row reading exactly `Adding venues: coming soon` replaces it, same spot only (the owner builds Google-Maps-link venue add next week).
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Files (touch only these; max 5)
- src/modules/organiser/p3/tickets/__tests__/tickets.managerReal.matrix.test.tsx (test-only)
- src/modules/organiser/p3/v2/copy.ts
- src/modules/organiser/p3/v2/WhereSheet.tsx
- src/modules/organiser/p3/v2/__tests__/whereAbout.test.tsx (add cases)

## What to build (decided; do not ask)
1. tickets.managerReal.matrix.test.tsx line ~188: expect `'ticket_sales_summary_v2'` (17e moved `fetchSalesSummary` to v2). Fixes the two `useSummary(money ...)` failures. The `toEqual` at ~176 already passes (refusal has `types: []`); leave it. Update the stale comment at line ~152 only if it names the RPC.
2. copy.ts: replace every raw non-ASCII character in a STRING (about 10 lines: middle dot, ellipsis, pound sign, right single quote) with a `\u` escape (`\u00b7`, `\u2026`, `\u00a3`, `\u2019`). Find them with `grep -nE '[^ -~	]' src/modules/organiser/p3/v2/copy.ts`. The rendered text must not change (same characters at runtime). Existing line 314 already uses `\u2026`: follow it. Fixes consistency.matrix G3 B-N8 ("no raw Unicode punctuation outside comments").
3. Venue placeholder. copy.ts `whereAbout`: add `addVenueSoon: 'Adding venues: coming soon'`. WhereSheet.tsx: inside the venue list card, AFTER the rows and after the `noMatch` hint (that is where a typed unknown name would be offered), render one disabled row: `<button type="button" className="row" data-testid="where-add-soon" disabled aria-disabled="true">` with the copy in a `<span className="hint">`, no chevron, no handler, no write, always shown once `venues.data` has loaded (also when rows exist). It is NOT a `where-row` (existing test "no Use ... row for an unknown name" counts `where-row`). Picking an existing venue is unchanged.

## Checklist (each a test; new ones FAIL on the 17g code)
- [ ] 1 managerReal `useSummary(money true)` and `(money false)` pass with the v2 RPC name
- [ ] 2 consistency.matrix G3 B-N8 passes; copy strings render the same characters (assert one: whereAbout loading ends with the ellipsis)
- [ ] 3 Where sheet, data loaded: `where-add-soon` present, disabled, text exactly `Adding venues: coming soon`; click does nothing (onSave not called)
- [ ] 3 unknown name typed: `where-row` count 0 AND `where-add-soon` still present
- [ ] 3 loading (venues pending): no `where-add-soon`
- [ ] Existing whereAbout + v2 tests green

## Handover
`docs/briefs/editor-v2-slice17h-fixes.handover.md`. Tap table `button | destination | prototype/OLD`; rows: Where sheet venue row (existing venue pick), Adding venues: coming soon (disabled, no destination), Back to the usual venue. Any OLD row = not done. Sections UNBUILT and Verified / Not verified. Run `npm run test:unit -- src/modules/organiser` and `npx tsc --noEmit -p .` (4 pre-existing errors in Venues.tsx, VenueEntity.tsx, VendorPortal.tsx, venuePublicService.ts are inherited from the stale base: do NOT touch), `npm run check:mojibake`. No secrets. Commit, push to claude/editor-v2-s1c. No PR.
