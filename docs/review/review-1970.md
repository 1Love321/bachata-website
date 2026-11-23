# Review of PR #1970 (party chat S3, head e6cebdca)

## Verdict: PASS
## Head: e6cebdcaad2b876d91a6a535bb378509c0adba1b
## Findings
### Blocking
None.
### Nits (non-blocking)
1. dropRemoved boundary tie: filter keeps a held message only if id in page or created_at < lo or > hi. A held message whose created_at EQUALS lo or hi but was cut off by the page limit (same-timestamp tie at the page edge) would be dropped. Very rare; fix is `<= lo`-style exclusion only for strictly inside range (lo < t < hi) or compare strictly.
2. Empty newest page returns cur unchanged, so a chat whose every message was removed keeps showing them until reload. Edge case, acceptable.
3. Bell: transient card RPC failure returns null -> alert vanishes -> pruneRead drops the read mark, so the alert re-shows unread on recovery. Brief says failure = no alert; read-loss is cosmetic.
4. Poll inflight guard has no timeout: a hung fetchChat never resolving blocks all later ticks. Normal fetch errors are fine (finally resets).
5. Bell shows Loading until the party card settles when partyOn (disclosed in handover).
### Checked OK
- Brief A: kind 'party', id party:<chat_id>, copy, MessageCircle tile, Link to /account/party-chat/<id>, read via same store, prune via pruneRead; order party,ticket,rate; cap after merge; only state open + chat_id; null/failed card = no alert. Tests cover each (model, order+cap, tap+read, in/none/closed, failure, prune, flag-off zero calls).
- Flag-off inert: partyOn inline env check; useQuery enabled false (react-query v5: isLoading false when disabled) so no RPC and no loading impact. fetchPartyCard is a dynamic import inside the bell chunk; no new first-load import. CI bundle-budget SUCCESS.
- Brief B: send sets atBottom and forceStick, mergeMessages always returns a new array so layout effect fires; inflight guard correct (live check + finally); dropRemoved applied only on non-older applies, so Show earlier pages (older than lo) and messages newer than page (sent between polls, concurrent send/poll race) are kept; Space handled with preventDefault. Tests for each, including fake-timer poll tests.
- CI at PR #1968 all relevant checks green (unit shards, bundle-budget, dancer-area-isolation, build-smoke, phone-qa).
- Diff text is ASCII (uses \u escapes); handover has TAP TABLE and lists extras not built. 6 files vs brief's 5 is explained.
- Not verified by me: no browser walk run; unit tests not executed locally (CI green).
