# Brief: Editor v2 slice 17i, remove the flyer for one night (blank night flyer)

Branch: claude/editor-v2-s1c
Size: 4 files, 150 lines
Repo: 123constante/bachata-website. Flag `organiserEditorV2` stays OFF; zero migrations here; NO admin change (the server already stores a blank night flyer, see Ground truth).
Design: owner-picked FROZEN prototype (DESIGN-REGISTRY rows 61-81), `~/.claude/plans/editor-v2/frozen/event-editor-c2-prototype.FROZEN-2026-11-21.html` (big file: grep `flyerSet`, `usual`, then Read with offset/limit), `checklist.md` SH-FLY-02..05, N-11, N-12, `tap-table.md` rows 57-59 (row 58 "Remove the flyer"). Prototype wins. Read `docs/briefs/editor-v2-slice17f-undo-gaps.md` and `docs/briefs/editor-v2-slice17e-money-gaps.handover.md` on this branch first (UNBUILT lists). Owner approved building this (item 9).

## Files (touch only these; max 5)
- src/modules/organiser/p3/v2/NightCard.tsx (`saveFlyer` ~line 122)
- src/modules/organiser/p3/v2/FlyerSheet.tsx (Remove button ~line 91)
- src/modules/organiser/p3/v2/__tests__/flyer.test.tsx (the line ~49 `flyer-remove` null assertion on a night is replaced: it now asserts the opposite)
- src/modules/organiser/p3/v2/__tests__/slice17i.test.tsx (new)
Files over 2 KB: `scripts/safe-edit.py` / `scripts/safe-write.py`. JSX entities only. A 5th file is allowed only if needed; a 6th = STOP and say so in the handover.

## Ground truth (read, do not guess)
- Server: `_cmd_occurrence_set_override_p5` stores a sent `cover_image_url` VERBATIM; `""` is an OWNED value (ownership is `IS NOT NULL`), so a night with `cover_image_url: ""` shows NO flyer while the series flyer stays for other nights. `null` = inherit the series flyer (what "Back to the usual flyer" sends today). Undo of a removal = the same command with the prior value (the prior string, or `null` to inherit). No admin change; command is `occurrence.set_override` (already allowed for owners).
- Website readers: `str()` in `shared/seriesModel.ts` keeps `""` (a string), so `dt.coverImageUrl` / `dt.coverImageOverride` arrive as `""` for a blank night flyer. `NightCard.tsx:88` is `dt?.coverImageUrl ?? s.default_cover_image_url`: `??` keeps `""`, so the card shows the grey "No flyer" block (correct; do not change it to `||`). `ownedKeys(date, 'flyer')` reads `override_keys` (not truthiness), so "Only this night" and "Back to usual" show for a blank flyer (correct, N-12).

## What to build (decided; do not ask)
1. `NightCard.tsx saveFlyer(url: string | null, label)`: today it returns when `!url`. Change: `null` means REMOVE for the night and sends `cover_image_url: ''`; a url string sends the url as now. Before-state for Undo stays `[{ cover_image_url: d.coverImageOverride }]` (null = inherit, string, or `''`), so Undo of a removal puts back the prior night flyer or inherits. Keep the existing savedLabel/toast and Undo; toast "Saved for <date> only".
2. `FlyerSheet.tsx`: the danger button `flyer-remove` ("Remove the flyer") shows in night mode too, when a flyer is displayed (`flyer` truthy). Everything else in the sheet stays. In night mode the button calls `onFlyer(null, label)`.
3. After removal the card shows "No flyer" + the tag "only this night" and the sheet's "Back to the usual flyer" link appears (`changed`), tapping it sends `cover_image_url: null` (existing `backToUsualStep`, no change needed; add a test that proves it).
4. Every Website reader of the night cover that falls back on a truthy check: do NOT change any file outside the 4 above. LIST in the handover (file:line, one line each) the readers that would show the series flyer for a blank night flyer. Known candidates to check and list: `src/modules/organiser/p3/data/dateFacts.ts:16`, `shared/../flows/change/changeModel.ts:289`, `p3/data/dates.ts:220`, `p3/screens/DateSheet.tsx:350` (all `!!`/truthy of `coverImageOverride`: old P3 screens, flag-OFF path), and the public side `src/modules/event-page/useEventPageQuery.ts:190` (`asString(cover) ?? asString(hero_image_url)`: `asString("")` is null, so a blank night cover falls back to `hero_image_url`; read what `hero_image_url` holds for an occurrence page and say in the handover whether the public night page would still show the series flyer. That is a follow-up for the owner, NOT built here).

## Checklist (each item is a test in slice17i.test.tsx that FAILS on the 17h code)
- [ ] night sheet with a flyer shows `flyer-remove` ("Remove the flyer"); with no flyer it does not
- [ ] tap Remove: `onSave` called once with step `{kind:'occurrence.set_override', payload:{cover_image_url:''}}`, target occurrence, the night id, before `[{cover_image_url: <prior override>}]`
- [ ] the label is the one-night label ("Saved for <date> only"); Undo (via `undoPlan(steps, before)`) gives `{cover_image_url: <prior>}` and `null` prior gives `null`
- [ ] a night that already had its own flyer: Undo restores that exact url (not null)
- [ ] choose-a-new-flyer still sends the url (unchanged)
- [ ] night card with `coverImageUrl: ''` and a series flyer shows "No flyer" (not the series flyer) and the tag text " · only this night" (N-12)
- [ ] "Back to the usual flyer" on that night sends `{cover_image_url:null}`
- [ ] one-off and Every modes: Remove behaves as before (series upsert null), flyer.test.tsx other cases green
- [ ] Existing v2 tests all green (flyer.test.tsx line ~49 updated, not deleted)

## Handover
`docs/briefs/editor-v2-slice17i-night-no-flyer.handover.md`. Tap table `button | destination | prototype/OLD`; any OLD row = not done, no "remaining differences" list. Rows at least: Remove the flyer (night), Choose a new flyer (night), Back to the usual flyer, Undo on the toast. Sections: Readers list (item 4), UNBUILT, Verified / Not verified (say plainly: no prod write was made; the server behaviour is read from the live function source). Run `npm run test:unit -- src/modules/organiser/p3/v2` and `npx tsc --noEmit -p .` and `npm run check:mojibake`. No secrets. Commit, push to claude/editor-v2-s1c. No PR.
