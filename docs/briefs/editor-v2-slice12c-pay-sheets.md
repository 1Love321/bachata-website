# Brief: Editor v2 slice 12c, pay sheets on the Tickets screen + one-off Tickets row

Branch: claude/editor-v2-s1c
Size: 5 files, 380 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81 (embedded below).

Read first: `docs/briefs/editor-v2-slice12a-tickets-screen.handover.md` and `docs/briefs/editor-v2-slice12b-open-tickets.handover.md` (on this branch). `TicketsView` already renders every button with an optional `on` handler map (`stripe|ticket|newTicket|sellHere|payDoor|link|doorUsual|howPay`); this slice gives handlers to `sellHere`, `payDoor`, `link`, `doorUsual`, `howPay` and adds the one-off Tickets row. NOT in this slice (12d): real Stripe (`stripe`), the "Switch how dancers pay?" sheet when tickets are sold, ticket card sheets (`ticket`, `newTicket`, slice 13).

## Files (touch only these)
- src/modules/organiser/p3/v2/PaySheets.tsx (new): `DoorSheet` (SH-DOOR), `LinkSheet` (SH-PAY-04), `HowPaySheet` (SH-PAY-05/06), all on `V2Sheet`.
- src/modules/organiser/p3/v2/TicketsV2.tsx: in `TicketsLoaded` own the sheet state and the writes, pass the `on` map to `TicketsView`; render the toast with Undo the same way `EditorV2` does (reuse `V2Toast`, `useSaveNow(series.id, series.version)` from saveNow.ts, `savedLabel`/`SAVED_TEXT` conventions; read how EveryBody calls `saveNow(steps, before, label)` and copy that shape). `TicketsView` stays pure.
- src/modules/organiser/p3/v2/NightCard.tsx: one-off row (tap 27), see below.
- src/modules/organiser/p3/v2/copy.ts: new strings only (add a `tickets` block).
- src/modules/organiser/p3/v2/__tests__/paySheets.test.tsx (new).
Do NOT edit payMode.ts, editorModel.ts, saveNow.ts, Facts.tsx, shared/, EveryBody.tsx. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only (`&rsquo;`).

## Behaviour (decisions already made; do not ask)
- Writes go through `series.upsert` (always send `name`), target `series`, id `ws.series.id`, version `ws.series.version`, with an inverse so Undo works (see `undoPlan`). Keys: `default_passes` (door price), `default_ticket_url` (link).
- **Door price** (SH-DOOR, tap 99/100): `doorPriceOf(series.passes)` is the first price-list entry. Done: empty or unchanged -> close, no save. Else write `default_passes` = the existing list with the FIRST entry's price replaced (keep every other field and entry); if the list is empty create ONE entry named "Entry" with that price. Use `ownerPassesFromStored` / the payload shape in shared/seriesCommands.ts (line ~56, ~214). Toast "Saved for every <day>" (weekly, day = `day` prop already in TicketsLoaded) or "Saved" (one-off), with Undo (before = old `default_passes`). Hint line weekly only: "This is the price for every <day>. Nights you changed keep their own price."
- **Link sheet** (SH-PAY-04, tap 97/98): field "Where dancers buy", placeholder `https://`, error "Add a link" when empty (error hidden otherwise), Done writes `default_ticket_url` and also pauses nothing. Toast "Dancers buy at your link" + Undo (before = old url).
- **Pay at the door** (tap 94, NOTHING sold): `payModeOf` says `here` when any ticket type is `on_sale`, so to reach door mode: clear `default_ticket_url` (set null) AND pause every on_sale type with `setTicketStatus(id, 'paused')` from ../../tickets/ticketsApi (zero sold, so safe). Toast "Dancers pay at the door" + Undo (restore url and re-open the paused types with `setTicketStatus(id, 'on_sale')`). Refetch the tickets reads after (`useSeriesTypes` etc.; use their refetch/invalidate the way slice 12b/useStripe do). If ANY ticket sold on this series (sum of `sold` over the rows): do NOT switch; leave `payDoor` as a no-op (12d builds the "Switch how dancers pay?" sheet) and say so in the handover. Same guard for `link` when mode is `here` and something sold: no-op.
- **Sell tickets here** (tap 93, door or link mode): Stripe is already connected here (screen only shows the button in that case). Re-open every paused type (`on_sale`); clear nothing else. If the series has NO types, keep a local `sellingHere` flag so the screen shows the selling state "No tickets yet. Add your first ticket." (T-08) until a ticket exists (slice 13 adds tickets). Toast "Selling tickets here" + Undo (re-pause what was opened / clear the flag).
- **How dancers pay** (tap 101, SH-PAY-05/06): sheet text "Or tap Sell tickets here to sell on Bachata Calendar again."; buttons: "Pay at the door" (secondary; HIDDEN when mode is door) runs the pay-at-door action; "Change the link" if a link exists else "Add a link" opens the Link sheet.
- Inline "add a link" (tap 97, footer in selling state) opens the Link sheet directly.
- **Tap 27, one-off row**: in `NightCard`'s one-off body add a row "Tickets" with sub line "Prices and how dancers pay" that calls `onOpenTickets` (prototype `factLn('goTickets','Tickets','Prices and how dancers pay')`, same look as the other fact lines in that body; keep every existing row and badge; weekly night cards unchanged). If the one-off body is rendered by NightHub instead, add it there and list the file in the handover (that is the 5th file swap).

