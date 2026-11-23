# Party chat S1: Nights card (Website, web only)

Branch: claude/party-chat-s1
Size: 5 files, 300 lines
Flag: VITE_ENABLE_PARTY_CHAT (default OFF; flag off = Nights screen byte-for-byte as today, no RPC call)
Design registry rows: PARTY CHAT CORRECTION, PARTY CHAT CARD RULES, PARTY CHAT CARD LOOK PICKED (owner 2026-11-23).
Prototype: https://claude.ai/artifact/Stqxvpq9kV1S2ZuZUYEZSw option C (C1 and C3 only; C2 confirm step DROPPED by owner).
Code: src/modules/dancer-app/screens/nights (NightsScreen.tsx, nightsUi.tsx, useMyNights.ts). Night rows, headings, bell, Switch, tabs UNCHANGED.

## Backend (already live on prod, do not touch supabase/)
- `get_party_card_v1()` -> `{ state: "none"|"in"|"open"|"closed", city_id, chat_id, closes_at }` (authenticated; never a count).
- `register_party_interest_v1(p_city_id uuid, p_party_date date default null)` -> `{ ok:true, already, ...card }` or `{ ok:false, error: "not_signed_in"|"no_dancer_profile"|"not_today"|"city_not_found" }`.
- Cities: table `cities` (`is_active`); reuse an existing city list/picker in the Website if one exists, else read active cities. City name needed for "You're in · London" (state `in` returns city_id only: resolve name from the same list).
- Profile city: reuse how the dancer profile stores its city (find it; do not invent a column).

## Prototype checklist
- [ ] Card at the TOP of Nights, above "Coming up"; night rows unchanged; no chat button on rows.
- [ ] State `none`: headline "Pre-party or after-party tonight?"; sub "Tap your city if you're up for a pre-party or after-party tonight."; first button = the person's profile city (if they have one), then a ghost button "Other city" that opens a list of active cities (44px rows, tap a city = join).
- [ ] One tap on a city joins at once: NO confirm step, NO copy saying it cannot be undone, NO Leave button.
- [ ] Optimistic: buttons disabled while the call runs; on `ok:false` show a short plain error line inside the card ("Couldn't join. Try again.", `not_today`: card refreshes and hides).
- [ ] State `in`: violet fill card, h3 "You're in · <City>", sub "The chat opens tonight if enough people join. You'll get a notification." No button, no count.
- [ ] State `open`: card "Party chat is open · <City>" + one 44px button "Open chat" -> navigates to `/account/party-chat/<chat_id>` (screen is slice S2; route may 404 until S2 lands, behind flag).
- [ ] State `closed` or no signed-in dancer profile or call error on load: no card at all (silent).
- [ ] Loading: no card (no skeleton jump).
- [ ] Dark mode via existing tokens; 390x844 no horizontal scroll; every tap target >= 44px.
- [ ] Test: card matrix (flag off/on x four states x profile-city present/absent), tap joins with right city_id, error line, flag-off makes zero RPC calls.

## Rules
- Any tap that leaves the prototype (old page) = not done. End the handover with TAP TABLE: button | destination | prototype/OLD. One OLD row = keep building.
- A prototype element you cannot build: STOP and put it at the top of the handover.
- Do not add anything not listed (bell alert, push, count, leave). List extras you noticed in the handover, never build them.
- Opens one PR, never merges.
