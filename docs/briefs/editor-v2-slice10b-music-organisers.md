# Brief: Editor v2 slice 10b, Music sheet + Organisers sheet on the Every tab

Branch: claude/editor-v2-s1c
Size: 5 files, 330 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.

The prototype file is NOT reachable from the cloud. Everything needed is embedded below; it is the spec, copy words exactly. Slices 1-10a are on the branch: read `docs/briefs/editor-v2-slice10a-every-card.handover.md` first (`EveryBody.tsx` `Line`, `send`, `NameSheet`, `V2Sheet`, `V2.every`). DESIGN-REGISTRY rows 61-81 win over this brief.

## Scope
Wire the "Music" and "Organisers" rows in `EveryBody.tsx` (rendered today with no `onOpen`) to two new sheets. Repeats / When / Price taps stay pending (slice 11 / later): do not touch them.

## Files (touch only these)
- src/modules/organiser/p3/v2/MusicSheet.tsx (new)
- src/modules/organiser/p3/v2/OrgSheet.tsx (new)
- src/modules/organiser/p3/v2/EveryBody.tsx: add `onOpen` for rows `music` and `organisers`, mount the two sheets, add `saveMusic` / `saveOrgs` next to `saveName`. Nothing else.
- src/modules/organiser/p3/v2/copy.ts: add the lines below.
- src/modules/organiser/p3/v2/__tests__/everyMusicOrg.test.tsx (new)
Do NOT edit editorModel.ts, NightCard, SaveBar, saveNow.ts, PeopleGroup.tsx, anything in `shared/`, `dates/`, `events/`. Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only, explicit px for sizes.

## Music (SH-MUSIC-01..03, taps 25, 76, 77)
- Title "Music". Chips in this order: Bachata, Sensual, Dominican, Salsa, Kizomba. Tap toggles; picked = black (reuse the `.tchip` / `.on` classes from v2.css if present, else the chip style the session sheet uses; `aria-pressed`). Picked set starts from `ws.musicStyles`.
- Button "Done": unchanged set -> closes, no save. Else ONE step `{ part: 'Music', target:'series', id: s.id, version: s.version, command: upsertCommand({ name: s.name ?? '', default_music_styles: picked }), order: 1 }` through the existing `send` (before = `{ default_music_styles: ws.musicStyles }`), toast = the same `savedLabel('usual', day)` the other Every sheets use, with Undo. Stored values that are not one of the five chips are kept (keep them in the written list, order = chip order then the extras).
- Row value stays `ws.musicStyles.join(', ')` or "None yet".

## Organisers (SH-ORG-01..03, taps 28, 45, 46, 81)
Prototype:
```
function orgSheet(){sheet('Organisers',people('organisers','Who runs it',D.list)+B('btn pri','orgDone','Done'))}
people(): label "Who runs it", chips with "x" (aria "Remove <name>"), empty "None yet", search field + Add button
orgDone: commit + toast "Saved for every <day>" with Undo
```
- Title "Organisers". One group "Who runs it": chips (name + "&times;" button, aria-label `V2.session.removeName(name)`), empty "None yet", then add control + "Add" button, then "Done".
- Reuse `PeopleGroup` for the chips, hint, "None yet" and remove, BUT organiser pages are not people: pass `role={null}` (read-only picker off) and render the add control yourself in OrgSheet: a `<select>` (aria-label "Organiser page") listing `addablePages(myPages, ids)` from `events/organisersModel.ts` (`myPages` from `useSeriesRole(seriesId).myPages` in `events/programmeSeriesApi.ts`, mapped to `MyPage`; if the hook's page type differs, adapt, do not edit it) plus an "Add" button (tap 45) that appends the chosen page id (`addOrganiser`). Empty addable list: show the hint `ADD_NONE_LEFT`. This is the real rule (AW1: you may only link pages you run) and deliberately replaces the prototype's free-text add; say so in the handover.
- Remove (tap 46, the chip "x"): `removeOrganiser(ids, id)`; when `removeOff(ids)` the last chip has NO "x" (`canRemove: false`) and the hint `LAST_ORGANISER_REASON` shows.
- Read-only: when `organisersOff({ lifecycle: ws.lifecycle ?? '', canEdit: role is owner or manager })` is non-null show the chips without x, no add control, the reason as hint, and "Done" just closes. (`ws.lifecycle`: grep how EveryBody / EventWorkspace names the lifecycle; if missing use `s.lifecycle_status`.)
- "Done": `organisersCommand(ws.organiserIds, ids)`; null -> closes, no save. Else one step `{ part:'Organisers', target:'series', id: s.id, version: s.version, command, order:1 }` via `saveNow(steps, [{ organiser_ids: ws.organiserIds }], label)` (inverseOf already handles `series.set_organisers`). Same toast label as Music, Undo.
- Names for chips: `fetchOrganiserPages(ids)` already used for the row (`pages` in EveryBody); a newly added page name comes from the addable list. Fall back to "Organiser".

## Copy to add (V2.every or V2.org)
title "Music", chips list, "Done"; title "Organisers", group "Who runs it", "Add", `organisersPageLabel` "Organiser page", "None yet" (reuse `E.noneYet`). Reuse existing keys where they exist.

## Checklist (every item built; tick in handover)
- [ ] SH-MUSIC-01 title; -02 five chips, toggle, black when picked; -03 Done + toast + Undo
- [ ] SH-ORG-01 title; -02 group "Who runs it": chips with x, "None yet", picker + Add; -03 Done + toast + Undo
- [ ] Row "Music" and row "Organisers" open the sheets; Repeats / When / Price taps unchanged (pending)

## Tap table (put in `docs/briefs/editor-v2-slice10b-music-organisers.handover.md`; columns button | destination | prototype/OLD; any OLD = not done)
| 25 | Music row | Every About group | Sheet "Music" |
| 28 | Organisers row | Every About group | Sheet "Organisers" |
| 45 | Add | sheet Organisers | Same sheet; picked page added as a chip |
| 46 | x (Remove <name>) | organiser chip | Same sheet; chip gone |
| 76 | Bachata/Sensual/Dominican/Salsa/Kizomba | sheet Music | Same sheet; chip on/off |
| 77 | Done | sheet Music | closes; toast "Saved for every <day>" + Undo |
| 81 | Done | sheet Organisers | closes; toast "Saved for every <day>" + Undo |
Tests: render EveryBody with a mocked workspace and `saveNow`; open each sheet; assert the exact series step (kind, keys, `name` present for upsert, `organiser_ids` complete ordered list for set_organisers), `before` for Undo, the toast label, the no-change close with no save, last organiser has no x, read-only mode.

## Handover
Write the handover file named above: built, filled tap table, deviations (Add = own pages select), not-buildable (exact), files + line counts, tests run (`npx vitest run src/modules/organiser/p3/v2`, tsc `-p tsconfig.app.json`, eslint on your files; pre-existing errors in eventPage.proto.test.ts tsc and V2Sheet.tsx eslint are not yours). Push to `claude/editor-v2-s1c` per the dispatch rules; open no PR.
