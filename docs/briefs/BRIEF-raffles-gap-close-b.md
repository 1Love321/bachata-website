Branch: claude/raffles-gap-close-b
Size: 5 files, 260 lines

# /raffles Spotlight C gap-close B: entered state on cards, entry dialog copy, party mode (Website, behind existing flags)

Prototype (whole spec): docs/briefs/raffle-spotlight-c-prototype.md (HTML in a fence; read CSS `.enter`, `.is-in .enter`, `.seal`, `.sheet*`, `.party .beam`, and the script blocks `LOOK.card`, `LOOK.onEnter`, `LOOK.celebrate`, `openSheet`, submit handler). Design registry row 82 (owner picks 2026-11-22/23). PR Website #1973 (gap-close A) is already merged: build on it. Touch only `src/components/raffles/RaffleNightCarousel.tsx`, `RaffleRedesignA.tsx`, `RaffleRedesignA.css`, `src/modules/event-page/bento/modals/RaffleEntryDialog.tsx` and their tests. Stays behind `flags.raffleV2 && flags.raffleRedesign`; the dialog copy changes apply ONLY when the new optional prop `redesign` is true (RaffleRedesignA passes it); every other caller of RaffleEntryDialog is unchanged. No new flag.

## Prototype checklist (every line must be built)
- [ ] Night card, not entered: button "Enter" (orange pill, 50px min height, glow shadow, tap scale .93) as today.
- [ ] Night card, entered: button becomes ghost: transparent, accent-hot text, inset 1.5px accent ring, label = tick icon + "You're in" (`.is-in .enter`).
- [ ] Night card, entered: 84px orange seal disc with tick centred over the poster art (`.seal`, radial gold, ring, `sealpop` animation 0.7s, spin from -120deg); reduced-motion = no animation.
- [ ] "Entered" = existing local entered state of the board (whatever sets the sticky bar entered state); no new storage.
- [ ] Tapping an entered card button does NOT open the dialog: it fires a small 18-piece confetti burst from the button (prototype `burst(..., 18, {spread: 2PI, power: 6})`); if the repo has no confetti util, add a small canvas one in RaffleRedesignA (no new dependency); reduced-motion = no burst.
- [ ] Dialog (redesign only): eyebrow "Free raffle"; h2 "Enter the raffle"; for-line "You picked <night name>. Your entry covers every other night too." (no night picked: "One entry puts you in the draw for every night."); label "Your mobile number"; placeholder "07123 456789"; note "Free · one phone number covers every night"; submit "Enter the raffle".
- [ ] Dialog error lines (redesign only): empty = "Add your mobile number to enter."; invalid = "That does not look like a UK phone number. Try it like 07123 456789."; field shakes and refocuses on error.
- [ ] Dialog done state (redesign only): 88px orange tick disc with drawn tick, h2 "You're in!", text "Your number is in the draw for every night. Winners are announced weekly.", button "Done" (closes).
- [ ] Party mode on successful entry: body/board gets a `party` class for 4500ms: both hero beams animate at 1.1s (prototype `.party .beam` brighter gradient; if the S2a beams use another duration, use prototype 1.1s as spec), then class removed.
- [ ] Party mode confetti: 7 bursts, 260ms apart, 46 orange/white pieces each (colours #FFA600 #FFD58A #FFFFFF #FF7A00), random positions in the upper half (x 15-85%, y 12-52% of viewport), power 9-13; reduced-motion = none.
- [ ] Entries orb: on successful entry the "Entries" orb (testid `raffles-count-pool`) counts n -> n+1 over 500ms (reduced-motion = jump).
- [ ] Escape and scrim close the dialog; focus trapped; focus returns to the trigger (keep what the dialog already does; add only if missing).

## Tests (block merge)
- Component keep-list test for RaffleRedesignA: not-entered card shows "Enter" and no seal; entered card shows "You're in" and the seal; tapping an entered card does not open the dialog.
- RaffleEntryDialog test with `redesign`: exact strings for eyebrow, for-line (picked + not picked), label, placeholder, note, both error lines, done heading + text. Existing dialog tests (no `redesign`) still pass unchanged.
- Entries orb goes n -> n+1 after a mocked successful entry; `party` class set then cleared (fake timers).

## Rules
- Any tap that leaves the prototype (old page, today's screen) = not done. End the handover with a TAP TABLE: button | destination | prototype/OLD. One OLD row = keep building. A prototype element you cannot build: STOP and write it at the top of the handover.
- Anything noticed but not listed here stays as today; list it in the PR.
- One PR, never merge, stop after the push and report.
