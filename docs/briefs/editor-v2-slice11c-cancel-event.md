# Brief: Editor v2 slice 11c, wire "Cancel the event" (weekly) on the Every tab

Branch: claude/editor-v2-s1c
Size: 3 files, 150 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.

Slice 11b is on the branch: read `docs/briefs/editor-v2-slice11b-status.handover.md` first. `CancelEventSheetV2` (StatusSheets.tsx) and the red "Cancel the event" row (EndCard.tsx `onCancel`) are built and tested but NOT mounted: no `series.*` command cancels a whole weekly event. Decision (owner's window, technical): a cancelled weekly event = every coming, not-yet-cancelled night gets one `occurrence.cancel` step (`cancelCommand(reason)` in shared/seriesCommands.ts), all in ONE `saveNow([...steps], [], 'Event cancelled', { noUndo: true })`, exactly how EditorV2.tsx `cancelNight` builds one night step via `nightStep`. Do not add a series command, do not use bulk_cancel.

## Files (touch only these)
- src/modules/organiser/p3/v2/EveryBody.tsx: pass `onCancel` to EndCard, mount `CancelEventSheetV2`, build the steps (one per coming night that is not already cancelled and not past; read how `coming` is derived in EditorV2.tsx / `dates/`), `soldCount` from the same source as the night cancel sheet (0 when unknown), show `error` on failure.
- src/modules/organiser/p3/v2/__tests__/everyStatus.test.tsx: add tests.
- src/modules/organiser/p3/v2/copy.ts only if a line is missing (toast "Event cancelled").
Do NOT edit editorModel.ts, NightCard, SaveBar, saveNow.ts, shared/, dates/, events/. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only.

## Prototype (copy exactly)
```
cancelEvent sheet 'Cancel the event':
  'Every upcoming date shows Cancelled and your reason to dancers.'
  tickets sold: warn box '<n> ticket(s) sold. We will email buyers. Refunds are sorted separately.'
  radios 'Venue not available' (checked) | 'No reason' ; hint 'This takes effect straight away.'
  danger solid 'Cancel the event' -> cancelEventDo ; secondary 'Keep the event' -> close
cancelEventDo: every coming night Cancelled with the reason ('' for No reason); toast 'Event cancelled' (NO Undo)
After: every date chip tagged Cancelled (CANCELLED_WORD, eventState.ts), pill "Cancelled" when every coming date is cancelled (use the existing derivation, do not invent a flag), the red row becomes static "Event cancelled" + "Reason: <reason>" (EndCard `status`/`reason` props; derive from the refetched workspace: all coming nights cancelled; reason = their shared cancel reason), row "Make it live again" shown.
"Make it live again" when derived Cancelled: send `uncancelCommand()` for every coming cancelled night (one step each, one saveNow, toast "Event is live again" + Undo if inverseOf supports it, else no Undo). Do NOT send series.set_lifecycle to 'live' for this case.
```

## Checklist
- [ ] SH-CANCELE-01..07 mounted and reachable from the red row
- [ ] static red "Event cancelled" / "Reason: <reason>" row after refetch; "Make it live again" restores every night
- [ ] failure path shows the error text in the sheet, sheet stays open

## Tap table (put in `docs/briefs/editor-v2-slice11c-cancel-event.handover.md`; columns button | destination | prototype/OLD; any OLD = not done)
| 32 | Cancel the event | card (weekly) | Sheet "Cancel the event" |
| 85 | Cancel the event | sheet | closes; chips Cancelled; toast "Event cancelled" (no Undo) |
| 86 | Keep the event | sheet | closes, nothing sent |
| 29b | Make it live again (cancelled) | card | uncancel every coming night; toast "Event is live again" |
Tests: sheet opens from row; exact steps (count, kind `occurrence.cancel`, payload `{cancelled:true, reason}`), already-cancelled and past nights skipped; "No reason" sends reason ''; Keep sends nothing; no Undo on cancel; uncancel steps; static row after cancel.

## Handover
Write the handover file, commit, push to claude/editor-v2-s1c. No PR. List what you verified and did not (390x844 walk, pre-ship).
