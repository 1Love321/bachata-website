# Review: Website PR 1865 (editor v2) at 9459bbf57, hard-tier slice

Reviewer: independent, review only. No code changed. Head verified: `9459bbf576b702ce3aea93c990b7ab21893f4632`.

## Verdict: PASS WITH NITS
## Hard-tier: PASS

## Findings

### 1. onboarding-write-doors.json
- `scripts/onboarding-write-doors.json:43-46` OK: `ticket_night_price_set_v1` is a ticket price write (upserts `ticket_type_night_prices`), not an event edit. Read on prod (project stsdtacfauprzrdebmzg, `pg_get_functiondef`).
- The RPC gates itself, in order: `auth.uid()` not null; `_ticket_can_manage_organiser_v1` (else `not_found`); `_ticket_can_handle_money_v1` (else `not_owner_or_admin`, owner seat or platform admin only); `_require_onboarding_complete_v1`. It also refuses wrong night, cancelled, past, non-night and free tickets, and prices outside 1..1000000 pence. EXECUTE is granted to `authenticated` only. Exempting it from the gate list is safe.
- NIT `scripts/onboarding-write-doors.json:44`: the reason text does not say the RPC calls `_require_onboarding_complete_v1` itself. The `ticket_price_step_set_v1` entry says so. Wording only.
- `node scripts/check-onboarding-write-doors.mjs` at the PR head: 0 violations (1476 files, 8 door calls in 4 wrappers). One [WARN], `admin_bulk_occurrence_command_p5` expected-pending, same as on main.
- The "gated lists 10, live guard reports 11" warning did not appear in my run, since the script reads the JSON offline. I did not query the live guard. `git diff origin/main...HEAD` on this file is a single added `not_event_writes` entry. `gated` is unchanged from main (10 names), so any 10 vs 11 drift is pre-existing, not from this PR. Live count NOT verified.

### 2. Money call sites
- `src/modules/organiser/tickets/ticketsApi.ts:333-337` `setNightPrice`: sends integer pence, throws on RPC error, and returns `{ok,reason}` for refusals. No money maths in this layer.
- `ticketsApi.ts:311-330` `parseNightPrices` fails closed: anything but `ok:true` with a tickets array gives `ok:false` and no tickets. `canChange` is true only when the server says `can_change === true`. The server computes that as night open AND paid AND money seat (`ticket_night_prices_v1`).
- A non-owner cannot reach a write. The UI only lets `canChange` fields be edited (`PriceSheet.tsx:127,196`). A manager sees read-only fields, and a direct RPC call is refused with `not_owner_or_admin`.
- `ticketsApi.ts:118-120` `fetchSalesSummary` calls `ticket_sales_summary_v2` and throws on error. Counts-only fields are separated from money fields, which is stripped for non-money seats via `opts.money`.
- Pence: `PriceSheet.tsx:131` uses `Math.round(Number(text)*100)` after the `PRICE_RE` check, then range-checks 1..1000000, the same bounds as the RPC. Rounding absorbs float drift (19.99 gives 1999). The display is `pence/100`.
- NIT `PriceSheet.tsx:131`: a sub-penny entry such as 1.005 rounds to 100 silently. Check the `PRICE_RE` decimal places to confirm it is limited to 2.
- Failed write: `PriceSheet.tsx:254-266` `writeAll` writes one ticket at a time. On the first failure it reverts the earlier writes (best effort), calls `refreshNight()` (invalidates `ticketKeys.all`), and returns an error line mapped by `nightPriceLine` (`night_past`/`night_cancelled` to "gone", `not_owner_or_admin` to "locked", else "not saved"). The shown price comes from the refetch, not an optimistic cache, so no stale price stays on screen. No fake success: the toast only fires when `r.ok`.
- NIT `PriceSheet.tsx:260`: if a revert call fails it is swallowed. The user sees "not saved" while one ticket may have kept the new price until the refetch lands. The refetch corrects the display, and the message is not false, but it does not say the change was partial. Low risk.
- Undo (`PriceSheet.tsx:269-276`) restores from `byId` captured before the save (the pre-save night price), which is correct. An undo that hits a past night is refused by the RPC and shows `UNDO_FAILED_TEXT`.
- NIT `PriceSheet.tsx:131`: `PRICE_RE` is required first, so NaN cannot get through, but the guard `pence < 1 || pence > 1000000` is not NaN-safe by itself. This is defence in depth only.
- Not walked: `TicketsV2.tsx` and `NightCard.tsx` contain no price write. A grep finds `setNightPrice` only in `PriceSheet.tsx`. They read sales and money via `fetchSalesSummary`.

### 3. vite.chunks.ts
- `vite.chunks.ts:397-402` is one list entry with a comment. `tests/chunkClassifier.test.ts:208` matches it, with the entry appended last.
- `src/lib/parseVenueVideoUrl.ts` is a pure leaf: it has no `import` statements, uses module-level regexes and pure functions, and touches no `window`/`document`. Pinning it into `lib-event-rules` adds no heavy dependencies to other routes. It is a few KB and `/event` already loads that chunk.
- I did not run a production build or the chunk ratchet. The 39 vs 40 request counts in the comment are the author's claim and NOT verified.

### 4. Flag organiserEditorV2
- `src/lib/featureFlags.ts:96`: `=== 'true'` on `VITE_ENABLE_ORGANISER_EDITOR_V2`. It is off unless the env var is exactly `true`.
- `src/modules/organiser/p3/shell/P3Routes.tsx:112-116`: the `/tickets` and `/night/:occurrenceId` routes mount only under `flags.organiserEditorV2 &&`. With the flag off, `events/:seriesId` and `/edit` render the old R4 screens, and `v2KeepsDraft` defaults to the flag value (`v2/scope.ts:15`).
- I found no nav entry for the v2 tickets route outside `p3/v2/`. Existing `eventTickets` paths use `?tickets=1` on the old event route. `routes.flag.test.tsx` exists for flag on and off, but I did not run the vitest suite.
- Not verified: the actual value of `VITE_ENABLE_ORGANISER_EDITOR_V2` in the Vercel environments.

## Not verified
- Live guard `gated` count (10 vs 11).
- Production build, chunk ratchet, vitest and tsc on the PR head.
- No browser walk (out of scope per the brief).
