# Brief: Editor v2 slice 12b, open the Tickets screen from the Price fact and the one-off Tickets row

Branch: claude/editor-v2-s1c
Size: 5 files, 150 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81 (embedded below).

Read `docs/briefs/editor-v2-slice12a-tickets-screen.handover.md` first. The Tickets screen exists at route `events/:seriesId/tickets` (P3Routes.tsx) but nothing links to it. Taps 22 and 27 are NOT wired because EveryBody/NightCard tests are router-less. Fix: an OPTIONAL prop, no router inside those components.

## Files (touch only these)
- src/modules/organiser/p3/v2/EditorV2.tsx: `useNavigate` here (the shell is routed); pass `onOpenTickets={() => navigate(`/app/events/${ws.series.id}/tickets`)}` (use the same path builder/prefix the other v2 navigations and the route use; verify the real URL prefix in P3Routes/EditorGate) to NightCard and EveryBody.
- src/modules/organiser/p3/v2/EveryBody.tsx: prop `onOpenTickets?: () => void`; weekly Price fact `onOpen={onOpenTickets}` (tap 22). One-off event (not weekly): the row "Tickets" with sub line "Prices and how dancers pay" (prototype `factLn('goTickets','Tickets','Prices and how dancers pay')`, line 308) opens the same screen (tap 27); build that row in the same place the one-off "Repeats" row is replaced, only if the one-off Every body is rendered by this file; otherwise list in the handover where it lives.
- src/modules/organiser/p3/v2/NightCard.tsx: prop `onOpenTickets?`; night Price fact `onOpen={onOpenTickets}` (tap 22 on a night card). Keep `changed`/`onBack` as is.
- src/modules/organiser/p3/v2/__tests__/every.test.tsx and nightCard.test.tsx (or one new `openTickets.test.tsx`): Price fact tap calls the prop; without the prop nothing happens and nothing throws; EditorV2 shell test navigates to the tickets path.
Do NOT edit TicketsV2.tsx, payMode.ts, Facts.tsx, editorModel.ts, saveNow.ts, shared/. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Prototype (copy exactly)
```
fact('goTickets','Price',esc(pl.m),esc(pl.s))          // weekly and night Price fact -> Tickets screen
S.oneOff ? factLn('goTickets','Tickets','Prices and how dancers pay') : factLn('repeat','Repeats', ...)   // one-off row
Tickets screen back link: '&larr; <event name>' -> goNight (first screen)
```

## Checklist
- [ ] Price fact "Change ›" on the weekly Every card opens the Tickets screen (tap 22)
- [ ] Price fact on a night card opens the Tickets screen (tap 22)
- [ ] one-off event: "Tickets / Prices and how dancers pay" row opens the Tickets screen (tap 27)
- [ ] back link on the Tickets screen returns to the editor first screen (tap 87), verified in a test with MemoryRouter
- [ ] E-09 is covered by these taps; the Price fact's label modes (door/link/tickets) stay as today: wiring them to payMode is a LATER slice, do not change `priceLabel` inputs

## Tap table (put in `docs/briefs/editor-v2-slice12b-open-tickets.handover.md`; columns button | destination | prototype/OLD; any OLD = not done)
| 22 | Price … Change › | Every card | Tickets screen |
| 22n | Price … Change › | Night card | Tickets screen |
| 27 | Tickets (Prices and how dancers pay) | one-off event | Tickets screen |
| 87 | ← <event name> | Tickets screen | first screen |

## Handover
Write the handover file, commit, push to claude/editor-v2-s1c. No PR. List what you verified and did not (390x844 walk, pre-ship). Run `npm run test:unit` on src/modules/organiser/p3/v2.
