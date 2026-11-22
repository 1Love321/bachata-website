# Brief: Editor v2 slice 13, ticket sheet with early birds (taps 91, 92, 102-106)

Branch: claude/editor-v2-s1c
Size: 5 files, 400 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81. The prototype code below is the whole spec: every row of the checklist is built.

Read first: `docs/briefs/editor-v2-slice12e-money-fixes.handover.md` and `docs/briefs/editor-v2-slice12d-stripe-switch.handover.md` (on this branch). Reuse the existing ticket-type WRITE paths of the old editor (`p3/tickets/TypeSheet.tsx`, `PriceSteps.tsx`, `data.ts`: find the create/update/delete type RPC calls and the price-steps (early bird) wire shape and call them the same way; do not invent RPCs). Money slice: every state a test.

## Files (touch only these)
- src/modules/organiser/p3/v2/TicketSheetV2.tsx (new): sheet SH-TICKET.
- src/modules/organiser/p3/v2/EarlyBirds.tsx (new): early bird rows + warning.
- src/modules/organiser/p3/v2/TicketsV2.tsx: wire taps 91 (ticket card opens sheet with id) and 92 (Add a ticket opens new); fix: the Connect-Stripe screen must NOT show while `stripe.state` is still null (loading) (gate on `stripe.state !== null`).
- src/modules/organiser/p3/v2/copy.ts: new strings in a new `ticketSheet` block only.
- src/modules/organiser/p3/v2/__tests__/ticketSheet.test.tsx (new).
Do NOT edit payMode.ts, editorModel.ts, saveNow.ts, Facts.tsx, shared/, p3/tickets/*. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Behaviour (decided; do not ask)
- Free ticket hides the price and early bird fields. How-many field never below the busiest-night sold count; Done with a lower value shows the error line and does not save. "Remove this ticket" disabled when anything is sold (with the hint). Early-bird warning when an early price is >= the main price (prototype rule). Writes follow the 12e pattern (`saveNow`/type RPC, toast with Undo where the checklist says so, `inFlight` guard, busy/disabled buttons, rollback on failure). Sold counts come from `useSummary`/types data already read on this screen.
- Weekly: early bird "Until" is one date for every night (hint SH-TICKET-13); one-off: no hint.
- `soldCount` for cancel sheets stays out of scope (slice 15).

## Checklist (build every row)
## SH-TICKET: ticket sheet ("Ticket" / "Add a ticket")

- [ ] SH-TICKET-01 Title "Ticket"; new one "Add a ticket"
- [ ] SH-TICKET-02 Field "Ticket name", placeholder "For example Class and party"
- [ ] SH-TICKET-03 Switch row "Free ticket"
- [ ] SH-TICKET-04 Free: no price fields; hint "Sales stay open until the night ends."
- [ ] SH-TICKET-05 Field "Price (£)", placeholder "Add a price"; with early birds the label is "Price after early bird (£)"
- [ ] SH-TICKET-06 Field "How many available each night"
- [ ] SH-TICKET-07 Hint "<max> already sold on the busiest night" (when any sold)
- [ ] SH-TICKET-08 Error "How many available cannot be lower than <max>, the number already sold."
- [ ] SH-TICKET-09 Label "Early bird prices"; one row per early bird: "Price (£)", "Until" (date) and a 44px "×" (aria-label "Remove this early bird price"), grid 1fr 1.4fr 44px, 8px gap
- [ ] SH-TICKET-10 Hint with early birds: "Then £12. Sales stay open until the night ends."; without: "One fixed price. Sales stay open until the night ends."
- [ ] SH-TICKET-11 Warning box "Check the prices: early bird prices usually go up over time and stay below £12." (when they do not)
- [ ] SH-TICKET-12 Button "Add an early bird price" (secondary)
- [ ] SH-TICKET-13 Hint "Early bird prices end on the same date for every night." (weekly)
- [ ] SH-TICKET-14 Line "<n> sold across all dates, £<x>" (existing ticket)
- [ ] SH-TICKET-15 Button "Done" (primary); toast "Saved" with Undo
- [ ] SH-TICKET-16 Button "Remove this ticket" (danger); toast "Ticket removed" with Undo
- [ ] SH-TICKET-17 With tickets sold: "Remove this ticket" is disabled, with the hint "Tickets are already sold, so this ticket cannot be removed."

Tap rows: 91 ticket card -> Sheet 'Ticket'; 92 Add a ticket -> Sheet 'Add a ticket'; 102 Free switch (same sheet, fields hidden/shown); 103 Add an early bird price (+1 row Price + Until); 104 x remove early bird row; 105 Done (below sold: error line, else close + toast Saved + Undo); 106 Remove this ticket (close, toast 'Ticket removed' + Undo; disabled when sold).

## Prototype code (frozen; handlers and sheet HTML)
```js
tFree:function(){readTicket();D.free=!D.free;ticketSheet()},
tStageAdd:function(){readTicket();D.stages.push({price:Math.max(1,D.price-2),until:''});ticketSheet()},
tStageDel:function(i){readTicket();D.stages.splice(+i,1);ticketSheet()},
tDone:function(){readTicket();var max=Math.max.apply(null,D.sold);
  if(D.qty<max){D.err='How many available cannot be lower than '+max+', the number already sold.';ticketSheet();return}
  var t={id:D.id,name:D.name||'Ticket',free:D.free,price:D.price,qty:D.qty,stages:D.free?[]:D.stages,sold:D.sold},found=false;
  commit(function(){S.tickets=S.tickets.map(function(x){if(x.id===t.id){found=true;return t}return x});if(!found)S.tickets.push(t)},'Saved')},
tRemove:function(){var id=D.id;commit(function(){S.tickets=S.tickets.filter(function(x){return x.id!==id})},'Ticket removed')}
  if(!$('t-name'))return;D.name=v('t-name');
  if($('t-price'))D.price=num(v('t-price'));
  D.qty=Math.round(num(v('t-qty')));
  D.stages.forEach(function(s,i){if($('e-price-'+i)){s.price=num(v('e-price-'+i));s.until=v('e-until-'+i)}});
}
function ticketSheet(){
  var d=D,sold=soldAll(d),max=Math.max.apply(null,d.sold);
  var h=fld('t-name','Ticket name',d.name,'text',' placeholder="For example Class and party"')
    +B('swrow','tFree','<span>Free ticket</span><span class="sw'+(d.free?' on':'')+'"></span>',null,' role="switch" aria-checked="'+d.free+'"');
  if(d.free)h+='<p class="hint">Sales stay open until the night ends.</p>';
  else h+=fld('t-price',d.stages.length?'Price after early bird (£)':'Price (£)',d.price,'number',' min="0" step="0.5" inputmode="decimal" placeholder="Add a price"');
  h+=fld('t-qty','How many available each night',d.qty,'number',' min="0" step="1" inputmode="numeric"')+(max?'<p class="hint">'+max+' already sold on the busiest night</p>':'')+(d.err?'<p class="err">'+esc(d.err)+'</p>':'');
  if(!d.free){
    h+='<div class="grp"><div class="flab">Early bird prices</div>'+d.stages.map(function(s,i){return '<div class="stage">'+fld('e-price-'+i,'Price (£)',s.price,'number',' min="0" step="0.5" inputmode="decimal"')+fld('e-until-'+i,'Until',s.until,'date')+B('xbtn','tStageDel','&times;',i,' aria-label="Remove this early bird price"')+'</div>'}).join('')
      +'<p class="hint">'+(d.stages.length?'Then '+gbp(d.price)+'. Sales stay open until the night ends.':'One fixed price. Sales stay open until the night ends.')+'</p>'
      +(d.stages.some(function(s){return s.price>=d.price})?'<p class="warn">Check the prices: early bird prices usually go up over time and stay below '+gbp(d.price)+'.</p>':'')
      +B('btn sec','tStageAdd','Add an early bird price')+(S.oneOff?'':'<p class="hint">Early bird prices end on the same date for every night.</p>')+'</div>';
  }
  if(!d.isNew)h+='<p class="txt">'+sold+' sold across all dates, '+gbp(sold*usualPrice(d))+'</p>';
  h+=B('btn pri','tDone','Done');
  if(!d.isNew)h+=B('btn dan','tRemove','Remove this ticket',null,sold?' disabled':'')+(sold?'<p class="hint">Tickets are already sold, so this ticket cannot be removed.</p>':'');
  d.err='';
  sheet(d.isNew?'Add a ticket':'Ticket',h);
```

## Done
`npx vitest run src/modules/organiser/p3/v2` green, tsc 0 errors in touched files, `npm run check:mojibake` clean. Fill a tap table (button | destination | prototype/OLD) for 91,92,102-106 in `docs/briefs/editor-v2-slice13-ticket-sheet.handover.md`. No PR (stacked on claude/editor-v2-s1c).
