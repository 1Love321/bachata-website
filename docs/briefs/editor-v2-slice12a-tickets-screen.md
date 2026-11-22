# Brief: Editor v2 slice 12a, Tickets screen (read side) and payMode

Branch: claude/editor-v2-s1c
Size: 4 files, 380 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.
Design: owner-picked FROZEN prototype, DESIGN-REGISTRY rows 61-81 (embedded below).

Read `docs/briefs/editor-v2-slice11c-cancel-event.handover.md` first (state of the branch). This slice builds the Tickets SCREEN and the pure pay-mode derivation. The sheets that open from it (SH-STRIPE, SH-DOOR, SH-PAY, switch-with-sold, ticket sheet) are slices 12b/13: in this slice those buttons render fully, with the right testids, and `onOpen` is undefined (a tap does nothing yet) EXCEPT the taps listed as wired below.

## Files (touch only these)
- src/modules/organiser/p3/v2/payMode.ts (new): pure `payModeOf({ ticketTypes, defaultTicketUrl, stripeConnected })` -> 'here' | 'door' | 'link'. Rule: any live (selling / on_sale) ticket type = 'here'; else default_ticket_url set = 'link'; else 'door'. Also pure `doorPriceOf(passes)`: the price of the usual price-list entry (`ownerPassesFromStored`, shared/seriesCommands.ts; first pass, normally named Entry); null when none. Reuse `Facts.tsx` `priceLabel` inputs, do not duplicate its rules.
- src/modules/organiser/p3/v2/TicketsV2.tsx (new): the screen, prototype `scrTickets()` below, all of T-01..T-19. Reads tickets and Stripe state through the EXISTING hooks (`organiser/tickets/ticketsApi.ts`, `p3/tickets/data.ts`, the same sources EveryBody/NightCard already use for the Price fact; do not add a fetch layer). Sold counts and per-date "<sold> of <how many>" from the existing tickets read; 0 when the read has none. The prototype-only "Prototype: show Stripe..." button is NOT built.
- src/modules/organiser/shell/P3Routes.tsx (or wherever v2 routes are registered; find where EditorV2 / NightV2 routes mount): route for the Tickets screen, behind the same v2 gate; back link T-01 returns to the first screen (tap 87).
- src/modules/organiser/p3/v2/__tests__/payMode.test.ts (new): payModeOf rules (live types win over link; link wins over door; none = door; archived/paused types do not count), doorPriceOf, and TicketsV2 render per mode (not connected, here with/without tickets, door, link, weekly vs one-off sub line and date list).
Wire tap 22 (weekly Price fact Change) and tap 27 (one-off Tickets row) to navigate to this screen from `Facts.tsx`/EveryBody/NightCard `onOpen` ONLY if a one-line prop pass is enough; otherwise list it in the handover as not wired.
Do NOT edit editorModel.ts, saveNow.ts, SaveBar, shared/, dates/, events/, tickets/ (read only). Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only (`&larr;`, `&rsquo;`, `&pound;` or the `£` via copy.ts constants as other v2 files do).

