# Brief: Editor v2 slice 15, one-off page + sold counts (taps 27, 36)

Branch: claude/editor-v2-s1c
Size: 5 files, 380 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81. The prototype code below is the whole spec: every row of the checklist is built.

Read first: `docs/briefs/editor-v2-slice14-night-price.handover.md`, `docs/briefs/editor-v2-slice13-ticket-sheet.handover.md`. Then AUDIT the current one-off page (`series.format === 'one_off'`) in `p3/v2/EditorV2.tsx`, `NightHub.tsx` (already has `oneOff`, `ticketsSold`, `moneyPence` props), `NightCard.tsx`, `EndCard.tsx`, `TicketsV2.tsx` against O-01..O-13 below. Build only what is MISSING or differs; list in the handover each row as BUILT-EARLIER / BUILT-NOW.

## Files (touch only these)
- src/modules/organiser/p3/v2/OneOffBody.tsx (new): the "The event" group for a one-off: one-line rows Name, Music, Tickets ("Prices and how dancers pay", opens the Tickets screen = tap 27), Organisers (reuse the existing Name/Music/Org sheets), then EndCard. No Description row, no Repeats row.
- src/modules/organiser/p3/v2/EditorV2.tsx: one-off: no tabs, no chips, no scope line; title = long date; pass real `ticketsSold`/`moneyPence` to NightHub and `soldCount` (tickets sold on THIS night) to NightCard/cancel sheets; mount OneOffBody under NightHub for one-off only.
- src/modules/organiser/p3/v2/EndCard.tsx: one-off: no "Cancel the event" row; End row second line "Dancers see it as ended.".
- src/modules/organiser/p3/v2/copy.ts: new strings in a new `oneOff` block only.
- src/modules/organiser/p3/v2/__tests__/oneOff.test.tsx (new).
Sold counts: reuse the sales read already used by TicketsV2 (`useSummary` / types data, `ticketsSold`); for weekly nights the count is tickets whose `occurrenceId` equals that night plus series-wide types sold on that night if the read gives it per night; if the read has no per-night figure for series-wide types, use the per-type total and say so under `UNBUILT` in the handover (do not invent an RPC). Do NOT edit payMode.ts, editorModel.ts, saveNow.ts, Facts.tsx, shared/, p3/tickets/*. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Behaviour (decided; do not ask)
- Saves on a one-off show toast "Saved" (never "for every <day>" or "for <date> only"). The When sheet for a one-off: Date field, error "Add a date." when empty, button "Save the date" (tap 36), toast "Saved" + Undo (verify slice 7b built it; fix only if it differs).
- Cancel this night on a one-off uses `series.cancel_one_off` (already wired); the sold warning in the cancel sheet now shows the real `soldCount`.
- Refund row stays out (owner item 5); Note row only if it already exists.

## Checklist (build every row)
- [ ] O-01 No tabs and no date chips; one page
- [ ] O-02 Title is the long date; no scope line about "this night only"
- [ ] O-03 Label: "What dancers see. Tap to change."
- [ ] O-04 Same card: flyer block, pill "Flyer and photos", name with status pill, facts When, Where, Price, Description
- [ ] O-05 No "Only this night" tags and no "Back to usual" buttons
- [ ] O-06 "Cancel this night" button and its line as N-27 to N-29
- [ ] O-07 Going card and the two tiles as N-31, N-32 (tiles show the real sold count and money)
- [ ] O-08 Group heading "On the night" with rows Guest list, Raffle, Note for this night (refund row only when a request exists: none today)
- [ ] O-09 Group heading "The event" with one-line buttons Name, Music, Tickets, Organisers (no Description row here, no Repeats row)
- [ ] O-10 One-line button "Tickets" with value "Prices and how dancers pay"; opens the Tickets screen
- [ ] O-11 End card rows: Pause the event, End the event (second line "Dancers see it as ended."), Make it live again; no "Cancel the event" row
- [ ] O-12 "Share this night" button and its line at the bottom
- [ ] O-13 Saves on a one-off show toast "Saved"

Tap rows: 27 Tickets (Prices and how dancers pay) -> Tickets screen; 36 Save the date -> empty: error "Add a date."; else toast "Saved" + Undo.

## Prototype code (frozen)
```js
function aboutRows(){
  return '<div class="fb-list">'+factLn('name','Name',esc(S.name))+(S.oneOff?'':factLn('about','Description',aboutShort(S.about)))
    +factLn('music','Music',S.music.length?esc(S.music.join(', ')):'None yet')
    +(S.oneOff?factLn('goTickets','Tickets','Prices and how dancers pay'):factLn('repeat','Repeats','Every '+dayName()+(S.endDate?', ends after '+longDate(S.endDate):', no end date')))
    +factLn('organisers','Organisers',S.organisers.length?esc(S.organisers.join(', ')):'None yet')+'</div>';
}
function endCard(){
  var st=S.status,endSub=S.oneOff?'Dancers see it as ended.':'...';
  return '<div class="card">'+(st!=='Live'?row('liveAgain','Make it live again',st+' now. Dancers see it as '+st.toLowerCase()+'.'):'')
    +(st==='Live'||st==='Ended'?row('pauseAsk','Pause the event','Dancers see it as paused. You can start it again.','','','Paused'):'')
    +(st==='Live'||st==='Paused'?row('pauseAsk','End the event',endSub,'','','Ended'):'')
    +(S.oneOff?'':/* Cancel the event row, weekly only */'')+'</div>';
}
// bodyNight, one-off parts:
// label: S.oneOff?'What dancers see. Tap to change.':'What dancers see on this night. Tap to change.'
// group:  '<div class="lab hd">'+(S.oneOff?'On the night':'This night')+'</div>' with refund(optional) / Guest list / Raffle / Note for this night
// if(S.oneOff) h+='<div class="grp"><div class="lab hd">The event</div><div class="card">'+aboutRows()+'</div></div>'+endCard();
// then Share this night button + "Send dancers the link to this night."
// goTickets:function(){closeSheet();if(!S.oneOff)S.tab='every';go('tickets')}
// savedAll: S.oneOff?'Saved':'Saved for every '+dayName()
```

## Done
`npx vitest run src/modules/organiser/p3/v2` green, tsc 0 errors in touched files, `npm run check:mojibake` clean. Handover `docs/briefs/editor-v2-slice15-one-off-page.handover.md`: O-01..O-13 each BUILT-EARLIER/BUILT-NOW, tap table (button | destination | prototype/OLD) for 27, 36, and an `UNBUILT` section (empty if none). No PR (stacked on claude/editor-v2-s1c).
