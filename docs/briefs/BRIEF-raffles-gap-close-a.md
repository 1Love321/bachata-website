Branch: claude/raffles-gap-close-a
Size: 4 files, 200 lines

# /raffles Spotlight C gap-close A: winners, footer, sticky bar (Website, behind existing flags)

Prototype (whole spec): docs/briefs/raffle-spotlight-c-prototype.md (HTML in a fence; read the CSS `.medals/.medal*`, `.foot*`, `.bar*`). Design registry row 82 (owner picks 2026-11-22/23). Build only inside `src/components/raffles/RaffleRedesignA.tsx`, `RaffleRedesignA.css`, `src/lib/raffleBoard.ts` and their tests. Stays behind `flags.raffleV2 && flags.raffleRedesign`; flag off = unchanged. No new flag.

Data note: DB slice S1 (branch claude/raffle-poster-data-v1, may not be merged yet) makes `raffle_public_board_v1.winners[]` carry `last_initial` (nullable) and `profile_slug` null. Parser must work with BOTH shapes: `profile_slug` optional; `last_initial` optional; a winner needs only `first_name`.

## Prototype checklist (every line must be built)
- [ ] Winners section heading is "This week's winners" (today "Recent winners"), centred, italic display font as prototype `h2`.
- [ ] Winners grid: 2 columns on phone, 4 columns from 700px, gap 44px 14px, centred text (`.medals`).
- [ ] Each winner = trophy disc 84px (radial gold, trophy svg 40px, spinning conic ring `i`, glow), NOT a link, NO "View profile" line, no `Link` to /dancers.
- [ ] Winner name = first name + " " + last initial + "." when `lastInitial` present (e.g. "Maya R."), else first name only; gradient-sheen name style as `.medal b`.
- [ ] Under the name: night title (muted) then "won a free entry" (orange small caps `.medal em`).
- [ ] Drop-in reveal animation staggered by index (`drop`, IntersectionObserver `seen` class); reduced-motion = no animation.
- [ ] Section hidden when no winners. Keys must not use profileSlug.
- [ ] Remove the gold/silver/bronze MEDALS styling.
- [ ] Footer: links "Rules" (to /terms) and "Contact" (mailto) ONLY. Remove "Privacy". No Reset button.
- [ ] Footer one-liner always visible under the links, exact text: "Free to enter · one phone number per night. We only use it to message winners." (class `.foot__info`, ink colour, max 34ch, centred).
- [ ] Sticky bar not entered: keep today's text and button, add the glowing ring around the button (`.bar__btn::before` conic ring, `glow` animation, reduced-motion off).
- [ ] Sticky bar entered: 44px orange disc with tick + "You're in for every night" in italic display, accent-hot colour (`.bar__done`), drop-in animation. No Reset.
- [ ] Orbs count up from 0 on first load (1100ms + 350ms per orb, ease-out cubic), reduced-motion = final number at once; final numbers unchanged (poolCount, open nights, winners listed).

## Tests (block merge)
- Update `src/lib/__tests__/raffleBoard*.test.ts`: winner without profile_slug parses; with last_initial parses; blank first_name dropped.
- Component test for `RaffleRedesignA` keep-list: hero h1 "Win a free night", 3 orbs with testids `raffles-count-pool|open|winners`, 3 steps, carousel present, winners heading + "Maya R." + "won a free entry" + NO link in winners, footer Rules + Contact + one-liner and no Privacy, sticky bar in both states, entry dialog opens from `raffles-enter`.

## Rules
- Any tap that leaves the prototype (old page, today's screen) = not done. End the handover with a TAP TABLE: button | destination | prototype/OLD. One OLD row = keep building. A prototype element you cannot build: STOP and write it at the top of the handover.
- Sticky-bar not-entered subline and anything else not listed here stays as today; list anything you notice but did not change in the PR.
- One PR, never merge, stop after the push and report.
