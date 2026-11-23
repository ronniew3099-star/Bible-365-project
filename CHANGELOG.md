# Bible Challenge — Code Fixes Changelog

Fixed copy of `index.html` (from the repo clone at `~/workspace/code-review/bible-365-project/index.html`, 1,033 lines) plus the new files the install fix needs. The clone was not modified. The Reading Shelf was not touched in this pass.

Base: repo file as cloned Nov 21, 2026. Fixed file: `index.html` (1,313 lines). Diff vs clone: 366 lines added, 86 removed.

Verified by: re-reading every changed function, `node --check` on the extracted inline script (syntax OK) and on `sw.js` (syntax OK), `manifest.json` parses, icons are valid PNGs, and a Python simulation of the reconcile rules against Ronnie's real backup (see fix 4). The app was **not run** — see "Could not verify" at the bottom.

---

## Phase 3 follow-up — Awards phone layout (Nov 23, 2026)

Ronnie checked the live app on his phone and reported the Awards page was cramped and a problem with the Refresh button. Fixes in this push:
- Removed the duplicate "Awards" `<h2>` in the Awards view (the topbar already says "Your awards." and the panel says "Your awards").
- Award cards are now 1-column at ≤700px (was 2-column until 380px, so a ~390px phone kept the narrow 2-column cards and titles like "30-day streak" broke across three lines). Now each card is full width on phones.
- `.award-state` ("Earned"/"Locked") now has `white-space:nowrap` so it does not wrap or squeeze.
- Calendar legend spacing: added space between the two legend items ("Reading completed" and "At least one IREAP completed") which ran together.
- The Refresh button issue is still to be clarified with Ronnie (what exactly he saw); the button itself was not changed in this push.

## Phase 3 follow-up — Dashboard overview and IREAP on phones (Nov 23, 2026)

Ronnie reported the Home "Today's reading overview" and "IREAP reflections" sections break on phones when there are many chapters to catch up (14 carry-over chapters). Root cause: the base `.stats.three` rule (3 columns) sits after the ≤980px media query (2 columns) in the stylesheet, so with equal specificity the later 3-column rule wins at every width — the overview stayed 3-column on phones and the narrow cards made labels wrap ("CARRY-" / "OVER" / "CHAPTERS", "OUTSTANDING" cut off) and chapter names fragment ("Colossians" / "3").
- Added a `@media(max-width:700px)` rule AFTER the base `.stats.three` rule so it wins the cascade: `.stats.three` is now 1-column (vertical stack) on phones. Each of the three overview cards and each of the three IREAP cards is full width, so labels and chapter lists read naturally.
- The chapter lists in the overview (`#dashPlannedNames`, `#dashCarryNames`) are now capped at 132px tall with vertical scrolling on phones, so a long carry-over list does not make the card enormously tall.

---

## Phase 3 — Bible Quota redesign (Nov 23, 2026)

