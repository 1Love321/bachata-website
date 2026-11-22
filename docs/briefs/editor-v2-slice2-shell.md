# Brief: Editor v2 slice 2, shell, route, look (page frame, tabs, date chips, sheet frame, shared look)

Branch: claude/editor-v2-s1c
Size: 5 files, 380 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations; no admin change.

Prototype (WHOLE spec): C:/Users/ecual/.claude/plans/editor-v2/frozen/event-editor-c2-prototype.FROZEN-2026-11-21.html (62 KB: never read whole; grep the class or id, then Read with offset/limit). Checklist: frozen/checklist.md (ids below). Tap table: frozen/tap-table.md rows 1, 2, 3, 4, 33, 113. DESIGN-REGISTRY rows 61-81 win over this brief. Slice 1 handover (save engine you build on): docs/briefs/editor-v2-slice1-save-engine.handover.md.

## What main lacks
- No v2 page frame: today v2 is `EventPageV2.tsx` + `NightV2.tsx` with `SaveBar`; the prototype is ONE screen (`EditorV2`) with two tabs and date chips, every change saved at once.
- No bottom sheet frame, no shared look (tokens, raised buttons, pills, fields, switch, chips).

## Files (touch only these)
- src/modules/organiser/p3/v2/EditorV2.tsx (new): page frame
- src/modules/organiser/p3/v2/V2Sheet.tsx (new): bottom sheet frame
- src/modules/organiser/p3/v2/v2.css: shared look (tokens + classes below), scoped under `.ev2`
- src/modules/organiser/p3/shell/P3Routes.tsx: route wiring
- src/modules/organiser/p3/v2/__tests__/routes.flag.test.tsx: extend
Do NOT edit SaveBar.tsx, EventPageV2.tsx, NightV2.tsx, screens/*, R4*, or any file outside the list (slice 16 deletes the old ones). Content of the tabs is built by later slices: in this slice the "This night" and "Every <day>" panes render a placeholder `<div data-ev2-slot="night|every" />` that slices 3 and 6 fill.

## Build
1. `EditorV2.tsx`: takes the loaded workspace (same gate as `R4EditorGate`: `WorkspaceGate`, weekly and one-off only, festival/course fall back to today). Renders back link, tabs, date chips (weekly only), title, scope line, slot, `<V2Toast>` from slice 1. State: tab (`night`|`every`) and picked night. Picked night lives in the URL query `?night=<occurrenceId>` and tab in `?tab=every` (absent = night); picking a chip replaces the query without scrolling and keeps the tab. Coming nights only (first 6 as chips, then "More dates" chip calling `onMoreDates`, a no-op prop until slice 3). A one-off has no tabs and no chips: one page, title = long date.
2. `V2Sheet.tsx`: `<V2Sheet open title onClose>` portal to body, focus trap, Escape and scrim tap close, no save on close.
3. `v2.css`: tokens and classes of G-01..G-26 (listed below), all scoped under `.ev2` and `.ev2-sheet`, no global leaks, explicit px sizes (phone html font is 13.5px).
4. `P3Routes.tsx`: flag ON: `events/:seriesId` renders the gate with `EditorV2` (fallback `R4Night`); `events/:seriesId/edit` and `events/:seriesId/night/:occurrenceId` become redirects to `events/:seriesId` (night id into `?night=`). Flag OFF: unchanged (`R4Night`, `R4EditNight`), zero behaviour change.
5. Back link goes to the events list of today (`toEvent`/list route already used by P3Routes; reuse it).

## Prototype checklist
- [ ] G-01 Colour tokens: bg #FDFDFD, card #FFFFFF, line #E9E9E9, text #030303, muted #464646, muted2 #696969, accent #C2410C, accent dark #9A3412, accent soft #FFFAF7, accent edge #E2A988, danger #B42318, danger soft #FEF3F2, ok #097C4F, grey #EFEFEF, field border #BDBDBD
- [ ] G-02 Font Inter, base 16px, line height 1.4
- [ ] G-03 Screen padding 16px 0 24px on phone (24px 16px above 480px), 24px gap between blocks
- [ ] G-09 Sheet: slides from the bottom, max height 88vh, radius 16px 16px 0 0, padding 10px 16px 20px, 14px gap, scrim #0000004D
- [ ] G-10 Sheet grab bar 40x4px #D6D6D6 at the top
- [ ] G-11 Sheet head: title 18px bold left, round close button right (44px circle, #EFEFEF, "×" 22px, aria-label "Close")
- [ ] G-12 Scrim tap closes the sheet; Escape closes the sheet
- [ ] G-13 Raised button (two-line "fact"): full width, white, min height 66px, padding 10px 10px 10px 14px, border 1.5px #E2A988, bottom edge 4px #C2410C, radius 12px, shadow 0 2px 6px rgba(124,45,18,.16)
- [ ] G-14 Raised button pressed: down 2px, bottom edge 2px, no shadow
- [ ] G-15 Raised one-line button: same style, min height 52px, padding 8px 10px 8px 14px; label 16px bold #030303 left, value 15px #464646 right-aligned one line with ellipsis, chevron "›" #696969
- [ ] G-16 Buttons: min height 48px, radius 8px, weight 600. Primary #C2410C white text; secondary white border #CFCFCF; danger white with #B42318 text; danger solid #B42318 white text; big 56px high 17px text
- [ ] G-17 Fields: label 13px weight 600 #464646 above; input 16px, border 1px #BDBDBD, radius 8px, min height 48px, padding 8px 12px; textarea min height 110px
- [ ] G-18 Hint line 13px #696969; error line 14px #B42318 weight 600; warning box 14px #8A4B00 on #FFF6E0, radius 8px, padding 10px 12px
- [ ] G-19 Pills: 11px weight 600 capitals, letter spacing .06em, padding 4px 8px, radius 99px. Normal #FFFAF7 bg, #C2410C text, border #F5D5C3. Off #EFEFEF bg, #464646 text, border #DDD. Red #FEF3F2 bg, #B42318 text, border #F7C9C4
- [ ] G-20 Group heading 18px bold #030303 no capitals; small label 12px capitals #696969 weight 600 letter spacing .06em
- [ ] G-21 Text link button min height 44px, 15px #464646 underlined centred; inline link #C2410C weight 600 underlined
- [ ] G-22 Switch 48x28px, off #C9C9C9, on #097C4F, knob 22px; row min height 48px weight 600
- [ ] G-23 Radio row min height 48px, 16px text, 22px radio in #C2410C
- [ ] G-24 Choice chips in sheets: min height 44px, padding 0 14px, radius 99px, border #CFCFCF; picked #030303 bg white text
- [ ] G-25 Person chips: min height 40px, #EFEFEF bg, radius 99px, 15px, with a 36px "×" button
- [ ] G-26 Link box: dashed 1px #BDBDBD, radius 8px, padding 12px 14px, text selects all on tap
- [ ] G-27 The app's bottom bar stays as today (prototype bar is chrome; do not build it)
- [ ] G-28 Prototype-only controls NOT built: "Show a one-off event", the Stripe on/off toggle, the page heading and caption above the phone
- [ ] N-01 Back link "← Events": min height 44px, #464646, 15px; goes to the events list
- [ ] N-02 Two tabs in a grey box (#EFEFEF, padding 4px, radius 12px, 4px gap): "This night" | "Every <day>"; each 48px high, radius 9px, 15px weight 600 #464646; picked white with #C2410C text and shadow 0 1px 3px rgba(0,0,0,.16); tab list aria-label "What you are changing"
- [ ] N-03 Date chips row: scrolls sideways, 8px gap, 6 dates; chip min 64x56px, padding 6px 12px, border 1px #CFCFCF with 3px bottom, radius 10px; line 1 short weekday "Thu" 13px, line 2 "26 Nov" 15px bold
- [ ] N-04 Picked chip: border #C2410C, bg #FFFAF7, text #C2410C
- [ ] N-05 Chip tag "Cancelled" (11px weight 600 #B42318) on a cancelled night
- [ ] N-06 Chip tag "Changed" (11px weight 600 #696969) on a night with any own change (own photos alone give no tag)
- [ ] N-07 Last chip "More dates" opens the sheet "All dates" (slice 3 builds the sheet; here the chip calls `onMoreDates`)
- [ ] N-08 Title: the long date, e.g. "Thursday 26 November", 24px bold
- [ ] N-09 Scope line under the title: "Changes here are for this night only." 14px #464646
- [ ] N-40 Picking another date chip redraws the whole page for that night and keeps the tab
- [ ] E-01 Same back link and tabs; no date chips
- [ ] E-02 Title "Every <day>" 24px bold
- [ ] E-03 Scope line: "Changes here are for every <day>. A night you changed on its own keeps its change."
- [ ] tap rows: 1 ← Events | events list of today (prototype: stub toast; build the real list route) ; 2 This night | same screen, tab night ; 3 Every <day> | same screen, tab every ; 4 date chip | same screen redrawn for that night ; 33 × Close | sheet closes, nothing saved ; 113 scrim tap / Escape | sheet closes, nothing saved

## Tests (vitest + RTL, no network; extend routes.flag.test.tsx)
Flag off: `events/:id` renders `R4Night`, `/edit` unchanged. Flag on: `events/:id` renders `EditorV2` for a weekly event, fallback for festival; `/edit` and `/night/:occ` redirect to `events/:id` with `?night=`; tab and night in query survive a chip pick; one-off has no tabs/chips; tab labels use the series weekday; sheet closes on ×, scrim, Escape and saves nothing. Assert key computed styles that jsdom can see (class present) and that every v2.css rule starts with `.ev2` or `.ev2-sheet`.

## Rules
Never merge. Bot-authored push to claude/editor-v2-s1c (existing PR 1865; do not open another). Push once when coherent; PARTIAL marker if context tight. `npm run check:bundle-budget` must not grow /app first load (v2 stays lazy). Handover ends with a TAP TABLE: button | destination | prototype/OLD for rows 1, 2, 3, 4, 33, 113 (one OLD row = keep building). A prototype element you cannot build: STOP and write it at the top of the handover; no "remaining differences" list.
