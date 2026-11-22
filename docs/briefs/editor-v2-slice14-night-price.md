# Brief: Editor v2 slice 14, price sheet for this night (taps 10, 53, 54, 55, 56)

Branch: claude/editor-v2-s1c
Size: 5 files, 380 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81. The prototype code below is the whole spec: every row of the checklist is built.

Read first: `docs/briefs/editor-v2-slice13-ticket-sheet.handover.md` and `docs/briefs/editor-v2-slice12e-money-fixes.handover.md` (on this branch). Read `p3/v2/NightCard.tsx` (FACT_KEYS price = `passes`,`ticket_url`; `backToUsualStep`; `saveVenue`/`saveAbout` show the save + Undo pattern through `onSave`), `p3/v2/Facts.tsx` (`priceLabel`), `p3/v2/payMode.ts`, `p3/v2/TicketsV2.tsx` (how the tab and screen are switched).

## Files (touch only these)
- src/modules/organiser/p3/v2/PriceSheet.tsx (new): sheet SH-PRICE, all five states.
- src/modules/organiser/p3/v2/NightCard.tsx: the Price fact's "Change" (tap 10) opens PriceSheet for this night (today it calls `onOpenTickets`); pass pay mode, link, stripe state, ticket list the card already has or can read. The Back-to-usual chip on the fact keeps working.
- src/modules/organiser/p3/v2/EditorV2.tsx: only the wiring for `goTickets` (close sheet, tab becomes "Every <day>" for weekly, open the Tickets screen) and `link` (open the existing link sheet from slice 12c).
- src/modules/organiser/p3/v2/copy.ts: new strings in a new `priceSheet` block only.
- src/modules/organiser/p3/v2/__tests__/priceSheet.test.tsx (new).
Do NOT edit payMode.ts, editorModel.ts, saveNow.ts, Facts.tsx, shared/, p3/tickets/*. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only (`&rsquo;` in "can&rsquo;t").

## Behaviour (decided; do not ask)
- Door mode: the night price is a one-night override of the price list, key `passes` (`occurrence.set_override passes`, first entry = door price, same list `doorPriceOf` reads). Save through `onSave` like `saveVenue`, with the old override as the Undo value. Toast "Saved for <date> only" (one-off: "Saved"), with Undo. Empty field or unchanged value: just close.
- Back to the usual price: use `backToUsualStep(date, 'price', ...)`; toast "Back to usual for <date>" with Undo.
- Tickets-here mode (one field per paid ticket): search first for an existing per-night ticket price write (`ticket_type`, `occurrence`, `prices`, `night_price` in src/modules/organiser and src/lib). If one exists, wire it. If NONE exists, build every other state, render this state exactly as the prototype but make Done close without saving, and write in the handover under a heading `UNBUILT` exactly which write is missing. Do not invent an RPC and do not hide it.
- Link, no-Stripe, no-tickets states have no save: only the buttons/links in the checklist.
- Weekly shows the bold long date plus "This changes the price for this night only. Other nights keep the usual price." Money slice: every state a test (five modes, Done, Back to usual, unchanged close, failure keeps sheet open).

## Checklist (build every row)
- [ ] SH-PRICE-01 Title "Price"
- [ ] SH-PRICE-02 Weekly: the long date in bold, then "This changes the price for this night only. Other nights keep the usual price."
- [ ] SH-PRICE-03 Pay at the door: field "Price at the door (£)", button "Done"; toast "Saved for <date> only" with Undo
- [ ] SH-PRICE-04 Pay at the door: button "Back to the usual price" when changed
- [ ] SH-PRICE-05 Link at the bottom, weekly: "Change the usual price" (goes to the Tickets screen and the tab "Every <day>")
- [ ] SH-PRICE-06 Link at the bottom, one-off: "All ticket settings"
- [ ] SH-PRICE-07 Link mode: text "Dancers buy at <link>." and link "Change tickets for every <day>" (one-off: button "Change the link" plus link "All ticket settings")
- [ ] SH-PRICE-08 Stripe not connected: text "Dancers can&rsquo;t buy yet." and button "Set up tickets"
- [ ] SH-PRICE-09 No tickets yet: text "No tickets yet." and button "Add your first ticket"
- [ ] SH-PRICE-10 Tickets here: one field per paid ticket, labelled "<ticket name> (£)"
- [ ] SH-PRICE-11 Tickets here: a free ticket shows as the line "<ticket name>: Free" (no field)
- [ ] SH-PRICE-12 Tickets here: button "Done" and button "Back to the usual prices" when changed

Tap rows: 10 Price Change -> Sheet "Price" (state by pay mode); 53 Done -> closes, toast "Saved for <date> only" (one-off "Saved") + Undo; 54 Back to the usual price(s) -> closes, toast "Back to usual for <date>" + Undo; 55 Change the usual price / All ticket settings / Change tickets for every <day> / Set up tickets / Add your first ticket -> sheet closes, tab becomes "Every <day>", Tickets screen; 56 Change the link (one-off, link mode) -> Sheet "Link to buy tickets".

## Prototype code (frozen)
```js
usual:function(k){var d=S.cur;commit(function(){clearNight(k)},'Back to usual for '+longDate(d))},
function backToUsual(key,label){return has(key)?B('btn sec','usual','Back to the usual '+label,key):''}
price:function(){
  var h='',only=S.oneOff?'':'<p class="txt"><b>'+longDate(S.cur)+'</b></p><p class="txt">This changes the price for this night only. Other nights keep the usual price.</p>',usualLnk=B('lnk','goTickets',S.oneOff?'All ticket settings':'Change the usual price');
  if(S.pay==='door'){h=only+fld('p-door','Price at the door (£)',eff('doorPrice'),'number',' min="0" step="0.5" inputmode="decimal"')+B('btn pri','priceDone','Done')+backToUsual('doorPrice','price')+usualLnk}
  else if(S.pay==='link'){h='<p class="txt">Dancers buy at '+esc(S.link||'your link')+'.</p>'+(S.oneOff?B('btn sec','link','Change the link')+B('lnk','goTickets','All ticket settings'):B('lnk','goTickets','Change tickets for every '+dayName()))}
  else if(!S.stripe){h='<p class="txt">Dancers can’t buy yet.</p>'+B('btn pri','goTickets','Set up tickets')}
  else if(!S.tickets.length){h='<p class="txt">No tickets yet.</p>'+B('btn pri','goTickets','Add your first ticket')}
  else{
    h=only+S.tickets.map(function(t){return t.free?'<p class="txt"><b>'+esc(t.name)+'</b>: Free</p>':fld('p-'+t.id,esc(t.name)+' (£)',nightPrice(t),'number',' min="0" step="0.5" inputmode="decimal"')}).join('')
      +B('btn pri','priceDone','Done')+backToUsual('prices','prices')+usualLnk;
  }
  sheet('Price',h);
},
priceDone:function(){
  if(S.pay==='door'){
    var nv=num(v('p-door')),old=eff('doorPrice');if(v('p-door')===''||nv===old){closeSheet();return}
    ask(gbp(nv)+' at the door, not '+gbp(old),function(){setNight('doorPrice',nv)},function(){S.doorPrice=nv;clearNight('doorPrice')});return;
  }
  var ch={};S.tickets.forEach(function(t){if(t.free)return;var raw=v('p-'+t.id),x=num(raw);if(raw!==''&&x!==nightPrice(t))ch[t.id]=x});
  if(!Object.keys(ch).length){closeSheet();return}
  /* setNight('prices', {ticketId: price, ...}) ; Undo clears 'prices' */
},
```
(`ask(...)` in the prototype is its confirm-and-toast helper: the real build saves at once and shows the toast with Undo, as `saveVenue` does.)

## Done
`npx vitest run src/modules/organiser/p3/v2` green, tsc 0 errors in touched files, `npm run check:mojibake` clean. Fill a tap table (button | destination | prototype/OLD) for 10, 53-56 in `docs/briefs/editor-v2-slice14-night-price.handover.md`, plus an `UNBUILT` section (empty if none). No PR (stacked on claude/editor-v2-s1c).
