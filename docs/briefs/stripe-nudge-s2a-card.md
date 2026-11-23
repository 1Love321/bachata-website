# Stripe for organisers v2, S2a: Stripe status card on Home and Tickets
Repo: bachata-website (this repo). Branch: claude/stripe-nudge-s2a-card. Builder: Sonnet. Flag: new `stripeNudgeV2` (VITE_ENABLE_STRIPE_NUDGE_V2), default off.
Size: 5 files, 300 lines
Design registry row: STRIPE FOR ORGANISERS v2. Prototype: mockup https://claude.ai/artifact/GeB4YA7AthDyoonDzeJSb8 board 10 (Status-card.dc.html) Option A, plus boards 1-2 (Main, Home-live) for placement.
Files: `src/modules/organiser/p3/r4/R4Home.tsx`, `R4Tickets.tsx`, new `src/modules/organiser/p3/r4/StripeStatusCard.tsx` (new), new `r4StripeCardModel.ts` (new), one test file.

Brief-version: 2

## What main lacks
- Home and Tickets show nothing about Stripe, so new organisers never find payouts. -- grep: stripe-status-card

## Files
- `src/modules/organiser/p3/r4/R4Home.tsx`
- `src/modules/organiser/p3/r4/R4Tickets.tsx`
- `src/modules/organiser/p3/r4/StripeStatusCard.tsx` (new)
- `src/modules/organiser/p3/r4/r4StripeCardModel.ts` (new)
- `src/lib/featureFlags.ts`

## Prototype checklist
- [ ] ONE card, look A (outlined card, orange 1px border `#C2410C`, white fill, R4 card radius 8, 12px pad).
- [ ] State not started: title "Sell tickets for this night" (16px, 600), body "Dancers pay by card. No commission from us." (14px), full-width 42px orange button "Set up payouts".
- [ ] State half-finished (Stripe account exists, not fully connected): title "Nearly there: 2 of 3 done", 6px progress bar 66% orange, button "Finish setting up".
- [ ] State connected: card not rendered at all.
- [ ] Home placement: under the tiles row, ABOVE "Share this night", on BOTH empty-organiser Home and live-event Home. No banner anywhere.
- [ ] Tickets tab placement: the same card at the very top of the tab, above the existing content.
- [ ] Only shown to roles that can set up payouts (`canSetUpPayouts`); others see no card.
- [ ] Both buttons route to the Get paid hub (S2b): `P3_PATHS` you/get-paid. Until S2b merges the hub is the existing GetPaid screen, so route there.
- [ ] Everything else on both screens unchanged: tiles, Share button, link rows, "To do" badge, waiting list, tab bar.
- [ ] Flag off: screens identical to today.
- [ ] Records `shown` and `tapped` via the S1 RPC once S1 is merged; if the RPC does not exist yet, skip silently (no error UI).
## Rules
Prototype is the whole spec. End the handover with TAP TABLE: button | destination | prototype/OLD. One OLD row = keep building. Per-screen unit keep-list test for Home and Tickets (every badge, line, control asserted). Use px sizes (html font-size trap). HTML entities for punctuation in JSX. Stripe state from `useStripe` / `stripeStateOf`.
## Inventory first
Before building, write INVENTORY.md beside the handover: every field, state and option on the live screens this brief touches (admin app, current Website, public page), file:line cited, GAPs listed. End the handover with a coverage count (elements in prototype / built).
