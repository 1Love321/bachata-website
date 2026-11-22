# Brief: Editor v2 slice 12d, real Stripe sign-up + "Switch how dancers pay?" sheet

Branch: claude/editor-v2-s1c
Size: 4 files, 260 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81 (embedded below).

Read first: `docs/briefs/editor-v2-slice12c-pay-sheets.handover.md` (on this branch). In 12c `payDoor` is a no-op when anything is sold and `link` is a no-op in `here` mode with sales; the `stripe` handler of `TicketsView` is unset. This slice builds both. Money slice: be exact; every state a test.

## Files (touch only these)
- src/modules/organiser/p3/v2/PaySheets.tsx: add `SwitchPaySheet` (SH-PAY-02/03).
- src/modules/organiser/p3/v2/TicketsV2.tsx: wire `on.stripe`, the Switch flow, the return-from-Stripe handling and `stripeWarn`.
- src/modules/organiser/p3/v2/copy.ts: new strings in the existing `ticketsPay` block only.
- src/modules/organiser/p3/v2/__tests__/stripeSwitch.test.tsx (new).
Do NOT edit payMode.ts, editorModel.ts, saveNow.ts, Facts.tsx, EveryBody.tsx, NightCard.tsx, shared/, p3/tickets/*. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Behaviour (decisions made; do not ask)
- **Stripe (taps 88, 89, 90, SH-STRIPE-01..03):** the prototype sheet "Stripe sign-up" is a STUB; build the REAL flow. Tapping the big "Sell tickets here" (no Stripe) calls `useStartOnboarding(organiserId, undefined, <return path>)` from `../tickets/data` (`go()` redirects to Stripe; same call `p3/tickets/TicketsEntry.tsx` line ~182 makes; copy its return path = `P3_TICKET_PATHS.eventTickets(series.id)` or the v2 tickets path `P3_PATHS.event(id) + '/tickets'` if the v2 route must be the return target: check what `stripeBack` does with `returnTo` and make Stripe return to THIS v2 Tickets screen). No sheet before the redirect. Show `busy` (button disabled) while redirecting; show `note` text (error) and the `switchedOff` case ("payments not switched on yet") as a short line under the button using the existing copy from `GetPaid.tsx` where it exists.
- **Return from Stripe:** the screen reads `?stripe=return` / `refresh` (see `returnKindOf` in p3/tickets/GetPaid.tsx; reuse it if exported, else copy its 3 lines). On `return`: refetch `useStripe`; once connected show toast "Stripe connected" (once, then clear the query param) and the selling state. On `return`/`refresh` but still not connected after the refetch: show the T-06 warning box (set `stripeWarn`) = SH-STRIPE-03. Do not poll in a loop: one refetch on arrival plus the existing `useConnectReturn` hook if it fits (read it; reuse as GetPaid does).
- **"Not now" (tap 90) does not exist for real Stripe**; the T-06 box is the SH-STRIPE-03 path only.
- **Switch sheet (taps 94 sold, 95, 96, SH-PAY-02/03):** when mode is `here` and tickets sold > 0: `payDoor` and `link` open sheet "Switch how dancers pay?" with text `<n> ticket(s) already sold here. Buyers keep their tickets. New buyers will not be able to buy here.` (`plural`: "1 ticket" / "3 tickets"), buttons "Switch" (primary) and "Keep selling" (secondary, closes, nothing saved). "Switch": for `door` target run the SAME pay-at-door action 12c built (clear link + pause on-sale types with `setTicketStatus(id,'paused')`), toast "Dancers pay at the door" + Undo (Undo re-opens the paused types and restores the link); for `link` target close this sheet and open the Link sheet (12c); saving the link pauses the on-sale types (as 12c does when nothing sold). Sold tickets are never touched: buyers keep them. Remove the 12c no-op guards.
- Total sold = sum of `sold` over the ticket rows; if the sales read has not loaded, treat as unknown and keep the 12c no-op (never switch blind).

## Prototype (copy exactly)
```
stripe: sheet('Stripe sign-up', 'In the real app this opens Stripe to make or link your account. ...' [STUB: build real Stripe instead])
stripeDo: commit(stripe=true, pay='here', toast 'Stripe connected')
stripeNo: warning box 'Dancers can’t buy yet. Connect Stripe or choose pay at the door.'
payDoor: if pay==='here' && stripe && sold -> switchSheet(next:'door') else commit(pay='door','Dancers pay at the door')
link: if pay==='here' && stripe && sold && !switched -> switchSheet(next:'link') else Link sheet
switchSheet: sheet('Switch how dancers pay?', plural(sold,'ticket')+' already sold here. Buyers keep their tickets. New buyers will not be able to buy here.' + btn pri 'Switch' + btn sec 'Keep selling')
switchDo: next==='door' -> commit(pay='door','Dancers pay at the door') else -> Link sheet
```

## Checklist (each done and tested)
- [ ] SH-STRIPE-01 big "Sell tickets here" starts real onboarding (mock `useStartOnboarding`; assert `go` called, button disabled while busy, error note shown)
- [ ] SH-STRIPE-02 return, connected: toast "Stripe connected", selling state, query param cleared
- [ ] SH-STRIPE-03 return, not connected: T-06 warning box
- [ ] SH-PAY-02 Switch sheet text with 1 and 3 tickets (plural), shown for door and link targets
- [ ] SH-PAY-03 "Switch" (door: types paused, toast + Undo) and "Keep selling" (closes, no write)
- [ ] 12c guards removed; unknown sales = still no-op

## Tap table (put in `docs/briefs/editor-v2-slice12d-stripe-switch.handover.md`; columns button | destination | prototype/OLD; any OLD = not done)
| 88 | Sell tickets here (big) | Tickets, no Stripe | real Stripe redirect |
| 89 | Connect Stripe / return | back from Stripe | toast "Stripe connected" + selling state |
| 90 | not connected on return | Tickets | T-06 warning |
| 94s | Pay at the door, sold>0 | footer / How sheet | Switch sheet (door) |
| 97s | add a link, sold>0 | footer | Switch sheet (link) |
| 95 | Switch | Switch sheet | door action + Undo / Link sheet |
| 96 | Keep selling | Switch sheet | closes |
Rows 91, 92 (ticket sheet) = slice 13: list as PENDING 13.

## Handover
Write the handover file, commit, push to claude/editor-v2-s1c. No PR. List verified/not verified (a real Stripe round trip cannot run here: say so). Run `npx vitest run src/modules/organiser/p3/v2` and tsc on branch files.
