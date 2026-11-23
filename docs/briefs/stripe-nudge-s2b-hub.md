# Stripe for organisers v2, S2b: Get paid hub (3 steps) and one route for every Connect button
Repo: bachata-website. Branch: claude/stripe-nudge-s2b-hub. Builder: Sonnet. Flag: `stripeNudgeV2`.
Size: 4 files, 300 lines
Design registry row: STRIPE FOR ORGANISERS v2. Prototype: board 11 (Hub-return.dc.html).
Files: `src/modules/organiser/p3/tickets/GetPaid.tsx`, `TypeSheet.tsx` (connect routing only), `TicketsEntry.tsx` (connect routing only), one test file.

Brief-version: 2

## What main lacks
- Connect buttons land in different places and the Get paid page has no 3-step list. -- grep: get-paid-hub-steps

## Files
- `src/modules/organiser/p3/tickets/GetPaid.tsx`
- `src/modules/organiser/p3/tickets/TypeSheet.tsx`
- `src/modules/organiser/p3/tickets/TicketsEntry.tsx`
- `src/modules/organiser/p3/tickets/model.ts`

## Prototype checklist
- [ ] Hub not started: title "Get paid"; card with label "Set up in 3 steps" and three checkbox rows: "1. Have your bank details and photo ID ready", "2. Connect Stripe (about 5 minutes)", "3. Come back here: you're done"; full-width 48px orange button "Connect Stripe"; note "No commission from Bachata Calendar. Stripe card fees apply."
- [ ] Hub back from Stripe and connected: green banner "Stripe is connected. Dancers can buy tickets."; the 3 rows ticked and relabelled "1. Bank details and ID", "2. Connect Stripe", "3. Done".
- [ ] Hub half-finished: ticks reflect real state (step 2 ticked once an account exists), button "Finish setting up".
- [ ] "Your ticket link" card, shown ONCE only when connected AND the night uses an external ticket link: label "Your ticket link", "<Event> sells through <host>", body "Sell in the app instead and dancers pay without leaving. Your link is kept: you can switch back.", buttons "Sell in the app instead" (primary 42px) and "Keep my link" (outline). Dismissal remembered per organiser. If "Sell in the app instead" needs a write path that does not exist, STOP and say so; do not invent one.
- [ ] "Money so far" and payouts cards unchanged below.
- [ ] Every Connect button in the app (ticket sheet "Open Stripe", Tickets entry, etc.) lands on this hub first; the hub's one button goes to Stripe.
- [ ] Return from Stripe (`?stripe=return|refresh`) behaviour and `useConnectReturn` polling unchanged.
- [ ] Records `tapped` and `returned` via the S1 RPC if it exists; else skip silently.
## Rules
Prototype is the whole spec; TAP TABLE at end (button | destination | prototype/OLD); one OLD row = keep building. Keep-list unit test of the live GetPaid screen (every badge, line, control). Flag off = today.
## Inventory first
Before building, write INVENTORY.md beside the handover: every field, state and option on the live screens this brief touches (admin app, current Website, public page), file:line cited, GAPs listed. End the handover with a coverage count (elements in prototype / built).
