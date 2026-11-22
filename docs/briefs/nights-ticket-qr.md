# Nights: QR button on a Going row that has a ticket (mockup option C)
Repo: 123constante/bachata-website. Branch: claude/nights-ticket-qr. Lane: normal. Model: sonnet.
Size: 4 files, 390 lines (slice S2, UI). Prerequisite web slice S1 (ids): `docs/briefs/nights-ticket-qr-s1-ids.md`, must be MERGED first (branch from main after it lands; `MyTicket.eventId` / `.occurrenceId` must exist). DB slice (admin PR 123constante/bachata-admin#2139) is separate and may land later.
Brief-version: 2 (2026-11-22)
OWNER ANSWERS 2026-11-22 (binding, override anything below): (1) QR opens in a BOTTOM SHEET as described under Build notes. (2) Buying a ticket MARKS the buyer Going (DB slice writes the `going` row), so a ticket-only night appears in `get_my_going_v1` once the DB slice is applied; web needs no client-made row. (3) A series-wide ticket counts as Going for the NEXT night only (matches the `ticketsForNight` rule). No backfill of old orders.
Design: DESIGN-REGISTRY line 87 "DANCER NIGHTS TICKET CHIP" (owner clicks 2026-11-22, option C picked; owner said "build this" 2026-11-22). Mockup: https://claude.ai/artifact/2RSEhzN5DhJdTU3ebuWR1H, phone "After C".
Flag: new `VITE_ENABLE_NIGHTS_TICKET_QR`, default off. OFF = Nights byte-for-byte as today, no ticket read, no new chunk loaded.
Rules: HTML entities in JSX (`&mdash;`, `&rsquo;`, `&hellip;`), explicit px sizes (html font-size is about 13.5px on phones), 390x844 first, `.claude/rules/design-density.md`. Calendar files are density-excluded: do not touch. No migrations in this repo. Source files over 2 KB go through `scripts/safe-edit.py` / `scripts/safe-write.py`.
CHECKPOINT: push and open the PR as soon as the flag-off test and the "both" shape pass (about 60% context), then continue.

## What main lacks
- Row component: `NightRow` in `src/modules/dancer-app/screens/nights/NightsScreen.tsx` (lines 58-99). `<li class="flex items-start gap-[10px] py-[10px]">` = `DateBlock` + main column (name link, `StatusPill`, place/time line, note, `LevelVote`).
- Going data: `nightsGoing.tsx` (lazy feed) -> `useMyNights` -> `useMyGoing` -> RPC `get_my_going_v1`. Mapping: `nightsModel.ts` `mapMyGoing`. `Night.occurrenceId` = occurrence id, `Night.seriesId` = `public_event_id`, `Night.date` = `YYYY-MM-DD`.
- `get_my_going_v1` reads ONLY `event_attendance` rows with `status = 'going'`. A ticket purchase writes no attendance row. So a ticket-only night is NOT in the Nights list today.
- Ticket data: RPC `my_tickets_v1` via `fetchMyTickets()` (`src/modules/tickets/myTicketsApi.ts`), parsed by `parseMyTickets` (`src/modules/tickets/myTicketsModel.ts`). The RPC sends `occurrence_id` and `event_id` but the parser DROPS both. `MyTicket.seriesId` is `event_series_id`, which is NOT the public event id for 120 of 772 series.
- Prod check (counts only): `public_event_id = COALESCE(legacy_event_id, id)` for all 772 series, so the RPC's `event_id` equals `Night.seriesId`.
- QR drawing: `src/modules/tickets/TicketQrCode.tsx` (default export, prop `token`). Must only be imported by a lazy chunk.
- Ticket status (one shared mapping): `src/modules/tickets/ticketCardStatus.ts` (`ticketStatusAt`, `ticketQrShown`).
- Door code line: `src/modules/tickets/DoorCodeLine.tsx` + `doorCodeText` (`myTicketsModel.ts`) + `ticketDoorCode` (`src/modules/organiser/p3/door/doorFlags`). Full weekday date: `doorDate` in `src/modules/dancer-app/screens/tickets/cardKit.ts`. Pager helpers: `ticketCountLine`, `stepTicket` (same file).
- Bottom sheet: `Sheet` in `src/modules/dancer-app/screens/options/optionsUi.tsx` (SheetView: focus trap, Escape, own "Close" button).
- Flag precedent inside the dancer-app folder: `src/modules/dancer-app/screens/home/layoutCFlag.ts` / `dashBFlag.ts`.
- Nights itself only renders when `GOING_CHECKIN_ENABLED` is on (Vercel preview + `VITE_ENABLE_GOING_PREVIEW=true`; off in production). This feature sits under it.
- Isolation guard: `scripts/check-dancer-area-isolation.mjs`. A changed file outside `src/modules/dancer-app/**` that is not a test must be a seam in `scripts/dancer-area-isolation-allowlist.json`.

## Split
The ids slice (`MyTicket.eventId` / `.occurrenceId`) is brief `nights-ticket-qr-s1-ids.md` and is merged before this one. This brief touches only dancer-app files plus tests; no allow-list edit expected (if `check-dancer-area-isolation.mjs` goes red, stop and tell the orchestrator).

## Files
1. `src/modules/dancer-app/screens/nights/nightsTicket.ts` (new, pure; type-only import of `MyTicket`; value imports only from `@/modules/tickets/ticketCardStatus`)
2. `src/modules/dancer-app/screens/nights/NightTicketQr.tsx` (new, default export, loaded with `lazyWithRetry`; the ONLY new importer of `TicketQrCode` and `fetchMyTickets`)
3. `src/modules/dancer-app/screens/nights/NightsScreen.tsx` (edit: `NightRow` and `NightsView`)
4. `src/modules/dancer-app/screens/nights/__tests__/nightsTicketQr.matrix.test.tsx` (new)
Do not touch: `nightsGoing.tsx`, `useMyNights.ts`, `nightsModel.ts`, `nightsUi.tsx`, `src/modules/going/**`, `screens/tickets/**`, shell, routes, Home, Me, any calendar file.

## Data contract (no migration)
- Read: `fetchMyTickets()` once per Nights mount, only when the flag is on AND the going list has loaded with at least one upcoming row. `signed_out`, `error` or zero tickets = no button anywhere, no message, Nights exactly as today. Never log the reply; the `qrToken` stays in component state and the `TicketQrCode` prop.
- Match (pure, `ticketsForNight(night, tickets, now)` in file 1): a ticket belongs to a night when `ticket.occurrenceId === night.occurrenceId`, OR `ticket.occurrenceId === null && ticket.eventId !== null && ticket.eventId === night.seriesId && ticket.date !== null && ticket.date === night.date` (series-wide ticket: the RPC dates it to the next scheduled night). Never match on title.
- Shown tickets: only those with `ticketQrShown(ticketStatusAt({ date, eventCancelled: nightCancelled, refunded, checkedIn }, now))` and a non-null `qrToken`. Used, refunded, passed and cancelled-night tickets give no button (the existing ticket rule).
- Button rule: an UPCOMING row with `night.status !== 'cancelled'` and at least one shown ticket. Past rows never get a button.
- Flag const in file 1: `export const NIGHTS_TICKET_QR_ENABLED: boolean = import.meta.env.VITE_ENABLE_NIGHTS_TICKET_QR === 'true';` (read at render in `NightsView`, like `GOING_CHECKIN_ENABLED`, so tests can flip it with a `vi.mock` getter).

## Build notes
- `NightRow` gets one optional prop `onQr?: () => void`. When set, render after the main column, as the last child of the `<li>`:
  `<button type="button" data-testid="night-qr" aria-label={`Show ticket QR code for ${night.name}`} onClick={onQr} className="grid h-[44px] w-[44px] shrink-0 place-items-center self-center rounded-[12px] border border-[var(--line)] bg-[var(--card)] text-[var(--fg)]">` with lucide `QrCode` at 22px, `aria-hidden`. Check the two CSS variable names against `nightsStyles.ts` / `theme/dancer-color.css` and use the ones the row already uses.
- No other class, order or copy in `NightRow` changes. With `onQr` undefined the rendered HTML is identical to today.
- `NightsView`: state `ticketsByNight: Map<occurrenceId, MyTicket[]>` and `qrFor: NightRowData | null`. Mount `<Suspense fallback={null}><NightTicketQr .../></Suspense>` only when the flag is on and `nights.upcoming.length > 0`.
- `NightTicketQr` (one lazy chunk, two jobs): (a) fetch once, hand `Map` back through `onTickets` (the `nightsGoing.tsx` feed pattern, render nothing); (b) when `qrFor` is set, render the sheet.
- Sheet (dancer-app `Sheet` from `optionsUi`, `testId="night-qr-sheet"`): title = night name; line 1 = `doorDate(night.date)` (full weekday); line 2 = ticket type name (`typeName ?? 'Ticket'`); the QR inside a white padded box, width `min(88vw, 340px)`; `DoorCodeLine` when `doorCodeText(shortCode, ticketDoorCode)` is non-null; when the night has more than one shown ticket, prev/next 44px buttons and `ticketCountLine` ("Ticket 2 of 3") using `stepTicket`; the sheet's own Close button. No buyer name, no email, no price.
- The row can grow by nothing: the button is 44px tall, the row is already at least 44px + 20px padding. Long names wrap in the main column (`min-w-0 flex-1` stays).

## Prototype checklist (mockup "After C"; every element and tap)
- [ ] Top bar: "&larr; Home" left, "Switch" right (shell, unchanged)
- [ ] Title "Nights"
- [ ] Section heading "Coming up" (text comes from `GOING_COPY.myUpcomingTitle`; keep the live words, do not retype)
- [ ] Upcoming row 1 (Going + ticket): date block "Sat 28 Nov" violet | name "Salsa &amp; Bachata Social" | pill "Going" teal | line "Saturday 28 November &middot; Rhythm Rooms, London" | NEW 44x44 QR icon button on the right, vertically centred, 1px line border, 12px radius, 22px icon, no word on it
- [ ] Tap QR button -> the ticket's QR opens (sheet). Tap Close / scrim / Escape -> back on Nights, same scroll position
- [ ] Tap the night name -> `/event/:id` (unchanged; the button is not inside the link)
- [ ] Pill stays "Going" on the ticket row (no "Ticket" word, no chip)
- [ ] Upcoming row 2 (Going, no ticket): date block "Wed 2 Dec" gold | "Wednesday Bachata" | "Going" | place line | NO button, identical to today
- [ ] Section heading "Past nights" (from `GOING_COPY.myPastTitle`)
- [ ] Past row: "Sat 14 Nov" | "Bachata Sensual Night" | pill "Went" violet | place line | level vote box | NO button
- [ ] Bottom tabs as the shell draws them today (the mockup shows Home / Nights / Me, which is layout C; tabs are out of scope)
- [ ] The pink dashed outline and "new" tag are mockup markers, not built
- [ ] Sheet contents: NOT DRAWN in the mockup (see Ambiguities 1)

## KEEP-LIST (live Nights; every item must be present and unchanged with the flag on)
- Back to Home link top left; Switch top right (shell)
- "Nights" screen title
- "Loading your nights&hellip;" status line
- Error card "We couldn&rsquo;t load your nights." + "Try again"
- Whole-screen empty state: moon art, title, how line, find-a-night line
- Upcoming heading + card; upcoming-empty sentence when only past nights exist
- Past heading + card; past-empty sentence
- Date block: 3-letter weekday, day, month, weekday tint, "TBC" block, spoken label with full weekday
- Night name: link to `/event/:id` (min height 44px), plain text when the night has no public event
- Status pill: Going (teal), Went (violet), Cancelled (rose)
- Place and time line with full weekday; "Place to be confirmed" fallback
- Note line (`night-note`): cancelled / not-live words
- Level vote on the first past row of each series (ended, listed, has a series id)
- Focus outline + scroll for `?focus=<seriesId>`
- `data-testid`, `data-series`, `data-status`, `data-focused` attributes on the row
- Going switch off -> `NightsOff`, no going code, no ticket code loaded
- Bottom tabs

## Test plan
`nightsTicketQr.matrix.test.tsx`, table-driven, same harness as `nightsAudit.test.tsx` (mock `@/lib/goingCheckin`, the supabase client `rpc` answering `get_my_going_v1`, `my_tickets_v1`, `series_level_summary_p5_v1`; fixtures from `goingShapes.ts`). Shape matrix, each x flag on/off:

| Shape | Going row | Ticket | Expect (flag on) |
|---|---|---|---|
| going only | upcoming | none | row as today, no `night-qr` |
| both, night ticket | upcoming | valid, `occurrence_id` = night | pill "Going", one `night-qr`; tap -> sheet with name, full-weekday date, type, `ticket-qr`; Close -> sheet gone |
| both, series-wide ticket | upcoming | valid, `occurrence_id` null, `event_id` = public id, same date | as above |
| both, 3 tickets | upcoming | 3 valid | one button; sheet shows "Ticket 1 of 3", next -> "Ticket 2 of 3", wraps |
| both, used | upcoming | `checked_in` true | no button |
| both, refunded | upcoming | `refunded` true, `qr_token` null | no button |
| ticket only | none (DB slice marks Going) | valid | Going row from the RPC (fixture returns it), pill "Going", one `night-qr` |
| neither | none | none | empty state unchanged |
| past | past row | ticket dated the past night | "Went", level vote, no button |
| cancelled | upcoming, `is_cancelled` | `night_cancelled` true | "Cancelled" pill + note, no button |
| ticket for another date of the same series | upcoming | valid, other date | no button |
| same title, different event | upcoming | valid, other `event_id` | no button |
| tickets read fails / signed out | upcoming | RPC error / 42501 | no button, no error text, list intact |
| many | 40 upcoming / 100 past | 500 tickets (RPC cap) | renders, buttons only on matches |

Also assert: flag off -> `my_tickets_v1` never called and the Nights HTML equals the flag-on "going only" HTML; flag on with zero upcoming rows -> `my_tickets_v1` never called; `nightsAudit.test.tsx` passes untouched; the `qr_token` string never appears in the DOM as text or attribute; every row button is at least 44x44 by class; the sheet's Close control exists (no copy points at a missing control).
Pure tests for `ticketsForNight` (the match table above without React).
Gates to run locally: `npm run test:unit`, `npm run lint`, `npm run build` + `npm run check:bundle-budget` (the `/app/*` first-load row must not grow by the QR generator: `qr.ts` must sit only in the lazy chunk), `node scripts/check-dancer-area-isolation.mjs`, `npm run pre-ship`.
Walk (orchestrator, preview build with `VITE_ENABLE_GOING_PREVIEW=true` and `VITE_ENABLE_NIGHTS_TICKET_QR=true`, 390x844): prod has 0 people with both a Going tap and a ticket on the same night, 1 signed-in buyer (2 series-wide tickets), 30 of 33 tickets are guest orders. The walk account must tap Going and take a free ticket on the same night, one night ticket and one series-wide.

## TAP TABLE (mandatory in handover)
| Button | Destination | prototype/OLD |
|---|---|---|
| QR button on a Going + ticket row | QR sheet for that night | prototype |
| Sheet Close / scrim / Escape | Nights, same place | prototype (sheet not drawn) |
| Sheet prev / next (2+ tickets) | next ticket of the same night | not in prototype (see Ambiguities 1) |
| Night name | `/event/:id` | prototype (unchanged) |
| Level vote | vote saved (unchanged) | prototype (unchanged) |
| Try again (error card) | re-read (unchanged) | keep-list |
| Home link, Switch, tabs | unchanged | keep-list |

## Cannot be built in this slice (stop; tell the orchestrator, do not improvise)
1. Nights is itself off in production (going switch). The button can only be seen where the going switch is on.
2. The ticket-only Going row comes from the DB slice (admin PR #2139). Do not build a client-made row; the fixtures simulate the RPC reply.

## Ambiguities (listed, none block the build)
3. A used (checked-in) ticket gets no button, by the existing rule "used = QR hidden". Listed, not asked.
4. The button appears a moment after the list (second read). No skeleton is drawn in the mockup; none is built.
5. Mockup tabs (Home / Nights / Me) and the heading "Coming up" belong to layout C / the live copy; this slice changes neither.

## Done when
Every checklist box is ticked or named under "Cannot be built"; the matrix test and `nightsAudit.test.tsx` are green; flag off is byte-identical; the tap table has no OLD row; the handover lists anything noticed and not changed. Do not merge.
