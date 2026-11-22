# Brief: Editor v2 slice 16b, 390x844 tap-walk spec (replaces the pre-prototype spec)

Branch: claude/editor-v2-s1c
Size: 3 files, 380 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF in app code; zero migrations; no admin change. Tests only: no `.tsx` change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81. Tap table below is the spec.

Read first: `docs/briefs/editor-v2-slice15-one-off-page.handover.md` and `...slice16a-delete-old-ui.handover.md` (on this branch). Existing `tests/e2e/organiser-editor-v2.spec.ts` (216 lines) tests the OLD pre-prototype pages (save bar, `/all`, `/edit` night screen): rewrite it, keep its helpers' pattern (`OrganiserFake`, `PHONE`, `expectNoHorizontalScroll`, `open(page)`), keep the MANUAL registration in `scripts/check-e2e-spec-coverage.mjs` line ~100 (adjust only if you rename the file; do not).

## Files (touch only these)
- tests/e2e/organiser-editor-v2.spec.ts (rewrite)
- tests/e2e/helpers/organiserFake.ts (only if a state needs it: e.g. cancelled night, paused/ended series, one-off series, tickets and sales, early birds; add the minimum)
- docs/briefs/editor-v2-slice16b-e2e-walk.handover.md (new: the tap table with a `built` column filled from the run)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`.

## Behaviour of the spec (decisions made; do not ask)
1. Viewport 390x844. Mount the editor at `/account/p3/events/<id>` (the route that mounts `EditorV2` with the flag on, see P3Routes). Run locally as the MANUAL line says (flag env at dev-server start).
2. TABLE-DRIVEN: one array of rows `{ n, label, setup, act, expect }` covering EVERY non-STUB, non-PROTO-ONLY row of the tap table below (rows 1..110 less rows 107-112 STUBs/PROTO-ONLY; STUB rows 1, 107-110 only assert that the tap does not navigate to an OLD page). Each row taps the real button by its visible label and asserts the destination named in the table (sheet title, toast text, screen). Group rows by state (weekly Live, weekly cancelled night, Paused, Ended, one-off, tickets none/door/here/link/no-Stripe) so setups are shared.
3. Row 113 (scrim tap, Escape closes a sheet with nothing saved): one test.
4. OLD-URL guard: a `page.on('framenavigated')` + `page.url()` check after every tap fails the test if the URL path ends in `/details`, `/dates`, `/all`, `/organisers` or `/edit` (the OLD pages), except the entry route itself if it is `/edit` (then compare to the entry).
5. Checklist X-01..X-13 (X-14 past night: prototype does not define it, SKIP, write "X-14 not defined by prototype" in the handover): one assertion per state: weekly plain; changed-venue night (tags "Only this night", "Back to usual", chip "Changed"); cancelled night (red pill, "Bring this night back", reason line); Paused (pill "Paused", rows "Make it live again" and "End the event"); Ended (pill "Ended", rows "Make it live again", "Pause the event"); weekly with end date (Repeats "Every <day>, ends after <date>"); cancelled event (cancel-event sheet); one-off (no tabs); Price in five modes (door, tickets here, link, no Stripe, no tickets) on fact + Price sheet + Tickets screen; empty lists ("None yet", "No note", "Nothing here yet."); disabled "Remove this one" and "Remove this ticket" (sold); error lines SH-NAME-04, SH-WHEN-03, SH-SES-05, SH-PAY-04, SH-TICKET-08; guest list on/off, Raffle pill on/off, refund row present/absent (refund + note are LEFT OUT by owner: assert they are ABSENT).
6. Screenshots of each state to `docs/design/editor-v2/shots/` (as the old spec does). `expectNoHorizontalScroll` after every state.
7. A row the built UI cannot satisfy: do NOT weaken the assertion. Mark it `test.fixme` with the row number and write it in the handover under "BUILT UI DIFFERS" (this is the signal the owner needs). Known UNBUILT from earlier handovers (do not fix, list): move_date Undo (row 36), weekly per-night sold count, per-night ticket price write.

## Checklist
- [ ] every non-stub row of the tap table has a test or a `fixme` with reason
- [ ] OLD-URL guard on every tap
- [ ] X-01..X-13 each one assertion; X-14 skipped with note
- [ ] spec runs green locally at 390x844 (paste the pass/fail/fixme counts in the handover); `npx tsc -p tsconfig.app.json --noEmit` 0 new errors; `npm run check:mojibake` clean; `npm run check:e2e-spec` (or the coverage script) still passes
- [ ] handover = tap table, 5th column `built: prototype / OLD / FIXME`; any OLD or FIXME listed up top

## Handover
`docs/briefs/editor-v2-slice16b-e2e-walk.handover.md`, commit, push to claude/editor-v2-s1c. No PR. Verified / Not verified.

## TAP TABLE (the spec)
# Event editor v2: tap table (from the frozen prototype)

Source: `event-editor-c2-prototype.FROZEN-2026-11-21.html`. One row per `data-act` (all 80, as counted by `smoke.mjs`), plus one row per argument that changes the destination.
Marks: **STUB** = the prototype shows a sample; the build goes to the real thing named in the row. **PROTO-ONLY** = prototype control, not built.
Toast rule "tab toast": on a one-off "Saved"; on the tab "Every <day>" "Saved for every <day>"; on "This night" "Saved for <date> only". All tab toasts carry Undo.
For the walk before a preview, add a fifth column `built: prototype / OLD`. One OLD row = not done.

| # | button (label as shown) | where it sits | act (arg) | destination in the prototype |
|---|---|---|---|---|
| 1 | ← Events | first screen, top | `stub` | **STUB** toast "The events list is the same as today." Build: go to the events list of today |
| 2 | This night | first screen, tabs | `vtab` (night) | Same screen, tab "This night": chips, title = long date, scope "Changes here are for this night only." |
| 3 | Every <day> | first screen, tabs | `vtab` (every) | Same screen, tab "Every <day>": title "Every <day>", About rows, Pause/End/Cancel card |
| 4 | date chip "Thu 26 Nov" | first screen, chips row | `pick` (date) | Same screen redrawn for that night |
| 5 | More dates | first screen, last chip | `moreDates` | Sheet "All dates" |
| 6 | date row "<date>" | sheet All dates | `pickClose` (date) | Sheet closes; first screen, tab "This night", that night |
| 7 | Flyer and photos | on the flyer | `flyer` | Sheet "Flyer and photos" (night gallery on This night; event gallery on Every and one-off) |
| 8 | When … Change › | This night card | `when` | Sheet "When" |
| 9 | Where … Change › | This night card and Every card | `where` | Sheet "Where" |
| 10 | Price … Change › | This night card | `price` | Sheet "Price" (state by pay mode: door, tickets, link, no Stripe, no tickets) |
| 11 | Description … Change › | This night card | `about` | Sheet "Description" (label "What dancers read on <date> only") |
| 12 | Back to usual | under a changed fact | `usual` (sessions / venue / doorPrice or prices / about / flyer) | Stays on the screen; the change is gone; toast "Back to usual for <date>" + Undo |
| 13 | Cancel this night | under the facts | `cancelNight` | Sheet "Cancel <date>?" |
| 14 | Bring this night back | under the facts, cancelled night | `bringBack` | Sheet "Bring <date> back?" |
| 15 | 23 going (avatars) | going card | `stub` | **STUB** toast "Who is going opens the same list as today." Build: today's going list |
| 16 | 1 refund asked / Tap to answer | group This night | `refund` | Sheet "Priya Shah asks for a refund" (**STUB** sample person) |
| 17 | Guest list [On · 3 names / Off] | group This night | `guest` | Sheet "Guest list" (**STUB** sample names and link) |
| 18 | Raffle [On · 12 entries / Off] | group This night | `stub` | **STUB** toast "The raffle opens the same screen as today. Its on and off switch is inside." Build: today's raffle screen |
| 19 | Note for this night | group This night | `note` | Sheet "Note for this night" |
| 20 | Share this night | bottom of This night | `share` | Sheet "Share this night" (**STUB** sample link) |
| 21 | When … Change › | Every card | `programme` | Sheet "Classes and party" |
| 22 | Price … Change › | Every card | `goTickets` | Tickets screen |
| 23 | Name | About this event / The event | `name` | Sheet "Name" |
| 24 | Description | About this event (weekly) | `about` | Sheet "Description" (label "What dancers read") |
| 25 | Music | About this event / The event | `music` | Sheet "Music" |
| 26 | Repeats | About this event (weekly) | `repeat` | Sheet "Repeats" |
| 27 | Tickets (Prices and how dancers pay) | The event (one-off) | `goTickets` | Tickets screen |
| 28 | Organisers | About this event / The event | `organisers` | Sheet "Organisers" |
| 29 | Make it live again | Pause, end or cancel card | `liveAgain` | No sheet; pill "Live"; toast "Event is live again" + Undo |
| 30 | Pause the event | Pause, end or cancel card | `pauseAsk` (Paused) | Sheet "Pause the event?" |
| 31 | End the event | Pause, end or cancel card | `pauseAsk` (Ended) | Sheet "End the event?" |
| 32 | Cancel the event | Pause, end or cancel card (weekly) | `cancelEvent` | Sheet "Cancel the event" |
| 33 | × (Close) | every sheet head | `close` | Sheet closes; nothing saved |
| 34 | Undo | toast | `undo` | Last change put back; toast "Undone" |
| 35 | Done | sheet Name | `nameDone` | Empty: error "Add a name". Else sheet closes; toast "Saved for every <day>" / "Saved" + Undo |
| 36 | Save the date | sheet When (one-off) | `dateDone` | Empty: error "Add a date." Else toast "Saved" + Undo |
| 37 | session row (title, "Class · Carlos & Ana · Beginner", times) | sheet When | `session` (night, id) | Session sheet for this night, title = session title |
| 38 | Add a class or party | sheet When | `session` (night, new) | Session sheet "Add a class or party", this night |
| 39 | Back to the usual times | sheet When | `usual` (sessions) | Sheet closes; toast "Back to usual for <date>" + Undo |
| 40 | session row | sheet Classes and party | `session` (usual, id) | Session sheet for the usual night |
| 41 | Add a class or party | sheet Classes and party | `session` (usual, new) | Session sheet "Add a class or party", usual night |
| 42 | Done | sheet Classes and party | `close` | Sheet closes; no toast |
| 43 | Class / Masterclass / Party / Performance | session sheet, Type | `sType` (type) | Same sheet redrawn: Level shows for Class and Masterclass; people group Teachers / DJs / Dancers; warning "<names> will be removed." when people no longer fit |
| 44 | Beginner / Improver / Intermediate / Advanced | session sheet, Level | `sLevel` (level) | Same sheet; chip picked |
| 45 | Add | session sheet people group; sheet Organisers | `pAdd` (teachers / djs / dancers / organisers) | Same sheet; the picked person is added as a chip |
| 46 | × (Remove <name>) | person chip | `pDel` (group, index) | Same sheet; chip gone |
| 47 | Done | session sheet | `sDone` | Ends before start: error "Ends before it starts. Check the end time." Else sheet closes; tab toast + Undo |
| 48 | Remove this one | session sheet | `sRemove` | Sheet closes; tab toast + Undo. Disabled when it is the only one |
| 49 | (typing in "Search for a venue") | sheet Where | `whereSearch` (input, not a tap) | **STUB** sample list filtered. Build: real venue list filtered as you type |
| 50 | venue row (name, area, "Now") | sheet Where | `wherePick` (index) | Sheet closes; venue saved; tab toast + Undo |
| 51 | Use “<typed>” / A venue we do not know yet | sheet Where | `wherePick` (typed) | Sheet closes; venue = typed name, area "New venue"; tab toast + Undo |
| 52 | Back to the usual venue | sheet Where | `usual` (venue) | Sheet closes; toast "Back to usual for <date>" + Undo |
| 53 | Done | sheet Price | `priceDone` | Sheet closes; toast "Saved for <date> only" (one-off "Saved") + Undo |
| 54 | Back to the usual price / prices | sheet Price | `usual` (doorPrice / prices) | Sheet closes; toast "Back to usual for <date>" + Undo |
| 55 | Change the usual price / All ticket settings / Change tickets for every <day> / Set up tickets / Add your first ticket | sheet Price | `goTickets` | Sheet closes; tab becomes "Every <day>"; Tickets screen |
| 56 | Change the link | sheet Price (one-off, link mode) | `link` | Sheet "Link to buy tickets" |
| 57 | Choose a new flyer | sheet Flyer and photos | `flyerSet` (New flyer) | **STUB** sample flyer set. Build: phone photo picker, then saved; tab toast + Undo |
| 58 | Remove the flyer | sheet Flyer and photos | `flyerSet` (empty) | Flyer gone; tab toast + Undo |
| 59 | Back to the usual flyer | sheet Flyer and photos | `usual` (flyer) | Toast "Back to usual for <date>" + Undo |
| 60 | photo or video tile | sheet Flyer and photos | `galOpen` (index) | Sheet "Photo" or "Video" |
| 61 | Add photos | sheet Flyer and photos | `galAdd` | **STUB** sample photo added. Build: phone photo picker; sheet stays open |
| 62 | Add a video | sheet Flyer and photos | `vidAdd` | Video tile added from the link field; sheet stays open |
| 63 | Remove this photo / Remove this video | sheet Photo / Video | `galDel` | Sheet "Flyer and photos" open again; toast "Photo removed" / "Video removed" + Undo |
| 64 | Back to flyer and photos | sheet Photo / Video | `galBack` | Sheet "Flyer and photos" |
| 65 | Done | sheet Note for this night | `noteDone` | Sheet closes; toast "Saved for <date>" + Undo |
| 66 | Copy link | sheet Share this night; sheet Guest list | `copy` | Toast "Link copied"; if blocked "Select the link to copy it" |
| 67 | Guest list (switch) | sheet Guest list | `guestTog` | Same sheet redrawn on or off; toast "Guest list on" / "Guest list off" |
| 68 | Refund £10 | sheet refund | `refundDo` (yes) | Sheet closes; row gone; toast "Refunded £10 to Priya Shah" (no Undo) |
| 69 | Say no | sheet refund | `refundDo` (no) | Sheet closes; row gone; toast "Refund declined" (no Undo) |
| 70 | Cancel this night | sheet Cancel <date>? | `cancelNightDo` | Sheet closes; night cancelled with the picked reason; toast "Cancelled <date>" (no Undo) |
| 71 | Keep this night | sheet Cancel <date>? | `close` | Sheet closes |
| 72 | Bring it back | sheet Bring <date> back? | `bringBackDo` | Sheet closes; night live again; toast "Brought back <date>" (no Undo) |
| 73 | Keep it cancelled | sheet Bring <date> back? | `close` | Sheet closes |
| 74 | Done | sheet Description | `aboutDone` | Sheet closes; tab toast + Undo |
| 75 | Back to the usual description | sheet Description | `usual` (about) | Sheet closes; toast "Back to usual for <date>" + Undo |
| 76 | Bachata / Sensual / Dominican / Salsa / Kizomba | sheet Music | `musicTog` (style) | Same sheet; chip on or off |
| 77 | Done | sheet Music | `musicDone` | Sheet closes; toast "Saved for every <day>" / "Saved" + Undo |
| 78 | Done | sheet Repeats | `repeatDone` | No night loses a change: sheet closes, toast "Saved" + Undo. Else sheet "Change repeat?" |
| 79 | Change repeat | sheet Change repeat? | `repeatDo` | Sheet closes; listed changes removed; toast "Saved" |
| 80 | Keep the current repeat | sheet Change repeat? | `close` | Sheet closes |
| 81 | Done | sheet Organisers | `orgDone` | Sheet closes; toast "Saved for every <day>" / "Saved" + Undo |
| 82 | Pause the event | sheet Pause the event? | `statusDo` (Paused) | Sheet closes; pill "Paused"; toast "Event paused" + Undo |
| 83 | End the event | sheet End the event? | `statusDo` (Ended) | Later date given: stays Live, toast "Ends after <date>". No date: pill "Ended", toast "Event ended". Both + Undo |
| 84 | Keep it live / paused / ended / cancelled | status sheets | `close` | Sheet closes |
| 85 | Cancel the event | sheet Cancel the event | `cancelEventDo` | Sheet closes; pill "Cancelled"; toast "Event cancelled" (no Undo) |
| 86 | Keep the event | sheet Cancel the event | `close` | Sheet closes |
| 87 | ← <event name> | Tickets screen, top | `goNight` | First screen |
| 88 | Sell tickets here (big) | Tickets screen, no Stripe | `stripe` | **STUB** sheet "Stripe sign-up": "In the real app this opens Stripe to make or link your account. In this prototype, tap Connect." Build: real Stripe sign-up |
| 89 | Connect Stripe | sheet Stripe sign-up | `stripeDo` | **STUB** sheet closes; toast "Stripe connected"; Tickets screen in selling state. Build: return from Stripe |
| 90 | Not now | sheet Stripe sign-up | `stripeNo` | **STUB** sheet closes; warning "Dancers can’t buy yet. Connect Stripe or choose pay at the door." |
| 91 | ticket card | Tickets screen | `ticket` (id) | Sheet "Ticket" |
| 92 | Add a ticket | Tickets screen | `ticket` (new) | Sheet "Add a ticket" |
| 93 | Sell tickets here | Tickets screen, door or link mode | `sellHere` | Same screen in selling state; toast "Selling tickets here" + Undo |
| 94 | Pay at the door (inline) | Tickets screen footer; sheet How dancers pay | `payDoor` | Nothing sold: toast "Dancers pay at the door" + Undo. Sold: sheet "Switch how dancers pay?" |
| 95 | Switch | sheet Switch how dancers pay? | `switchDo` | Sheet closes; pay at the door; toast "Dancers pay at the door" |
| 96 | Keep selling | sheet Switch how dancers pay? | `close` | Sheet closes |
| 97 | add a link (inline) / Change the link / Add a link | Tickets screen footer; sheet How dancers pay | `link` | Sheet "Link to buy tickets" |
| 98 | Done | sheet Link to buy tickets | `linkDone` | Empty: error "Add a link". Else sheet closes; toast "Dancers buy at your link" + Undo |
| 99 | Change the usual price (inline) | Tickets screen, door mode | `doorUsual` | Sheet "Usual price at the door" |
| 100 | Done | sheet Usual price at the door | `doorUsualDone` | Sheet closes; toast "Saved for every <day>" / "Saved" + Undo |
| 101 | Change how dancers pay / Change (inline) | Tickets screen, door or link mode | `howPay` | Sheet "How dancers pay" |
| 102 | Free ticket (switch) | ticket sheet | `tFree` | Same sheet; price and early bird fields hidden or shown |
| 103 | Add an early bird price | ticket sheet | `tStageAdd` | Same sheet; one more row "Price (£)" + "Until" |
| 104 | × (Remove this early bird price) | ticket sheet, early bird row | `tStageDel` (index) | Same sheet; row gone |
| 105 | Done | ticket sheet | `tDone` | "How many" below sold: error line. Else sheet closes; toast "Saved" + Undo |
| 106 | Remove this ticket | ticket sheet | `tRemove` | Sheet closes; toast "Ticket removed" + Undo. Disabled when sold |
| 107 | Home | bottom bar | `tab` (Home) | **STUB** toast "Home is the same as today." Build: today's bar, not rebuilt |
| 108 | Events | bottom bar | `tab` (Events) | **STUB** toast "The events list is the same as today." |
| 109 | Tickets | bottom bar | `tab` (Tickets) | **STUB** toast "Tickets is the same as today." |
| 110 | Team | bottom bar | `tab` (Team) | **STUB** toast "Team is the same as today." |
| 111 | Show a one-off event / Show the weekly event | under the phone | `oneOff` | **PROTO-ONLY** toast "Showing a one-off event" / "Showing the weekly event". Not built |
| 112 | Stripe connected on/off | under the phone | `stripeToggle` | **PROTO-ONLY**. Not built |
| 113 | (scrim tap, Escape key) | behind any sheet | no act | Sheet closes; nothing saved |

## The 80 acts and their rows

about 11, 24 · aboutDone 74 · bringBack 14 · bringBackDo 72 · cancelEvent 32 · cancelEventDo 85 · cancelNight 13 · cancelNightDo 70 · close 33, 42, 71, 73, 80, 84, 86, 96 · copy 66 · dateDone 36 · doorUsual 99 · doorUsualDone 100 · flyer 7 · flyerSet 57, 58 · galAdd 61 · galBack 64 · galDel 63 · galOpen 60 · goNight 87 · goTickets 22, 27, 55 · guest 17 · guestTog 67 · howPay 101 · link 56, 97 · linkDone 98 · liveAgain 29 · moreDates 5 · music 25 · musicDone 77 · musicTog 76 · name 23 · nameDone 35 · note 19 · noteDone 65 · oneOff 111 · orgDone 81 · organisers 28 · pAdd 45 · pDel 46 · pauseAsk 30, 31 · payDoor 94 · pick 4 · pickClose 6 · price 10 · priceDone 53 · programme 21 · refund 16 · refundDo 68, 69 · repeat 26 · repeatDo 79 · repeatDone 78 · sDone 47 · sLevel 44 · sRemove 48 · sType 43 · sellHere 93 · session 37, 38, 40, 41 · share 20 · statusDo 82, 83 · stripe 88 · stripeDo 89 · stripeNo 90 · stripeToggle 112 · stub 1, 15, 18 · switchDo 95 · tDone 105 · tFree 102 · tRemove 106 · tStageAdd 103 · tStageDel 104 · tab 107 to 110 · ticket 91, 92 · undo 34 · usual 12, 39, 52, 54, 59, 75 · vidAdd 62 · vtab 2, 3 · when 8 · where 9 · wherePick 50, 51

Handler with no tap: `whereSearch` (row 49, fired by typing).
