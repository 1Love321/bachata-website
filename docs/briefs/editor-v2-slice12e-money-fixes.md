# Brief: Editor v2 slice 12e, money-path fixes from the independent review of 12a-12d

Branch: claude/editor-v2-s1c
Size: 3 files, 250 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change. No visual change: every fix is behaviour, copy stays as is.
Design: FROZEN prototype, DESIGN-REGISTRY rows 61-81 (unchanged; no new element, no tap changes).

Read first: `docs/briefs/editor-v2-slice12d-stripe-switch.handover.md` (on this branch). Money slice: be exact; every fix gets a test.

## Files (touch only these)
- src/modules/organiser/p3/v2/TicketsV2.tsx
- src/modules/organiser/p3/v2/PaySheets.tsx (busy/disabled props only)
- src/modules/organiser/p3/v2/__tests__/moneyFixes.test.tsx (new)
Do NOT edit payMode.ts, editorModel.ts, saveNow.ts, copy.ts, Facts.tsx, shared/, p3/tickets/*. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Fixes (line numbers are TicketsV2.tsx at 69a2a4ae9; re-find them)
1. Stripe return (:170-182): replace the one-shot refetch with `useConnectReturn(back.kind !== null, connected, refetch)` from `p3/tickets/connectReturn.ts` (polls 3 s for 30 s; read it, reuse as GetPaid does). `stripeWarn` shows only after polling ends still not connected; it clears the moment `connected` turns true.
2. Return effect: it has `[]` deps and returns early when `!orgId`. Re-run once `orgId` is known; delete the `?stripe=` param only after handling. `refresh` (expired Stripe link) restarts onboarding via `onboard.go()` instead of showing the warning; `return` is the only kind that can warn.
3. Order of writes in `change` (:211-212): pause/reopen the ticket types FIRST, write the series (`default_ticket_url`) LAST. If any step throws, roll back the completed steps in the catch (restore each type's prior status) and show the existing error toast. No half-switched state.
4. Reopen scope (:152, :238-239): `sellHere` with `reopen: paused` reopens ONLY the types the switch itself paused (track their ids when switching away), never types paused by hand. Undo restores EACH type's own prior status captured at click time, and skips a type whose current status changed since.
5. Sold guard (:241-246, :160-163): before deciding door/link while mode is `here`, refetch the sales summary and use the fresh result. Fresh summary not ok: do NOT act; show the existing warning line/toast, never a silent no-op, and keep the buttons enabled for retry.
6. In-flight guard: an `inFlight` ref in `change`; second call while one runs is ignored. Sheet buttons (`link-done`, `how-door`, `switch-do`) get `disabled` + `aria-busy` from a `busy` state (PaySheets prop only).
7. `inverse` (:222): if `undoPlan` returns null after steps ran, do not show an Undo action (toast without Undo); never drop it silently when steps is empty-and-nothing-changed.
8. Check only, report in the handover, change nothing: (a) multi-org events use `organiserIds[0]`: note whether the return URL's organiserId should be read; (b) `TicketsView` first branch `!p.stripe` always shows Connect: compare with the prototype for an organiser in door/link mode with no Stripe (checklist rows 88-97) and say whether the door/link footer must show; also the Connect screen must not flash while `stripe.state` is still loading.

## Tests (every state)
One test per fix 1-7 (late webhook then connected clears warning; refresh restarts onboarding; mid-failure rollback leaves types as before; hand-paused type not reopened; Undo skips a changed type; stale summary refetched; double tap writes once; null inverse shows no Undo).

## Done
`npx vitest run src/modules/organiser/p3/v2` green, tsc 0 errors, `npm run check:mojibake` clean. Write `docs/briefs/editor-v2-slice12e-money-fixes.handover.md`. No PR (stacked on claude/editor-v2-s1c).
