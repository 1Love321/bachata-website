# Review: Website PR 1865 (editor v2) at head 9459bbf57, hard-tier slice

Branch: claude/review-editor-v2-1865
Size: 1 files, 120 lines
Design: n/a (review only, no UI change)

You are the INDEPENDENT reviewer. Review the Website PR 1865 (`claude/editor-v2-s1c`, head `9459bbf57`) for the hard-tier slice only. Write ONE review doc `docs/review/pr-1865-editor-v2-9459bbf57.md` on your branch. Do not edit code, do not merge, do not approve.

## What changed at this head (the merge work)
The owner approved the preview of the feature. Since then three small commits were added on top of main:
1. `scripts/onboarding-write-doors.json`: `ticket_night_price_set_v1` added to `not_event_writes` (guard list, hard-tier path).
2. `vite.chunks.ts` + `tests/chunkClassifier.test.ts`: `/src/lib/parseVenueVideoUrl.ts` pinned into `lib-event-rules` (build config).
3. Two merges of `origin/main`.

## Review targets (read these, report findings with file:line)
1. `scripts/onboarding-write-doors.json`: is the new entry correct (the RPC really is a ticket price write and not an event edit; is it gated by a permission inside the RPC, so exempting it from the onboarding gate list is safe)? Run `node scripts/check-onboarding-write-doors.mjs`: expect 0 violations. The guard also warns that `gated` lists 10 doors while the live guard reports 11: is that pre-existing on main (compare `git show origin/main:scripts/onboarding-write-doors.json`)? Say so either way.
2. Money-adjacent call sites added by the PR in `src/modules/organiser/tickets/ticketsApi.ts` (`setNightPrice`, `fetchNightPrices`, `parseNightPrices`, `fetchSalesSummary` now calling `ticket_sales_summary_v2`) and the UI that calls them (`src/modules/organiser/p3/v2/PriceSheet.tsx`, `TicketsV2.tsx`, `NightCard.tsx`): can a non-owner reach a price write; are amounts handled in pence with no float drift; does a failed write leave a stale price shown; are errors from the RPC shown honestly (no fake success)? The RPCs are already live on prod; read-only prod checks are allowed (project `stsdtacfauprzrdebmzg`, counts only, never any write).
3. `vite.chunks.ts`: the pin is one list entry with a comment; confirm it is a pure leaf (no side effects, no heavy imports) so pinning it costs no bytes on other routes.
4. The flag `organiserEditorV2` in `src/lib/featureFlags.ts` is off by default and nothing v2 is reachable with it off (route, nav entry).

## Not in scope
The other ~160 files are UI behind the flag and were walked at 390x844 (129 pass). Do not review styling or copy.

## Output (merge gate parses only these)
Your review doc MUST contain, as separate heading lines, `## Verdict: PASS` (or `PASS WITH NITS` or `BLOCK`) and `## Hard-tier: PASS` (or `## Hard-tier: FAIL` with the reason). Findings below, one line each with file:line. Push the doc to your branch.
