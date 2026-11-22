# Bible Challenge — Code Fixes Changelog

Fixed copy of `index.html` (from the repo clone at `~/workspace/code-review/bible-365-project/index.html`, 1,033 lines) plus the new files the install fix needs. The clone was not modified. The Reading Shelf was not touched in this pass.

Base: repo file as cloned Nov 21, 2026. Fixed file: `index.html` (1,313 lines). Diff vs clone: 366 lines added, 86 removed.

Verified by: re-reading every changed function, `node --check` on the extracted inline script (syntax OK) and on `sw.js` (syntax OK), `manifest.json` parses, icons are valid PNGs, and a Python simulation of the reconcile rules against Ronnie's real backup (see fix 4). The app was **not run** — see "Could not verify" at the bottom.

---

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
- The tracker shows only the first 5 entries initially. With more than 5, a "View more" button appears under the list; tapping it expands to 10 entries. With more than 10, Previous / Next buttons and a "Page X of Y" label page through the full list 10 at a time (Previous disabled on page 1, Next disabled on the last page). With 10 or fewer, View more shows them all and no pagination appears. With 5 or fewer, no View more button appears at all.
- The Completed / Not completed radios are unchanged (same markup, same `toggleChapterIreap` wiring, same status text). The paging state (`ireapTrackerExpanded`, `ireapTrackerPage`) is module-level memory only, so a radio change — which re-renders the Today view through `toggleChapterIreap()` — keeps the user's place (expanded state and page). The page is clamped if the list shrinks, and the expanded state resets if the list drops to 5 or fewer.
- **No reflection text is stored.** The tracker still shows completion status only. The ephemeral IREAP scratch area (fix 6) is untouched: its textareas still have no handlers that write to state, localStorage, the cloud, or backups.

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