## Prototype (copy exactly)
```js
}
function scrTickets(){
  var h='<div class="head">'+B('back','goNight','&larr; '+esc(S.name))+'<h2 class="ttl">Tickets</h2>'+(S.oneOff?'':'<div class="sub">The usual prices, for every '+dayName()+'.</div>')+'</div>';
  if(!S.stripe){
    h+='<div class="grp">'+B('btn pri big','stripe','Sell tickets here')+'<p class="sent">Connect Stripe and get paid in seconds. No commission from Bachata Calendar. Stripe card fees apply.</p>'
      +(S.stripeWarn&&S.pay==='here'?'<p class="warn">Dancers can’t buy yet. Connect Stripe or choose pay at the door.</p>':'')+'</div>';
  }else if(S.pay==='here'){
    h+='<div class="grp"><div class="sub">'+(S.tickets.length?'Tap a ticket to change it.':'No tickets yet. Add your first ticket.')+'</div>';
    var ds=dates(6);
    S.tickets.forEach(function(t){
      h+=B('tcard','ticket','<b>'+esc(t.name)+'</b><span class="s">'+esc(ticketLine(t))+'</span><span class="s">'+soldAll(t)+' sold across all dates, '+gbp(soldAll(t)*usualPrice(t))+'</span>'
        +(S.oneOff?'':'<span class="plist">'+ds.map(function(d,i){return '<span>'+longDate(d)+'</span><span>'+(t.sold[i]||0)+' of '+t.qty+'</span>'}).join('')+'</span>'),t.id);
    });
    h+=B('btn sec','ticket','Add a ticket','new')+'</div>';
  }else{
    h+='<div class="grp">'+B('btn pri big','sellHere','Sell tickets here')+'</div>';
  }
  if(S.pay==='here')h+='<p class="sent">Not selling here? '+B('inl','payDoor','Pay at the door')+' or '+B('inl','link','add a link')+' instead.</p>';
  else if(S.pay==='door')h+='<p class="sent">Dancers pay '+gbp(S.doorPrice)+' at the door. '+B('inl','doorUsual','Change the usual price')+'</p><p class="sent">'+B('inl','howPay','Change how dancers pay')+'</p>';
  else h+='<p class="sent">Dancers buy at '+esc(S.link||'your link')+'. '+B('inl','howPay','Change')+'</p>';
  h+=B('proto','stripeToggle',S.stripe?'Prototype: show Stripe as not connected':'Prototype: show Stripe as connected');
  return h;
}

/* sheet, toast, save */
function sheet(title,html){$('shTitle').textContent=title;$('shBody').innerHTML=html;$('scrim').hidden=false;$('sheet').scrollTop=0}
```
Styles for classes btn pri big, btn sec, tcard, plist, sent, warn, inl, back, ttl, sub, grp: read them in the prototype css below and use v2.css tokens; add to v2.css only what is missing.
```css
27:.back{background:none;border:0;padding:0;min-height:44px;color:var(--p-mut);font-size:15px;text-align:left;align-self:flex-start}
29:.ttl{font-size:24px;font-weight:700;line-height:1.2;margin:0;text-wrap:balance}
97:.btn.big{min-height:56px;font-size:17px}
130:.warn{font-size:14px;color:#8A4B00;background:#FFF6E0;border-radius:8px;padding:10px 12px;margin:0}
133:.inl{background:none;border:0;padding:0;color:var(--p-acc);font-weight:600;text-decoration:underline;min-height:0}
142:.tcard{width:100%;text-align:left;padding:14px 16px;display:flex;flex-direction:column;gap:4px;border:1.5px solid #CFCFCF;border-bottom-width:3px;border-radius:10px;background:#FFF}
143:.tcard b{font-weight:600}
144:.tcard .s{font-size:14px;color:var(--p-mut)}
145:.plist{margin:6px 0 0;padding:0;list-style:none;font-size:13px;color:var(--p-mut2);display:grid;grid-template-columns:1fr auto;gap:2px 12px;font-variant-numeric:tabular-nums}
146:.sent{font-size:15px;color:var(--p-mut);margin:0;text-align:center}
447:  D.ctx=ctx;D.count=base.length;D.warn='';D.oldDancers=!!s&&s.type==='Party'&&(s.dancers||[]).length>0;sessSheet();
452:  D.type=t;D.warn=lost.length?lost.join(', ')+' will be removed.':'';sessSheet()},
609:    +(d.warn?'<p class="warn">'+esc(d.warn)+'</p>':'')
```

## Checklist (every id built)
## T: Tickets screen

- [ ] T-01 Back link "← <event name>"; returns to the first screen
- [ ] T-02 Title "Tickets" 24px bold
- [ ] T-03 Weekly sub line: "The usual prices, for every <day>." 15px #464646
- [ ] T-04 Stripe not connected: big primary button "Sell tickets here" (56px, 17px)
- [ ] T-05 Stripe not connected: line "Connect Stripe and get paid in seconds. No commission from Bachata Calendar. Stripe card fees apply."
- [ ] T-06 After "Not now": warning box "Dancers can’t buy yet. Connect Stripe or choose pay at the door."
- [ ] T-07 Selling here with tickets: sub line "Tap a ticket to change it."
- [ ] T-08 Selling here with no tickets: "No tickets yet. Add your first ticket."
- [ ] T-09 Ticket card: padding 14px 16px, border 1.5px #CFCFCF with 3px bottom, radius 10px; bold name; price line 14px #464646
- [ ] T-10 Ticket price line with an early bird: "£10 until Tuesday 1 December, then £12"; fixed: "£8"; free: "Free"
- [ ] T-11 Ticket card line "<n> sold across all dates, £<x>"
- [ ] T-12 Weekly ticket card: list of dates, two columns, 13px #696969: "<date>" and "<sold> of <how many>"
- [ ] T-13 Tapping a ticket card opens the ticket sheet
- [ ] T-14 Button "Add a ticket" (secondary); opens the ticket sheet, empty
- [ ] T-15 Selling here, footer line: "Not selling here? Pay at the door or add a link instead." with "Pay at the door" and "add a link" as inline links
- [ ] T-16 Pay at the door: button "Sell tickets here" (primary); toast "Selling tickets here"
- [ ] T-17 Pay at the door: line "Dancers pay £10 at the door." with inline link "Change the usual price"
- [ ] T-18 Pay at the door: link "Change how dancers pay"
(SH-STRIPE/SH-DOOR/SH-PAY ids are slice 12b.) T-06's "Not now" state needs the sheet: render the warning box only from a prop `stripeWarn` that 12b will set; test it with the prop.

## Tap table (put in `docs/briefs/editor-v2-slice12a-tickets-screen.handover.md`; columns button | destination | prototype/OLD; any OLD = not done)
| 87 | ← <event name> | Tickets screen top | first screen |
| 22 | Price … Change › | Every card | Tickets screen |
| 27 | Tickets (Prices and how dancers pay) | one-off event | Tickets screen |
| 93,94,95,96,97,98,99,100,101 + Sell tickets here without Stripe + ticket cards + Add a ticket | rendered, testids present | NOT wired (12b/13): list as "pending 12b/13", they are not OLD pages |

## Handover
Write the handover file, commit, push to claude/editor-v2-s1c. No PR. List what you verified and did not (390x844 walk, pre-ship). Run `npm run test:unit` on the touched folder.
