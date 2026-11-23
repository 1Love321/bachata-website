# Review: Website PR #1966 (party chat S2 screen)

Scope: `git diff 80829a66f 7c10fb5a3` (6 files). Contract: `docs/handover/party-chat-s0b.md` (admin repo, commit f16b52a38; not in working tree of admin, read via git).

## Verdict: PASS

## Findings

### Blocking
None.

### Checked and OK
- Flag off: `PartyChatRoute` returns `<Navigate to="/account" replace />` before `AuthGuard`/screen mount, so zero RPC (test covers). Flag read at render via `import.meta.env.VITE_ENABLE_PARTY_CHAT === 'true'`. Only the lazy chunk may load; no data call.
- Auth: flag on wraps screen in `AuthGuard` (signed-out goes to /auth with returnTo). Signed in without dancer profile gets `not_member` screen.
- Route: no earlier `/account/*` wildcard shadows it; sits inside the `Suspense`; added to `CATCHALL_CLIENT_PATHS`.
- RPC args/shape match the S0b contract: `get_party_chat_v1(p_chat_id,p_before,p_limit=50)`, `post_party_chat_message_v1(p_chat_id,p_body)`, `leave_party_chat_v1(p_chat_id)`, `report_party_chat_message_v1(p_message_id,p_reason)`, `mark_party_chat_read_v1(p_chat_id)`. Response parsing is defensive (`ok`, `error`, `chat`, `messages`, `has_more`). Error codes `too_long`, `slow_down`, `closed`, `not_member`, `empty`, `own_message` all handled. Older-page cursor = `messages[0].created_at` as contract says.
- XSS: bodies and names rendered as React text (`whitespace-pre-wrap break-words`); no `dangerouslySetInnerHTML`; avatar goes through `PersonAvatar` as an image URL.
- Leave: confirm sheet, "can't rejoin" copy, `not_member` on leave treated as success, navigates to `/app/nights` with replace; failure shows toast. Report: only `!m.mine` messages tappable in report mode, own_message tolerated, null reason allowed by RPC default, toast on success/failure.
- 500 limit: `maxLength=500`, counter at 450+, server `too_long` mapped. Send disabled when empty/sending.
- Layout 390x844: `fixed inset-x-0 top-0`, height = `visualViewport.height` or 100dvh, list scrolls, footer pinned, input 16px (no iOS zoom), 44px targets, `max-w-[78%]` bubbles with break-words, no horizontal overflow.
- Time: en-GB `hour12:false` Europe/London (h23, no "24:xx"); day dividers Today/Yesterday logic correct.
- perf-budgets.json: +1 KB for /event with a recorded reason; lazy chunk not in first load.

### Nits (non-blocking)
1. `send()` sets `stick.current = true`, then `apply()` overwrites it with `atBottom.current`. If the user sent while scrolled up, the list does not jump to their new message. Fix: let `apply` take a `forceStick` arg.
2. Poll has no in-flight guard; a slow response can overlap the next 5 s tick. Harmless (merge by id) but wasteful.
3. Merge only adds: a message an admin later removes stays on screen until reload (poll returns only newest 50 without it, merge never drops). Acceptable for S2; relevant once S0c moderation lands.
4. Poll errors after first load are silent; no "offline" hint.
5. Layout uses `top-0` with `visualViewport.height`; on iOS Safari when the viewport scrolls (`offsetTop > 0`) the header can sit above the visible area. Same pattern as existing organiser sheets, so consistent; confirm in the laptop walk.
6. Report-mode keyboard: only Enter activates a message (not Space) for `role="button"`.
7. Handover md and `PartyChatRoute` comments reference the flag file from the S1 Nights folder; fine, but the flag file lives under `screens/nights/` (cross-screen import).
8. Unlike the admin repo, `docs/handover/party-chat-s0b.md` is absent from the Website tree; the review used the admin repo copy at f16b52a38.
