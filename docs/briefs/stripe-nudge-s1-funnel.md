# Stripe for organisers v2, S1: funnel events (admin, invisible)
Repo: bachata-admin-11april. Branch: claude/stripe-nudge-s1-funnel. Lane: careful (DB). Builder: Opus.
Size: 3 files, 150 lines
Design registry row: STRIPE FOR ORGANISERS v2. Prototype: none (no visible change).

## Goal
Record 4 funnel events so the owner can see whether the Stripe nudge works, BEFORE any UI ships:
`shown` (a Stripe card/chip/hub was rendered), `tapped` (a Connect button pressed), `returned` (back from Stripe), `abandoned` (tapped, no connected state after 24h; computed in the read RPC, not stored).

## Build
- One migration: table `stripe_funnel_events(id, organiser_id, user_id, event text check in (shown,tapped,returned), surface text check in (home,tickets,hub,create_chip,price_sheet,ticket_sheet), created_at)`; RLS on, no direct grants; insert only through `record_stripe_funnel_event_v1(p_organiser_id uuid, p_event text, p_surface text)` SECURITY DEFINER, caller must be owner or manager of the organiser, dedupe `shown` to once per user+surface+day. No auto-grants (memory: supabase_no_auto_grants_new_public_tables): explicit GRANT EXECUTE to authenticated.
- Admin-only read RPC `admin_stripe_funnel_v1(p_days int)` returning counts per surface per event plus `abandoned`.
- Contract test in the admin repo for ACL (anon denied, non-member denied).
## Rules
No UI. No Website change. Migration PR order per dispatch-checklist (assign versions, prove green at head).
## Not in scope
Reminder / connected push messages (S3, deferred until data).
