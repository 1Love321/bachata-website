# Brief: Editor v2 slice 10a, the "Every <day>" tab card + Name sheet, usual writes for flyer / where / description

Branch: claude/editor-v2-s1c
Size: 5 files, 390 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.

The prototype file is NOT reachable from the cloud. Everything needed from it is embedded below; it is the spec, copy words exactly. Slices 1-9 are on the branch: read `docs/briefs/editor-v2-slice9-flyer-photos.handover.md` and `docs/briefs/editor-v2-slice8-where-about.handover.md` first (`V2Sheet`, `V2` copy, `SheetMode`, `savedLabel`, `OnSave`, `Fact`, how NightCard mounts WhereSheet / AboutSheet / FlyerSheet). DESIGN-REGISTRY rows 61-81 win over this brief.

## Scope
Fill the EMPTY Every slot in `EditorV2.tsx` (`data-ev2-slot={every ? 'every' : 'night'}`, `every` branch renders nothing today) with `EveryBody`, and add `NameSheet`. Music, Organisers and Repeats rows are RENDERED here (values shown) but their sheets are slice 10b (Music, Organisers) and slice 11 (Repeats): the tap does nothing yet, log it as intentionally pending (flag OFF). Same for the "When" fact (sheet "Classes and party" on Every = slice 11) and the Price fact (Tickets screen = later slice): rendered, tap pending. The "Pause, end or cancel" group is a later slice: do NOT render it.

## Files (touch only these)
- src/modules/organiser/p3/v2/EveryBody.tsx (new): the card + "About this event" group, mounts the usual-mode sheets.
- src/modules/organiser/p3/v2/NameSheet.tsx (new)
- src/modules/organiser/p3/v2/EditorV2.tsx: render `<EveryBody .../>` in the every branch (pass `ws`, `day`, `saveNow`, `busy`), nothing else.
- src/modules/organiser/p3/v2/copy.ts: add the lines below.
- src/modules/organiser/p3/v2/__tests__/every.test.tsx (new)
Do NOT edit NightCard, NightV2, NightHub, SaveBar, saveNow.ts, editorModel.ts, anything in `shared/`, `dates/`, `events/`. Max 15 lines to `v2/v2.css` only if unavoidable (6th file, tell me). Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only (`&rsaquo;` `&hellip;`), explicit px for sizes.

## Data and writes (reuse)
- Every writes target the SERIES: step `{ part, target:'series', id: ws.series.id, version: ws.series.version, command: upsertCommand({ name: ws.series.name, <keys> }), order: 1 }` via the same `saveNow(steps, before, label)` shape EditorV2 already uses (`OnSave`). `series.upsert` ALWAYS needs `name`. `upsertCommand` in `shared/seriesCommands.ts` strips what it must; keys: `default_venue_id` (+ `default_city_id` when known, see `venueCommand` in `events/eventModel.ts`, venue city from the picked venue row), `default_description`, `default_cover_image_url`, `default_gallery`, `name`.
- Undo `before` = the same keys with the old values from `ws.series`. If an `inverseOf` case is missing for a series upsert, pass the exact `before` object like NightCard `saveVenue` does; only if that cannot work STOP and say so in the handover (do not touch editorModel).
- Toast label per mode: `savedLabel('usual', dateWords)` (= "Saved for every <day>"), `dateWords` = a picked-night full date string whose first word is the weekday (see how NightCard builds it); a one-off never shows this tab (weekly only), so `oneOff` mode is not needed here.
- Mount in `usual` mode: `<WhereSheet mode="usual" current={ws.series.default_venue_id} .../>`, `<AboutSheet mode="usual" current={ws.series.default_description ?? ''} usual={...} .../>`, `<FlyerSheet mode="usual" flyer={ws.series.default_cover_image_url} gallery={ws.gallery} seriesId={ws.series.id} dayName={day} onFlyer onGallery .../>`. `changed` is always false and no Back link on this tab (E-10).
- Name: `NameSheet` field "Event name" (prefilled), hint "The name is the same for every <day>." (weekly), empty -> error "Add a name" and the sheet stays open; unchanged -> closes with no save; else one `series.upsert {name}` and toast `savedLabel('usual', ...)` with Undo (before `{name: old}`).
- Row values (read from `ws`): name = `ws.series.name`; description = `aboutShort(ws.series.default_description)` (Facts.tsx); music = `ws.musicStyles.join(', ')` or "None yet"; repeats = "Every <day>, no end date" or "Every <day>, ends after <long date>" from `ws.endedOn`/series end date field (grep how the old Repeats row computed it; if no date field is readable say so in the handover); organisers = names or "None yet" (`ws.organiserIds` are ids: look the names up from the organiser home/team data already loaded for the editor, grep `organiserIds` and `teamOf`; if no loaded name source exists show "None yet" only when ids is empty and the count like "2 organisers" otherwise, and REPORT it as a gap).
- Status pill (E-06) by lifecycle: reuse the pill NightCard renders (`pill` there; Live / Paused / Ended / Cancelled). Usual flyer block: same markup/classes as NightCard's `.flyer` with the pill button "Flyer and photos" (data-testid `fact-flyer`), no "Only this night" suffix.