## Prototype (copy exactly)
```
doorUsual: sheet('Usual price at the door', fld('d-door','Price at the door (£)',S.doorPrice,'number',' min="0" step="0.5" inputmode="decimal"') + (S.oneOff?'':'<p class="hint">This is the price for every '+dayName()+'. Nights you changed keep their own price.</p>') + btn pri 'Done')
doorUsualDone: raw empty or unchanged -> close; else commit, toast savedAll()  // "Saved for every <day>" weekly / "Saved" one-off, with Undo
sellHere: commit(pay='here', 'Selling tickets here')
payDoor: nothing sold -> commit(pay='door', 'Dancers pay at the door')
link: sheet('Link to buy tickets', fld('f-link-in','Where dancers buy',S.link,'url',' placeholder="https://"') + '<p class="err" id="f-err" hidden>Add a link</p>' + btn pri 'Done')
linkDone: empty -> show f-err; else commit(link, pay='link', 'Dancers buy at your link')
howPay: sheet('How dancers pay', '<p class="txt">Or tap Sell tickets here to sell on Bachata Calendar again.</p>' + (pay==='door'?'':btn sec 'Pay at the door') + btn sec (link?'Change the link':'Add a link'))
oneOff row: factLn('goTickets','Tickets','Prices and how dancers pay')
```

## Checklist (each must be done and tested)
- [ ] SH-DOOR-01..04 sheet title, field label, weekly hint, Done + toast + Undo (tap 99, 100)
- [ ] SH-PAY-01 pay at the door, nothing sold: saves at once, toast "Dancers pay at the door" + Undo (tap 94)
- [ ] SH-PAY-04 link sheet incl. error "Add a link" and toast "Dancers buy at your link" + Undo (tap 97, 98)
- [ ] SH-PAY-05, SH-PAY-06 How dancers pay sheet text and buttons incl. hidden "Pay at the door" in door mode (tap 101)
- [ ] T-16 Sell tickets here in door/link mode: toast "Selling tickets here" + Undo (tap 93)
- [ ] T-17 / T-18 / T-19 lines already render; their inline links now open the sheets
- [ ] Tap 27 one-off row opens the Tickets screen
- [ ] Undo restores url / passes / paused types

## Tap table (put in `docs/briefs/editor-v2-slice12c-pay-sheets.handover.md`; columns button | destination | prototype/OLD; any OLD = not done)
| 27 | Tickets (Prices and how dancers pay) | one-off night card | Tickets screen |
| 93 | Sell tickets here | Tickets door/link mode | selling state + toast Undo |
| 94 | Pay at the door (inline), nothing sold | footer / How dancers pay sheet | toast + Undo |
| 97 | add a link / Change the link / Add a link | footer / How dancers pay sheet | Link sheet |
| 98 | Done | Link sheet | error when empty; else toast + Undo |
| 99 | Change the usual price | Tickets door mode | Door sheet |
| 100 | Done | Door sheet | toast + Undo |
| 101 | Change how dancers pay / Change | Tickets door or link mode | How dancers pay sheet |
Rows 88-90, 95, 96 (Stripe, Switch sheet) = 12d: list them as PENDING 12d.

## Handover
Write the handover file, commit, push to claude/editor-v2-s1c. No PR. List what you verified and did not (390x844 walk, pre-ship). Run `npx vitest run src/modules/organiser/p3/v2` and tsc on branch files.
