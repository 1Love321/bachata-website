# Review PR #1934 head 58c9820e05d6642f902e1c85db51c19aca67e25b
- NightsScreen.tsx:14 (nit): `Sheet` is statically imported from ../options/optionsUi even with the flag off. No render change, but the import is unused weight when the flag is off. Acceptable if the bundle budget is green; otherwise lazy-load the sheet shell.
- NightTicketQr.tsx:20-30 (nit): the feed has no error/retry path and re-calls onTickets whenever `nights` identity changes. The ticket matching is cheap and idempotent, so this is harmless. fetchMyTickets runs once per mount, so a ticket bought while the screen is open does not appear until remount.
- nightsTicket.ts:20-22 (nit): a series-wide ticket matches only on exact date equality with night.date. This is deliberate and tested. It relies on the RPC dating series-wide tickets to the next night, so a mismatch just hides the button (fails closed).
- Checked, no issue:
  - Flag off: qrOn is false, so NightTicketQr and NightQrBody are never rendered and the lazy chunk is never fetched; onQr is undefined, so no button.
  - Ticket scope: tickets come from my_tickets_v1 (the caller's own) and are matched on occurrenceId, or series-wide on eventId+date, never on title. Used, refunded, cancelled and token-less tickets are filtered out. qrToken stays in props and is not logged.
  - NightQrBody: `key` on occurrenceId resets the index; Math.min clamps the index; zero tickets returns null; the Sheet only renders while byNight has the key.
  - Prev/next: 44px buttons with aria-labels, wraparound via stepTicket.
  - QR trigger: 44px, aria-label includes the night name.
  - JSX punctuation: no raw unicode punctuation in the diff.
  - Hooks: all hooks sit above the conditionals in NightsView.
## Verdict: PASS WITH NITS