## Prototype (verbatim, bodyEvery / aboutRows / flyerBlock)
```
function bodyEvery(){
  var sp=span(S.sessions),pl=priceLabel();
  var h='<div class="grp"><div class="lab">What dancers see every '+dayName()+'. Tap to change.</div><div class="card">'
    +flyerBlock(S.flyer,'')
    +'<div class="fb-list"><div class="trow"><b>'+esc(S.name)+'</b>'+statusPill()+'</div>'
    +fact('programme','When','Every '+dayName(),sp.start+' to '+sp.end)
    +fact('where','Where',esc(S.venue.name),esc(S.venue.area))
    +fact('goTickets','Price',esc(pl.m),esc(pl.s))
    +'</div></div></div>';
  h+='<div class="grp"><div class="lab hd">About this event</div><div class="card">'+aboutRows()+'</div></div>';
}
function flyerBlock(fl,tag){return '<div class="flyer '+(fl?(fl==='Flyer'?'f1':'f2'):'none')+'"><span>'+(fl?esc(fl):'No flyer')+tag+'</span>'+B('fpill','flyer','Flyer and photos')+'</div>'}
function aboutRows(){
  return '<div class="fb-list">'+factLn('name','Name',esc(S.name))+(S.oneOff?'':factLn('about','Description',aboutShort(S.about)))
    +factLn('music','Music',S.music.length?esc(S.music.join(', ')):'None yet')
    +(S.oneOff?factLn('goTickets','Tickets','Prices and how dancers pay'):factLn('repeat','Repeats','Every '+dayName()+(S.endDate?', ends after '+longDate(S.endDate):', no end date')))
    +factLn('organisers','Organisers',S.organisers.length?esc(S.organisers.join(', ')):'None yet')+'</div>';
}
name sheet: sheet('Name', fld('f-name','Event name',S.name) + hint 'The name is the same for every <day>.' + '<p class="err" id="f-err" hidden>Add a name</p>' + Done(primary))
nameDone: empty -> show error, stay open; same as before -> closeSheet() with no save; else commit + toast "Saved for every <day>" with Undo
```
The "One-line button" rows (`factLn`) are one raised row: label left, value (bold, one line, ellipsis), "&rsaquo;" right. Reuse an existing `.row` / one-line style from v2.css if there is one (WhereSheet uses `.row`); the scope line and title are already rendered by EditorV2.

## Checklist (every item is built; checkbox in handover)
- [ ] E-02/E-03 title and scope: already in EditorV2, keep
- [ ] E-04 Label "What dancers see every <day>. Tap to change."
- [ ] E-05 Card with usual flyer block + pill "Flyer and photos" opening FlyerSheet (usual)
- [ ] E-06 Event name + status pill
- [ ] E-07 Fact "When": bold "Every <day>", second line usual start to end; tap PENDING slice 11
- [ ] E-08 Fact "Where": usual venue + area; opens WhereSheet (usual)
- [ ] E-09 Fact "Price": same value rules as NightCard `priceLabel`; tap PENDING (Tickets screen)
- [ ] E-10 No "Only this night" tags, no "Back to usual" buttons on this tab
- [ ] E-11 No going card, tiles, "This night" group, Share, Cancel this night on this tab
- [ ] E-12 Group heading "About this event"
- [ ] E-13 Row "Name" opens NameSheet
- [ ] E-14 Row "Description" (first 60 chars + ellipsis or "No description") opens AboutSheet (usual)
- [ ] E-15 Row "Music" (styles or "None yet"); tap PENDING 10b
- [ ] E-16 Row "Repeats"; tap PENDING slice 11
- [ ] E-17 Row "Organisers" (names or "None yet"); tap PENDING 10b
- [ ] SH-NAME-01..05 as above (title "Name", field "Event name", hint, error "Add a name", Done + toast with Undo)

## Tap table (fill in `docs/briefs/editor-v2-slice10a-every-card.handover.md`; columns: button | destination | prototype/OLD; any OLD = not done; PENDING rows are listed separately with their slice)
| 7 | Flyer and photos pill | Every card | Sheet "Flyer and photos" (usual gallery) |
| 9 | Where Change > | Every card | Sheet "Where" |
| 23 | Name | About this event | Sheet "Name" |
| 24 | Description | About this event | Sheet "Description" (label "What dancers read") |
| 35 | Done | sheet Name | Empty: error "Add a name". Else closes; toast "Saved for every <day>" + Undo |
| 57-64 | flyer sheet buttons | Every flyer sheet | as slice 9, usual writes (choose flyer / add photos / remove / tile / photo sheet) |
| 25,28,26 | Music / Organisers / Repeats | About this event | PENDING 10b / 10b / 11 |
Tests render EveryBody with a mocked workspace, `uploadEventPicture` and `saveNow`; open each sheet, assert the exact series step (target 'series', keys, `name` present), the toast label, `before` for Undo, the E-10/E-11 absences, and "None yet"/"No description" fallbacks.

## Handover
Write `docs/briefs/editor-v2-slice10a-every-card.handover.md`: built, filled tap table, PENDING list, not-buildable (exact), files and line counts, tests run (`npx vitest run src/modules/organiser/p3/v2`, tsc `-p tsconfig.app.json`, eslint on your files; pre-existing errors in eventPage.proto.test.ts tsc and V2Sheet.tsx eslint are not yours). Push to `claude/editor-v2-s1c` as the dispatch rules say; open no PR.
