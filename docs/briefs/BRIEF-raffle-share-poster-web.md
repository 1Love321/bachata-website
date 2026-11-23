Branch: claude/raffle-share-poster-web
Size: 5 files, 380 lines

# Winner page "Share your win" step + canvas share poster (Website, behind new flag raffleSharePoster)

Prototype (whole spec): docs/briefs/raffle-share-poster-prototype.md (HTML in a fence; poster = a2 "Door ticket": CSS `.tk .tp .rw .pf .bt .ft .ct .nm .won .ti .me .url .mk .fly .nf`, JS template `T.a2`; step mock = the `.prev` frame). Design registry row 95 (owner pick A2, 2026-11-23). Data is live: admin migration 20261123113300 (merged, applied) adds `poster_url` to every night object and top-level `first_name` to `raffle_win_claim_link_v1` / `raffle_win_lock_v2` results. `pool_count` comes from the existing `raffle_public_board_v1` RPC.

Touch only: `src/lib/featureFlags.ts` (new flag), `src/modules/raffle-win/model.ts` (+ its test), `src/pages/RaffleWin.tsx`, a NEW `src/modules/raffle-win/sharePoster.ts` (canvas drawing + share/save helpers), and tests. No new dependency. Flag: `raffleSharePoster`, env `VITE_ENABLE_RAFFLE_SHARE_POSTER === 'true'`, default OFF, same pattern as `raffleRedesign`. Flag OFF = winner page byte-for-byte today's behaviour.

## Data
- `parseNight` also reads `venue_name` (string|null), `poster_url` (string|null), `public_event_id`. Existing fields and callers unchanged.
- `parseClaim` / `parseLock` also return `firstName` (top-level `first_name`, string|null) and the night from `win.night`.
- Draw count N = `pool_count` from `raffle_public_board_v1` via the existing Supabase client; fetched once when the share step opens; on failure the pill is omitted (poster still works).

## Prototype checklist (every line must be built)
- [ ] New screen `share` between `honest` (lock confirmed) and `qr`: shown ONLY after a successful lock in this visit, flag ON. Returning to an already-locked win goes straight to `qr` as today (no share step).
- [ ] Share screen: eyebrow "Night confirmed"; heading "Share your win"; sub "Tell your friends you are going. Your QR code is on the next screen."; live poster preview (4:5, rounded, shadow); buttons: primary "Share" (orange), secondary "Save image", text link "Skip, show my QR". Share and Save do NOT leave the step; only "Skip, show my QR" (and after a successful Share) go on to `qr`. After Share resolves or Save is tapped, the text link label stays; no extra copy.
- [ ] Share = `navigator.share({ files:[png], text })` when `navigator.canShare({files})` is true; otherwise falls back to download (same as Save image). User cancelling the share sheet (AbortError) = stay on the step, no error.
- [ ] Save image = downloads `raffle-win-<slug>.png`.
- [ ] Poster, 4:5, canvas export 1080x1350 PNG, white + orange `#C2410C` (prototype tokens): orange frame; white ticket; top part: "Bachata Calendar" mark top-left, flyer 40cqw x 50cqw left (event `poster_url` cropped to fill, cover; if no poster_url or it fails to load: orange block with the title in white 800 weight, bottom-left, `.fly.nf`), winner first name big orange to the right, "won a free night" below it; dashed tear line with side notches (`.pf`); bottom: "at <title>" ("at" small), date/time line "Thu 27 Nov · 20:30" (London wall clock from `start_local`, no timezone maths) and venue line (omit if venue null); pill "N dancers in the draw" (`.ct`, omit if N unknown); link text under it (event page URL as the site builds it today, long form; strip the protocol).
- [ ] First name null: poster headline becomes "A dancer" (no blank). Long first names / titles shrink to fit on one / two lines with ellipsis (canvas measureText loop); never overflow the frame.
- [ ] Poster image load: `poster_url` fetched with `crossOrigin='anonymous'`; if the canvas gets tainted or load fails, draw the no-flyer variant (never an error, never a blank poster).
- [ ] Reduced motion: no animation on this screen. No confetti.
- [ ] Back button / browser back from `share` behaves like from `qr` today.

## Tests (block merge)
- model: parseNight keeps old fields and adds venue/poster/public id; parseClaim/parseLock return firstName; missing keys = null, no throw.
- Screen table: flag ON + lock ok -> `share`; `share` + skip -> `qr`; already-locked claim -> `qr`; flag OFF + lock ok -> `qr` (today).
- sharePoster: pure layout function returns the exact strings (headline, "won a free night", "at <title>", date line, venue line, pill text, link) for sample data; no-poster variant; null first name -> "A dancer"; no pool_count -> no pill.
- RaffleWin keep-list: every existing control on today's screens (won, pick, honest, qr, none, cancel, other) still rendered with flag ON and OFF.

## Rules
- Any tap that leaves the prototype (old page, today's screen) = not done. End the handover with a TAP TABLE: button | destination | prototype/OLD. One OLD row = keep building. A prototype element you cannot build: STOP and write it at the top of the handover.
- Anything noticed but not listed here stays as today; list it in the PR.
- One PR, never merge, stop after the push and report.
