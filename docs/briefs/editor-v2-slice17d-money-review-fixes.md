# Brief: Editor v2 slice 17d, money review fixes (slices 12-14)

Branch: claude/editor-v2-s1c
Size: 5 files, 350 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: FROZEN prototype wins (DESIGN-REGISTRY rows 61-81). These are bug fixes: no visible change except where noted.
Read first: `docs/briefs/editor-v2-slice17c-cleanup-unfixme.handover.md` on this branch.

## Files (touch only these)
- src/modules/organiser/p3/v2/TicketSheetV2.tsx
- src/modules/organiser/p3/v2/TicketsV2.tsx
- src/modules/organiser/p3/v2/PriceSheet.tsx
- src/modules/organiser/p3/v2/EarlyBirds.tsx (only if finding 4 needs it)
- src/modules/organiser/p3/v2/__tests__/slice17d.test.tsx (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Findings to fix (Sonnet review at 1dcd8b5a0, each proved by reading code; verify, then fix at the narrowest layer)
1. HIGH TicketSheetV2.tsx:107-112, 128-131, 35-36: Undo of a name-only or places-only ticket edit does nothing but toasts "Undone". `writeTicket` computes `baseSame` against the stale pre-edit `p.type`, so no upsert runs. Same flaw breaks the failure rollback at line ~123. Fix: compare the form against the freshly fetched row.
2. MED TicketSheetV2.tsx:42-61, 119-125: failed save of a NEW ticket leaves an orphan (upsert ok, setPriceStep/setTicketStatus fails), retry creates a duplicate. Keep the created id in state for the retry, or close the created row on failure.
3. MED TicketsV2.tsx:323-326, 251: "Sell tickets here" silently does nothing after reload; the paused-by-switch set lives only in sessionStorage (134-141). `changeOnce` sees no moves, toasts "Selling here" and returns true while tickets stay paused. Fix: derive the reopen set from server state, or do not toast success when nothing moved.
4. MED EarlyBirds.tsx:13-14, 20-26 + TicketSheetV2.tsx:32-34: steps mapped to a calendar day and back to London midnight; a stored step with another time counts as changed (blocks rename with birdLive, or silently rewrites to midnight). Unproven: check whether non-midnight steps can exist; if yes, keep the stored instant when the day is unchanged.
5. MED TicketSheetV2.tsx:98,117,136,167 + TicketsV2.tsx:179,340: sold-count guards fail open (sold=0 while the summary loads or failed). Disable Remove and the places check, or block opening, until the summary has loaded; on failure refuse like `guardSold` (line ~302).
6. LOW-MED PriceSheet.tsx:123-136: "tickets" state shows editable price inputs but Done saves nothing (slice 14 UNBUILT). Make the inputs read-only/disabled in that state so no typed price is lost silently. Prototype wins: if the prototype shows editable, STOP and list it under UNBUILT instead.
7. LOW PriceSheet.tsx:60 "Back to the usual price" closes before the result so a failure is never shown: close only after success.
Not in scope: TicketsV2.tsx:186 per-date sold for series-wide tickets, TicketsV2.tsx:204 stripe poll `ret.again` (list both under UNBUILT).

## Checklist (each tested in slice17d.test.tsx; each test must fail on the old code)
- [ ] 1 name-only and places-only edit: Undo restores; failure rolls back
- [ ] 2 failed new-ticket save then retry makes no second ticket
- [ ] 3 Sell here after reload: tickets reopen, or no false success toast
- [ ] 4 non-midnight step: unrelated edit not blocked and step not rewritten (or UNBUILT with proof it cannot exist)
- [ ] 5 sold guards closed while summary loading or failed
- [ ] 6 Price sheet tickets state: no silent-loss input
- [ ] 7 Back to usual price shows a failure

## Handover
`docs/briefs/editor-v2-slice17d-money-review-fixes.handover.md`. Tap table `button | destination | prototype/OLD`, any OLD = not done. UNBUILT and Verified / Not verified. Run v2 unit tests and tsc. Commit, push to claude/editor-v2-s1c. No PR.