Working copy: `~/workspace/code-fixes/bible-365-project-audit/index.html`, base repo HEAD `6c9721af` (135,052 bytes, 1,371 lines, including Ronnie's own Nov 22 evening wording edits). After this pass: 1,671 lines, 153,953 bytes. The stale `~/workspace/code-fixes/bible-365-project/index.html` (135,262 bytes) was NOT touched. NOT pushed to GitHub, NOT deployed.

### Re-theme: purple + gold (distinct from The Reading Shelf)
- `:root`: `--green` #315c48 → #4c1d95 (deep purple), `--green2` → #e9e2f7 (pale lavender), `--gold` stays #d9ae62, `--goldbg` → #efe9f7, `--bg` → #f4f1fa, `--ink` → #251f2e, `--line` → #e3ddf0.
- `.hero`: purple gradient (#4c1d95 → #6d28d9 → #7c3aed) + inline SVG burning-bush motif (gold bush, pale flame). `.brand-mark`: purple with a gold flame SVG.
- Heatmap greens → purples (#e9e2f7 / #c9b8ec / #9d7fd6 / #4c1d95). Ghost buttons now lavender. Finish box lavender.
- `manifest.json` (new in this folder): name "Bible Quota", short_name "Bible Quota", theme_color #4c1d95, background_color #f4f1fa. This folder has no `sw.js` or icons; the repo's `sw.js` (cache `ronnies-bible-challenge-v1`) and gold-tree icons are reused unchanged.

### Rename
- Brand: "Bible Quota" large, "by Njenga wa Wainaina" smaller. `<title>`, meta description, backup filename (`bible-quota-backup.json`), backup `app` field, reminder notification title, and `showView` fallback all say "Bible Quota". Repo name and Worker URL unchanged.

### Responsive fixes (audit H1/H2/H3, M1/M2/M3/M5, lows)
- H1: `.hero` is single-column at ≤700px (button and ring stack instead of colliding).
- H2: month + year `<select>`s and a "Today" button on the Reading calendar and on the new Home calendar; prev/next arrows have aria-labels ("Previous month"/"Next month"). Wired to `shiftMonth`/render logic via `initCalendarControls()`; year range 2025–2029.
- H3: inline `grid-template-columns:repeat(3,minmax(0,1fr))` removed from the "Today's reading overview" sub-grids; they now use `.stats.three` (3 cols desktop, 2 cols ≤980px), so the media query applies.
- M1: `--muted` #748078 → #5a665e (4.5:1+ on both backgrounds); verse citation #90774a → #6d5a33; done-chapter #8b958d → `var(--muted)`.
- M2: `showView()` moves focus to the view `h1` (`tabindex="-1"` + `.focus({preventScroll:true})`); `#pageTitle` has `aria-live="polite"`.
- M3: toast div has `role="status"` and `aria-live="polite"`.
- M5: calendar IREAP dot is no longer colour-only — see "Accurate calendar" below.
- Lows: `body{overflow-x:clip}`; `.book-grid` 1 column below 380px; toast moved above the mobile nav (`bottom:70px` at ≤700px); `:focus-visible` outline rule (gold on purple surfaces); mobile nav now 7 buttons (Awards added).

### Accurate calendar
- New `calendarDayButton()`: each day cell shows the date number, chapters actually read that day from `state.logs[date].chapters` (never from the plan/schedule), and the IREAP count as text ("2 ch · IREAP 2/2"). The day button's `aria-label` reads the full info ("November 23, 2026, 2 chapters read, IREAP done for 2 of 2."). Dots kept as decoration (`aria-hidden`).
- Day detail panel: each logged chapter now shows "IREAP done" or "IREAP not done · Carries forward" in words.
- New Home/Dashboard calendar panel (`renderDashCalendar()`): same month/year dropdowns + Today button; tapping a day opens the Reading calendar on that date.

### Pagination
- Books: 66 books paginated, 10 per page, Previous / "Page X of 7" / Next (`buildPager()` shared helper). Sort change resets to page 1.
- Today: each subsection (carry over / planned / completed) paginates at 5 items with the same pager.
- IREAP Tracker: unchanged.

### Awards (new section, 23 awards)
- New "Awards" view + sidebar/mobile nav entries; dashboard Achievements panel now previews the first 6 awards with a "View all awards →" button. Count shown as "X of 23 earned" in both places.
- Computed from state only (`computeAwards()`), no new cloud field. A book is finished when all its chapters are in `completed`.
- Streaks: 3-day 🔥, 7-day 📅, 30-day 👑 (longest streak — once earned, stays earned). Chapters: 10 🌱, 50 📖, 100 🔖, 500 🏆, Whole Bible 1,189 ✝️. Books: first ✅, 5 📚, 10 🏛️. IREAP: first 💡, 10 🔍, 50 🙏. Consistency: a full Mon–Sun week ☀️, a full calendar month 🌙. Covenant Keeper 📜 (OT, 39 books), New Covenant 🕊️ (NT, 27), The Law (tablets SVG, Torah), Voice of the Prophets 🎺 (Nevi'im), Wisdom and Worship 🎵 (Ketuvim), Wisdom of Solomon 🦉 (Proverbs, Ecclesiastes, Song of Solomon), The Fourfold Gospel ⭐ (Matthew, Mark, Luke, John).
- Streak helpers: `currentStreak()` (dashboard stat), `longestStreak()`, `calendarWeekComplete()`, `calendarMonthComplete()`. Group awards respect excluded books (a group is finished when its non-excluded books are finished).

### Reading order editing ("Ronnie's Reading Schedule")
- Books section labelled "Ronnie's Reading Schedule" (`state.scheduleName`, default "Ronnie's Reading Schedule", saved in state/cloud/backup/merge).
- Per book: "↑ Up" / "↓ Down" (`moveBook()`; reordering switches sort to plan), "Remove" (`removeBook()`; in-app confirm; soft-remove into `state.excludedBooks`), and "Add this book back" (`restoreBook()`) for removed books. The "Read this book next" dropdown lists only non-excluded books.
- Excluded books' chapters are skipped by plan logic (`getReadingSequence()`, `firstUnreadInOrder()`, `projectedFinish()` via `remainingChapters()`, dashboard counts via `activeCompletedCount()`/`planTotal()`), but completed chapters stay in `completed`/`logs` and the books still show their progress. `switchToBook()` now preserves the current reading order (including rearrangements) instead of resetting to the default order, and refuses an excluded book with a toast. `resumeOriginalPlan()` still restores the default order; it does not clear exclusions. Multiple plans NOT implemented (one plan only, as agreed).

### Text trimmed (Ronnie's wording kept)
- "Progress, not perfection" now appears once (IREAP finish-box); the brand tagline is now "by Njenga wa Wainaina".
- "Miss a day? Pick up where you left off" now appears once (hero); the Today explainer and the "Give yourself grace" note are shortened.
- Projected finish shown once ("Your finish line" panel); the dashboard stat is now "Current streak" (days in a row); `finishMessage` no longer repeats the date or chapter count; `fmtDelta()` removed as dead code.
- Settings sync sentence appears once (the sync status line); the signed-out account line is now empty, signed-in still shows "Signed in as …".
- "Completed today" renamed to "IREAP done today" for the reflections stat.
- "Up next" pill "N of 1,189 completed" shown only on the first row, and now uses the active plan total.
- "Clear this day's record" note removed (the confirm dialog already explains it).
- Ronnie's wording verified intact: "Scripture Deep Dive", "For wisdom and understanding of Scripture", "IREAP is a self-made five step reflection method that's a acrostic", "keep it simple", "Minimize".

### Kept, not broken
- Sign-in, cloud sync, backup/restore (restore still preserves planAnchor/readingOrder/detour/dailyTarget, now also excludedBooks/scheduleName), reconcile, future-date guard, daily target setting, IREAP scratch area (never saved — no handlers write it anywhere; only the yes/no checkbox is saved), IREAP yes/no checkbox, Today scroll + Minimize (now paging), IREAP Tracker pagination, PWA manifest/sw.js references. No native `confirm()`/`prompt()`/`alert()` calls (the only `confirm(` match is a code comment; `installPrompt.prompt()` is the PWA install API). No database schema change.

### Verification (this pass)
- `node --check` on the extracted inline script: syntax OK. `node --check` on the repo `sw.js`: syntax OK. `manifest.json` parses.
- Static checks: all 17 HTML handler functions defined; all 91 `getElementById` targets exist in the HTML; no duplicate function declarations; all new symbols present.
- Node harness (`/tmp/bible-quota-harness.js`, DOM stubs): 53/53 assertions pass — awards compute correctly for a 30-chapter/4-book/6-IREAP test state (6 of 23 earned), all 23 earned when the whole Bible is complete, exclusions drop the plan total 1,189 → 1,008 and are skipped by plan logic, calendar aria-labels and cell text are correct, and all render paths (`renderDashboard`, `showView` for awards/books/calendar/today, `goToToday`, `goToTodayDash`, `moveBook`, `restoreBook`, `switchToBook`, `renderAwards`, `renderDashCalendar`) run without throwing.
- NOT verified (needs a live browser after deploy): actual rendering at 320/375/430/768/1024/1280/1440, H1 at 320px, H3 at 320/375px, horizontal-scroll check, month/year dropdown interaction, Today pagination with >5 items, Awards page live, book move/remove/restore clicks, and the Home calendar tap-to-open.

## 1. Restore from backup actually applies — `importBackup()`, lines 1079–1126

**Root cause of the silent failure.** The restore used a native `confirm()`. Native dialogs are auto-dismissed (returning false) in some browsers, so the restore aborted with no toast and no state change — exactly what happened on Nov 22 when the edited backup was selected three times. A second hazard: the file input was never reset, so choosing the same file again fired no `change` event. A third: after a restore, the next cloud sync merged the old cloud copy back over the restored state.

**Fix.**
- New in-app confirmation dialog `confirmDialog()` (lines 373–384, overlay HTML line 277, CSS in the `<style>` block). `importBackup()`, `catchMeUp()`, and `resumeOriginalPlan()` all use it now. No native `confirm()` calls remain in the app (the one remaining match for `confirm(` is a code comment).
- The file input is reset (`e.target.value = ""`) after every selection, so the same file can be chosen again.
- `importBackup()` now restores `planAnchor`, `readingOrder`, `detourBook`, and `detourBeforeBook` (previously dropped — see fix 12), plus `dailyTarget`.
- After restoring, the state is pushed to the cloud **immediately** (not on the 700 ms debounce), and a `restoringBackup` flag (line 291) makes `pullAndMerge()` (line 1254) and `scheduleCloudSave()` (line 1276) skip while a restore is in progress, so the old cloud copy cannot merge back over it.
- Success shows "Backup restored." Failure shows the actual error ("Backup could not be restored: …") instead of silence.

## 2. Per-day clear in the calendar — `clearDayLog()`, lines 856–873

A "Clear this day's record" button now appears under any calendar day that has recorded chapters or unable marks (including future dates). It asks for confirmation, then deletes only that day's log entry. Chapters also recorded on another day are kept, along with their IREAP marks. This is the manual tool for removing a wrong entry such as the Nov 30 test entry if it ever survives the automatic cleanup in fix 4.

## 3. "Continue original plan" restores the original schedule — `resumeOriginalPlan()`, lines 504–521

**Root cause.** The button planted a *new* plan anchor at today pointing at the first unread chapter. Carry-over is computed from the anchor, so the backlog became zero by construction — the missed chapters were redefined out of existence, not deleted. This is what the Nov 21 test click did (`planAnchor` date 2026-11-21, chapterIndex 1).

**Fix.** The button now clears the detour, resets the reading order to the default, and clears `planAnchor` (sets it to `null`), so the schedule math falls back to the original cumulative targets. It asks for confirmation first (in-app dialog). Completed chapters are untouched.

## 4. `completed` vs `logs` reconciled — `reconcileState()`, lines 342–367

**Root cause.** Two independent sources of truth: `state.completed` (array) and `state.logs` (per-date map). A chapter completed on two dates landed in two date-logs but once in `completed`, so log-based stats counted it twice and the total once (total 32 vs "This month" 33; "Days with reading" 19 vs "Reading days" 18).

**Fix.** New `reconcileState()` runs on every save (`saveState()`, line 330), on load (`loadState()`, line 301), on every cloud merge (`mergeStates()`, line 1226), and on every backup restore. Rules:
- One chapter lives in only one date's log — keep the earliest date, drop it from later dates.
- "Unable" marks for a chapter that is completed anywhere are removed as stale.
- Log entries left completely empty are deleted.
- `completed` is rebuilt from the logs, keeping any completion that has no dated record so real progress is never lost.
- IREAP marks are kept only for chapters still completed.

**Checked against Ronnie's real backup** (Python simulation of these exact rules, not the app running): log dates 22 → 18, total stays 32, Nov 3 stays Ruth 3 + Ruth 4, Nov 21 becomes Mark 11 + Mark 12 (Ruth 1 correctly kept only on Nov 2), Nov 2 stays Ruth 1 + Ruth 2, Nov 30 is gone entirely, "Days with reading" 19 → 18, "This month" 33 → 32.

**Consequence for the Nov 30 cleanup:** on first load after deploy, the app reconciles the loaded state and the Nov 30 entry disappears on its own (its Ruth 3 is a duplicate of Nov 3; its Ruth 2 unable mark is stale). It should then sync away from the cloud copy too. The per-day clear button (fix 2) is the fallback if anything remains.

**The bad test anchor is NOT auto-removed.** `planAnchor` is still in the data. After deploy, tap "Continue original plan" once (Books view) to clear it — see deploy step 4. The true carry-over will reappear then; that is expected, not a bug.

## 5. Future-date guard — `markScheduledChapter()` line 1020, `completeChapter()` line 816

Completing chapters on a future date is now rejected with a toast ("You can only mark reading for today or earlier."). The calendar's future-date checkboxes are rendered disabled (preview only) and the copy now says so. The dashboard and achievements "days" counts now both use the today-or-earlier rule (`renderDashboard()` line 663, `renderAchievements()` line 584), so the two views can no longer disagree over a future entry.

## 6. IREAP — ephemeral scratch notes, and the "Completed today" count

**Ronnie's rule, followed exactly:** IREAP reflections live in his physical journal and are NOT kept on the computer. No persistent text fields were added.
- New scratch area in the IREAP Tracker card (Today view, HTML line 173; helpers `clearIreapScratch()` / `updateIreapScratch()`, lines 805–814): five textareas (Insight, Recap, Examine, Apply, Pray) for the next chapter needing reflection, with a "Start fresh" button and the note "Nothing written here is saved. It clears when you switch chapters, leave this page, or reload." The textareas have no handlers that write to state, localStorage, the cloud, or backups — verified by grep: zero references to the scratch field IDs in `saveState`, `exportBackup`, `importBackup`, `mergeStates`, `loadState`, or `scheduleCloudSave`. They clear when the chapter changes or the page reloads.
- The existing IREAP completed yes/no radio is unchanged.
- **"Completed today" count fixed** (`toggleChapterIreap()`, line 771; migration in `loadState()`, lines 301–329): the timestamp was stored as a UTC ISO string but compared against the local date, so after 7pm ET the UTC date was the next day and the count was always 0. It now stores the local date (`todayISO()`), and old UTC timestamps are migrated to local dates on load.

## 7. Calendar day label — `renderCalendar()`, lines 874–1018

A day with no log no longer shows the cumulative catch-up list mislabelled "Your recorded reading for this day". It now shows "Nothing recorded for this day." plus that day's planned chapters as a clearly labelled reference. Days with a log show only what was actually recorded.

## 8. Daily target is now a setting; projection uses real pace

- New `state.dailyTarget` (default 3), editable in Settings → Reading plan (HTML line 268; `updateDailyTarget()` / `updateDailyTargetHint()`, lines 626–642). `getTarget()` (line 445), `plannedStartPosition()` (line 435), and `scheduleTargetAt()` (line 423) all use it instead of the hardcoded 2/day in the `SCHEDULE` array. The `SCHEDULE` dates are still used for the plan's date range. A hint under the input says whether the chosen pace finishes by Dec 31, 2027.
- `projectedFinish()` (line 538) now uses `recentPace()` (line 523): chapters per day over the last 14 days, falling back to the pace since Nov 1, then to the daily target. The finish-line copy was updated to say the estimate uses recent pace.
- **Expect a visible change:** the projected finish will move further out than the old Jan 4, 2028, because it now reflects actual pace instead of schedule targets. That is the honest number, not a regression. The Settings hint shows what pace is needed to finish on time.

## 9. Undo is reachable — `undoChapter()`, lines 821–837; Today view rows, lines 701–769

- A visible **Undo** button now appears next to each chapter completed today (Completed Today list).
- `undoChapter()` now allows undoing any chapter completed on that day, not just the most recently completed one.
- The Today checkbox is no longer disabled when a chapter is IREAP-locked (the lock is still enforced, with the existing toast explaining how to unlock it by setting IREAP back to "Not completed"). Checkboxes for chapters completed on another day stay disabled.

## 10. "Unable to complete" is respected by the plan — `skippedChapters()` / `skippedSet()`, lines 461–470

Skipped chapters ("unable") are now excluded from carry-over (`getCarryOverChapterIndexes()`, line 455) and planned chapters (`getPlannedChapterIndexes()`, line 450), and from the "next unread" calculation (`firstUnread()`, line 418; `firstUnreadInOrder()`, line 410), so skipping a chapter no longer blocks the next one. A new "Skipped for now" section on the Today view (HTML line 154) lists skipped chapters with the date skipped and a "Bring back" button that clears the skip.

## 11. Progress shows one decimal — `renderDashboard()`, lines 663–700

Progress is now `pct.toFixed(1)` (e.g. 2.7% instead of rounding 32/1,189 to 3% — previously 1/1,189 rounded to 0%) and the ring uses the unrounded value. The "Up next" pill now shows the completed count ("32 of 1189 completed") instead of the sequence position.

## 12. Sync status no longer duplicated — `cloudStatus()` line 1127, `setAuthUI()` line 1131

`cloudStatus()` now writes only `#syncStatus`. The account line (`#authMessage`) is set by `setAuthUI()` only. The two elements have different jobs: account state vs last sync state.

## 13. Install can actually work — `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`

- Install copy fixed (Settings line 269): "This site is already secure, so you can install it on your devices."
- New `manifest.json` (name, short name, colors, both icons) and `<link rel="manifest">` in the head (line 16), plus an `apple-touch-icon` link for iPhone Add to Home Screen.
- New `sw.js`: caches the app shell, network-first for page navigations (so a new deploy shows up) with cache fallback, cache-first for same-origin assets. The existing `sw.js` registration no longer 404s.
- Icons are simple generated PNGs (green rounded square, gold four-point star) made for this fix — replace them with a proper app icon later if wanted.

## 14. Sync / Refresh disabled while signed out — `setAuthUI()`, lines 1131–1151

The top Refresh button and the Settings "Sync now" button (now `id="syncNowButton"`, line 267) are disabled when no user is signed in, and re-enabled on sign-in. The click-time guards remain as a backup.

## 15. Restore preserves plan fields — `importBackup()`, lines 1079–1126

Covered in fix 1: `planAnchor`, `readingOrder`, `detourBook`, `detourBeforeBook`, and `dailyTarget` are now included in the restored state (previously dropped, silently resetting the reading order and any detour). `exportBackup()` already exports the full state, so no change was needed there.

## 16. Removed injected junk

Both repo files end with content-filter artifacts (hidden inputs + an external script tag) injected after `</script>`. Removed from this fixed copy. The file now ends cleanly with `</script></body></html>`.

## 17. IREAP Tracker: most-recent-first, 5 at first, View more, pagination — `renderOutstandingIreaps()`, lines 784–852

The tracker used to list every completed chapter (32 now, growing) in chapter order. It now shows the most recently completed first and pages through the list.

**Fix.**
- New `ireapCompletionDate(i)` finds the log date a chapter was completed on (earliest date if it appears in more than one log, matching the reconcile rule in fix 4); a chapter with no log date counts as oldest. New `sortedIreapChapters()` sorts by that date, latest first, with ties on the same date broken by higher chapter index first.
- The tracker shows only the first 5 entries initially. With more than 5, a "View more" button appears under the list; tapping it expands to 10 entries. With more than 10, Previous / Next buttons and a "Page X of Y" label page through the full list 10 at a time (on page 1 the Previous button reads "Minimize List" and collapses the list back to 5 — see fix 18; Next disabled on the last page). With 10 or fewer, View more shows them all and no pagination appears. With 5 or fewer, no View more button appears at all.
- The Completed / Not completed radios are unchanged (same markup, same `toggleChapterIreap` wiring, same status text). The paging state (`ireapTrackerExpanded`, `ireapTrackerPage`) is module-level memory only, so a radio change — which re-renders the Today view through `toggleChapterIreap()` — keeps the user's place (expanded state and page). The page is clamped if the list shrinks, and the expanded state resets if the list drops to 5 or fewer.
- **No reflection text is stored.** The tracker still shows completion status only. The ephemeral IREAP scratch area (fix 6) is untouched: its textareas still have no handlers that write to state, localStorage, the cloud, or backups.

## 18. Today subsections scroll past 5 items; IREAP pager "Minimize List" on page 1 — `renderRows()` in `renderToday()`, `renderOutstandingIreaps()`

**Fix.**
- The three Today subsections (Carry Over Chapters, Planned Daily Chapters, Completed Today) now scroll when they hold more than 5 items: `renderRows()` adds a `scroll-list` class to the subsection when it has more than 5 rows, capping its height at about 5 rows (340px) with vertical scrolling. With 5 or fewer items nothing changes — the full list shows with no scroll.
- In the IREAP Tracker pager, the Previous button now reads "Minimize List" when the pager shows "Page 1 of X". Tapping it collapses the tracker back to the first 5 entries (and brings back the View more button). On later pages it still reads "Previous" and goes back one page. Next is still disabled on the last page.

## Also changed

- Reminders checkbox reflects the effective state (`effectiveReminderOn()` / `syncReminderCheckbox()`, lines 610–617; `updateReminderSettings()`, line 618; `setupReminderTimer()`, line 650; boot, line ~1311): checked only when reminders are enabled **and** notification permission is granted. Enabling reminders now asks for notification permission if it has not been granted yet, instead of silently doing nothing.
- `catchMeUp()` (line 471) now uses the in-app confirmation dialog instead of native `confirm()`.
- `getAssignedChapters()` (line 1019) is no longer used by the calendar after fix 7; it is kept as a small utility.

## Not changed in this pass

- Reading Shelf — untouched, as instructed.
- The hardcoded Supabase URL and publishable key (flagged in the code report as belonging in deploy-time config) — left as-is.
- `SETUP.md` / `README.md` hosting and auth docs (stale) — left as-is.
- The remaining cosmetic/copy items from the report are all covered above except the sidebar `aria-label` / overflow suggestions, which were not in scope.

---

# Deploy steps for Ronnie

1. **Back up first.** The original backup from Nov 22 is already saved in your files ("Bible Challenge backup ORIGINAL 2026-11-22.json"). Download a fresh backup from the live app before deploying, just in case.
2. **GitHub repo** (`ronniew3099-star/Bible-365-project`): replace `index.html` with the fixed copy in `~/workspace/code-fixes/bible-365-project/index.html`, and add the four new files from the same folder — `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png` — at the repo root, next to `index.html`. Commit and push.
3. **Redeploy on Cloudflare Workers** the same way the current version was deployed. If the Worker serves from the repo, the push may deploy it automatically; otherwise run the usual deploy command.
4. **Clear the bad test anchor.** Open the app and sign in. Then go to Books and tap **"Continue original plan"** once, and confirm. This clears the test anchor (date Nov 21, pointing at Ruth 2) and restores the original schedule. Your true carry-over will reappear — that is expected, not a bug. Do this once only.
5. **Verify (do not skip).**
   - Total still **32 / 1,189**.
   - Nov 3 still shows **Ruth 3 and Ruth 4**. Nov 21 still shows **Mark 11 and Mark 12** (Ruth 1 now only on Nov 2). Nov 2 still shows Ruth 1 and Ruth 2.
   - Nov 30 shows **"Nothing recorded for this day."** (If it still shows anything, tap Nov 30, then tap **"Clear this day's record"** and confirm.)
   - "Days with reading" should read **18** and "This month" **32**.
   - Projected finish will show a **later** date than the old Jan 4, 2028 — that is the real-pace projection (fix 8), not a regression.
   - Daily target is now **3/day** by default. Change it in Settings → Reading plan if you want 2/day back.
6. **Check the restore.** In Settings, download a backup, then restore it: a confirmation dialog should appear, and after confirming you should see "Backup restored." If you ever restore and see no confirmation, do not assume it worked — check the numbers.

# Could not verify (app was not run)

- The in-app confirmation dialog, the per-day clear flow, the service-worker registration, and the install prompt were verified only by reading the code and by syntax checks. They need a live check after deploy.
- The cloud round-trip after a restore (restored state pushed immediately, then a later sync) was verified only by reading the code. Confirm after deploy that a restore survives a page reload and a Refresh.
- The projected-finish date after deploy depends on the real log data at that moment; the value in step 5 is a direction ("later than Jan 4, 2028"), not an exact date.
- The IREAP Tracker paging (fix 17: 5 at first, View more to 10, Previous/Next past 10, most-recent-first order, place kept after a radio change) was verified only by reading the code and by syntax checks. It needs a live check after deploy: with 32 completed chapters the tracker should show 5, View more should show 10, and the pager should read "Page 1 of 4" with working Previous/Next.
- The generated icons are placeholders. The manifest references them by filename; if the deploy serves files from a subfolder, adjust the `src` paths in `manifest.json` and the `<link>` tags in `index.html` to match.
