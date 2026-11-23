# Stripe for organisers v2, S2c: create-flow Stripe chip and editor price sheet
Repo: bachata-website. Branch: claude/stripe-nudge-s2c-create. Builder: Sonnet. Flag: `stripeNudgeV2` (create flow also needs the existing CREATE_LOOK flag on).
Size: 3 files, 300 lines
Design registry row: STRIPE FOR ORGANISERS v2 (v1 KEPT parts). Prototype: board 4 Create-flow Option A, board 5 Editor-price Option B.
Files: `src/modules/organiser/p3/new/sheets.tsx`, `src/modules/organiser/p3/v2/PriceSheet.tsx`, one test file.

Brief-version: 2

## What main lacks
- Create flow and editor price sheet never offer Stripe first. -- grep: stripe-first-chip

## Files
- `src/modules/organiser/p3/new/sheets.tsx`
- `src/modules/organiser/p3/v2/PriceSheet.tsx`
- `src/modules/organiser/p3/new/__tests__/stripeChip.test.tsx` (new)

## Prototype checklist
- [ ] Create flow, Price sheet: first card "Sell tickets in the app" (outlined orange, tinted `#FFF1E8`), body "Dancers pay by card. Money goes to your bank. We take no commission. About 5 minutes to set up.", button "Connect Stripe and set a price" (42px); selected by default for a NEW organiser without Stripe. Button routes to the hub (S2b route) after saving the draft.
- [ ] Label "Other ways", chips Pay at the door / Bank transfer / Cash / Free beneath it.
- [ ] Underlined link "Use my own ticket site"; the ticket link input is hidden until it is tapped.
- [ ] Organiser already connected: the card reads as a normal Stripe-selected state, no Connect button (worker proposes copy in the PR, owner reviews in prod).
- [ ] Editor price sheet B: title "Price"; copy "Sell tickets in the app. Dancers pay by card, money goes to your bank, no commission from us."; big 48px button "Connect Stripe and set a price"; note "About 5 minutes. Have your bank details and ID ready."; two underlined links "Use my own ticket site instead" and "Pay at the door, bank transfer, cash or free".
- [ ] Price fact row in the editor UNCHANGED (still "Add a ticket").
- [ ] When Stripe is already connected the existing price sheet is unchanged.
- [ ] Records `shown`/`tapped` via the S1 RPC if it exists; else skip silently.
## Rules
Prototype is the whole spec; TAP TABLE at end; one OLD row = keep building. Keep-list unit test of both live sheets. Flag off = today. Check sheet files for existing copy lines before changing and list any dropped line in the PR.
## Inventory first
Before building, write INVENTORY.md beside the handover: every field, state and option on the live screens this brief touches (admin app, current Website, public page), file:line cited, GAPs listed. End the handover with a coverage count (elements in prototype / built).
