# Brief: Editor v2 slice 17e, money gaps (per-night ticket price write, per-date sold, Stripe poll again)

Branch: claude/editor-v2-s1c
Size: 5 files, 330 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations here; no admin change (the DB half is live, see below).
Design: owner-picked FROZEN prototype (DESIGN-REGISTRY rows 61-81), `~/.claude/plans/editor-v2/frozen/event-editor-c2-prototype.FROZEN-2026-11-21.html` (big file: grep `priceDone`, `nightPrice`, `backToUsual`, then Read with offset/limit around lines 481-520), `checklist.md` SH-PRICE-10..12, `tap-table.md` rows 10, 53, 54, 55. Prototype wins. Read `docs/briefs/editor-v2-slice17d-money-review-fixes.handover.md` on this branch first (its UNBUILT list is this brief). Owner approved building all three items.

## Files (touch only these; max 5)
- src/modules/organiser/tickets/ticketsApi.ts (night-price wrappers; parse per-night sold)
- src/modules/organiser/p3/v2/PriceSheet.tsx (tickets state editable; `NightPriceSheet` reads + writes)
- src/modules/organiser/p3/v2/TicketsV2.tsx (perDate sold ~line 186; `ret.again` ~line 204 and the warn at ~line 84)
- src/modules/organiser/p3/v2/copy.ts (only the new lines needed: Check again label, price error lines; no other edit)
- src/modules/organiser/p3/v2/__tests__/slice17e.test.tsx (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only. If NightCard.tsx must change, STOP and say so in the handover (6th file = over limit; the sheet derives `changed` itself, see 1d).

## Ground truth (read, do not guess)
Item 1 write RPCs EXIST and are LIVE on prod (admin PR #2114, migration `20261121220001_editor_v2_db_e_night_price_v1.sql`; prod `stsdtacfauprzrdebmzg` checked: both functions present, ACL `{postgres=X/postgres,authenticated=X/postgres}`). Not yet in `src/integrations/supabase/types.ts`: call through the existing `db().rpc(...)` style in ticketsApi.ts (cast the name if the typed client rejects it; do NOT hand-edit types.ts).
- Write: `ticket_night_price_set_v1(p_ticket_type_id uuid, p_occurrence_id uuid, p_price_pence integer) returns jsonb`. `p_price_pence` null = remove the override ("Back to the usual prices"). Owner money seat only (a manager is refused). Returns `{ok, ticket_type_id, occurrence_id, night_price_pence, changed, price_pence}`; refusals `{ok:false, reason}` with reason one of `invalid_payload, not_found, not_owner_or_admin, wrong_night, not_night_ticket, night_cancelled, night_past, not_paid_ticket, invalid_price` (price floor 1..1000000 pence, so 0 is refused: a free night is not a night price). Run it through the existing `writeResult` parse (`{ok, reason}`).
- Read: `ticket_night_prices_v1(p_occurrence_id uuid) returns jsonb`: `{ok, occurrence_id, night_open, tickets:[{ticket_type_id, name, currency, status, usual_price_pence, night_price_pence|null, price_pence, can_change}]}`. `usual_price_pence` is the base or live step; `night_price_pence` null = no override; `can_change` = money seat + paid + night open. Reasons `invalid_request`, `not_allowed`.
- Public and checkout already charge the night price through the one price seam; nothing else to wire. Shown = charged.
- Per-date sold: `ticket_sales_summary_v2(p_organiser_id uuid)` (live, ACL authenticated) is `v1` plus a `nights` array per type: `{occurrence_id, occurrence_date, tickets_sold, reserved_count, orders_*, ...}`; counts only for a non-money seat. Baseline source: admin repo `supabase/migrations/20261118000000_baseline_prod_schema_v2.sql` lines 92395-92507 (read only; confirm the top-level keys `ok`, `ticket_types`, `money_visible` match v1 before switching).

## What to build (decided; do not ask)
1. Per-night ticket price WRITE (PriceSheet tickets state, SH-PRICE-10..12, taps 53, 54).
   a. ticketsApi.ts: `setNightPrice(typeId, occurrenceId, pence | null): Promise<WriteResult>` and `fetchNightPrices(occurrenceId)` with a parser (fail closed: not ok = unknown). Add a `ticketKeys` entry for it.
   b. `NightPriceSheet` (state `tickets` only): read `ticket_night_prices_v1(date.id)`; show each paid ticket's field value = `night_price_pence ?? usual_price_pence` in pounds; free ticket stays the "<name>: Free" line. While that read is loading or failed, the inputs are read-only and Done only closes (never edit on an unknown price). A ticket with `can_change` false (manager, night past/cancelled) stays read-only with no Done write.
   c. Inputs editable (remove `readOnly`). Done, as the prototype `priceDone`: validate each changed field with `PRICE_RE`; empty or unchanged = no write; nothing changed = close. For each changed ticket call `setNightPrice(id, date.id, pence)` one at a time (a second tap while running is ignored, same `inFlight` rule as door). A field entered as 0 or below the floor shows the existing bad-price line; never sends 0.
   d. Partial failure: if ticket 2 of 2 fails, put ticket 1 back (call with its previous night price, null if it had none), show "Not saved. Try again." and keep the sheet open. Never a false "Saved".
   e. Success: close, toast the existing `savedNight`/one-off `savedOne` line with Undo; Undo writes each ticket's previous night price back (null if none). Invalidate the night-prices read and `ticketKeys.all` so the Night card and Tickets screen show the new price.
   f. "Back to the usual prices" (`P.backTickets`): shown when any ticket has a night price (derive from the read; keep `p.changed` as an OR). Tap: `setNightPrice(id, date.id, null)` for every ticket that has one; close only if all succeed; on failure show the line and stay. Toast "Back to usual for <date>" + Undo (Undo restores each night price).
   g. Refusals map to one plain line each: `night_past`/`night_cancelled` = "This night has gone.", `not_owner_or_admin` = the existing `P.locked`, everything else = `P.notSaved`. Add copy lines to copy.ts; wording from the prototype if it has one, else these.
2. Per-date sold for series-wide tickets (TicketsV2.tsx ~186).
   a. ticketsApi.ts: `fetchSalesSummary` calls `ticket_sales_summary_v2`; `parseSummary` gains `nights: {occurrenceId, ticketsSold}[]` per `TypeSales` (counts only, so the money-stripping filter is unaffected; a missing `nights` = `[]`). Existing parse tests must still pass.
   b. TicketsV2.tsx `perDate`: night-bound ticket unchanged (`t.occurrenceId === d.id ? sold : 0`); series-wide ticket (`occurrenceId` null) = that type's `nights` entry for `d.id`, else 0. A refused or failed summary keeps today's behaviour (fail closed, no invented counts).
3. Stripe poll `ret.again` (TicketsV2.tsx ~204).
   a. When `stripeWarn` (poll timed out, still not connected), show a "Check again" button under the warn line (data-testid `tk-check-again`, `btn sec`, label from copy.ts, wording the same as GetPaid's "Check again") that calls `ret.again()`. It restarts the 30 s poll (phase back to checking, warn hides until the next timeout). Disabled while `stripeBusy`.

## Checklist (every item is a test in slice17e.test.tsx that FAILS on the 17d code)
- [ ] 1b tickets state, night price read ok: a ticket with night_price_pence 1200 and usual 1000 shows 12 in its field; input is NOT readOnly
- [ ] 1b read still loading or `ok:false`: inputs read-only, Done closes, no RPC call
- [ ] 1c edit one field 10 -> 12, Done: `ticket_night_price_set_v1` called once with (type id, night id, 1200); sheet closes; toast with Undo
- [ ] 1c empty or unchanged field: no RPC call, sheet closes
- [ ] 1c entered 0 or "abc": bad-price line, no RPC call, sheet stays open
- [ ] 1c double tap on Done: one RPC call only
- [ ] 1d two tickets changed, second RPC returns `{ok:false}`: first ticket is written back (second call set with its old night price / null), "Not saved. Try again." shown, sheet open
- [ ] 1e Undo after success calls the RPC with the previous night price (null when there was none)
- [ ] 1f "Back to the usual prices" shown when a night price exists, hidden when none; tap calls RPC with null per overridden ticket; one failure keeps sheet open with the line
- [ ] 1g `night_past` returns the "This night has gone." line; `not_owner_or_admin` returns `P.locked`
- [ ] 1b `can_change` false: field read-only, no write on Done
- [ ] 2a parseSummary reads `nights`; money-hidden summary still has no pence keys; missing `nights` = []
- [ ] 2b series-wide ticket, summary nights `[{d1: 3}, {d2: 5}]`: Tickets screen perDate shows 3 and 5 (old code shows 0 0); night-bound ticket unchanged; failed summary shows 0
- [ ] 3a poll timed out, not connected: "Check again" button present; tap calls `ret.again` (refetch fires again, warn clears while checking)
- [ ] 3a connected or still polling: no "Check again" button
- [ ] Existing v2 tests all green (priceSheet.test.tsx's read-only tickets case is updated, not deleted: it now asserts read-only only while the read is unknown)

## Handover
`docs/briefs/editor-v2-slice17e-money-gaps.handover.md`. Tap table `button | destination | prototype/OLD`; any OLD row = not done, no "remaining differences" list. Rows to include at least: Price sheet Done (tickets), Back to the usual prices (tickets), Undo on each toast, Check again, Tickets per-date line, Change the usual price / Add your first ticket (unchanged links). Sections UNBUILT and Verified / Not verified (say plainly: the live RPC was not exercised against prod, no writes). Run `npm run test:unit -- src/modules/organiser/p3/v2 src/modules/organiser/tickets` (v2 + ticket unit tests) and `npx tsc --noEmit -p .`, `npm run check:mojibake`. No secrets. Commit, push to claude/editor-v2-s1c. No PR.
