# Brief: Nights ticket QR, slice S1 (invisible): tickets parser keeps event_id and occurrence_id
Repo: 123constante/bachata-website. Branch: claude/nights-ticket-qr-s0-ids. Lane: normal. Model: sonnet.
Size: 3 files, 45 lines
Brief-version: 1 (2026-11-22)

Invisible slice: no flag, no screen change, nothing reads the new fields yet. The UI slice is `docs/briefs/nights-ticket-qr.md` (S2) and needs this merged first.

## What main lacks
RPC `my_tickets_v1` sends `occurrence_id` and `event_id` but `parseMyTickets` drops both. The Nights row needs them to match a ticket to a night. Prod check: the RPC's `event_id` equals the public event id (`COALESCE(legacy_event_id, id)` for all 772 series); `MyTicket.seriesId` (= `event_series_id`) is NOT that for 120 of 772 series, so do not reuse it.

## Files
1. `src/modules/tickets/myTicketsModel.ts`: add `eventId: string | null` (`str(r.event_id)`) and `occurrenceId: string | null` (`str(r.occurrence_id)`) to `MyTicket` and to `parseMyTickets`. Nothing else changes. Files over 2 KB go through `scripts/safe-edit.py`.
2. The existing parser test under `src/modules/tickets/__tests__/` that covers `parseMyTickets` (find it with grep): add two cases: ids present map through; ids absent on an old reply = null. Fix any fixture that builds a full `MyTicket` literal so tsc stays green.
3. `scripts/dancer-area-isolation-allowlist.json`: ONLY if `node scripts/check-dancer-area-isolation.mjs` goes red; then add `src/modules/tickets/myTicketsModel.ts` to `seams` with a `seamNotes` line "nights-ticket-qr: the two ids the Nights row needs to match a ticket to a night". If green, leave the file alone (then the PR is 2 files).

## Rules
HTML entities rule not relevant (no JSX). No migrations in this repo. Never log the RPC reply.

## Gates
`npm run test:unit` (tickets tests), `npm run lint`, `npx tsc --noEmit`, `node scripts/check-dancer-area-isolation.mjs`, `npm run pre-ship`.

## Tap table
None: no screen. State "no UI change" in the handover.

## Done when
Parser test green, tsc green, no other file touched. Open one PR, do not merge. Handover lists anything noticed and not changed.
