# Bible Challenge — Code Fixes Changelog

Fixed copy of `index.html` (from the repo clone at `~/workspace/code-review/bible-365-project/index.html`, 1,033 lines) plus the new files the install fix needs. The clone was not modified. The Reading Shelf was not touched in this pass.

Base: repo file as cloned Nov 21, 2026. Fixed file: `index.html` (1,313 lines). Diff vs clone: 366 lines added, 86 removed.

Verified by: re-reading every changed function, `node --check` on the extracted inline script (syntax OK) and on `sw.js` (syntax OK), `manifest.json` parses, icons are valid PNGs, and a Python simulation of the reconcile rules against Ronnie's real backup (see fix 4). The app was **not run** — see "Could not verify" at the bottom.

---

## Next milestone replaces Overall Progress — Figma audit item 3 (Nov 23, 2026)

The Figma audit said the hero ring and the Overall Progress card showed the same measurement. The ring stays; the card is now "Next milestone" instead.
- It shows the closest unearned award and how far away it is (for Ronnie right now: "7-day streak — 1 more day"). It uses the current streak, not the longest ever, so after a missed day a new run starts from zero.
- The exact count ("32 / 1,189") still shows in the Up next panel. The hero ring still shows the percentage.
- The replacement shows in both layouts: the desktop third stat card and the phone compact summary.

## Catching up, gently — carry-over recovery choices, Figma audit item 5 (Nov 23, 2026)

The Figma audit said a big carry-over number feels discouraging. A new "Catching up, gently" panel now sits on the dashboard below the Today's reading overview, and only shows when there is carry-over to deal with or a plan is running.
- Four choices, each a real plan change: "One extra a day" (adds the oldest missed chapter to each day's reading until caught up); "Spread over 7 days" (shares the missed chapters across the next 7 days); "Start fresh from today" (puts the missed chapters back in order from today at the normal pace, finish date moves later); "Let them go" (skips the missed chapters and continues from today).
- Skipped chapters are not completed or deleted. They appear in the Read view's Skipped section with individual "Bring back" buttons.
- The active plan shows its progress (e.g. "3 of 14 missed chapters caught up.") with a "Stop this plan" button. Picking a different choice undoes the old plan's effects first. The daily target setting itself never changes.
- "Start fresh from today" and "Let them go" ask for confirmation first, using the app's own confirm dialog.

## Avatar icon fix and sticky pager fix (Nov 23, 2026)

Two bugs Ronnie reported with screenshots on Nov 23, 8:09pm.
- Avatar: the person icon showed next to the R even after the earlier header fix. Root cause: the person icon is an SVG, and setting .hidden on an SVG does nothing, so the icon never hid. It was not a cached copy. Fixed with a helper that sets the hidden attribute and the display directly. Signed in shows only the R; signed out shows only the person icon.
- Sticky pager on the Books view: the pager rule had 175px of right padding sized for the old desktop buttons, squeezing "1 of 7" into a narrow strip on phones. It now uses 60px, matching the avatar. The pager also respects the phone's top safe area instead of jamming under the status bar, and the reading-schedule paragraph has right clearance so its text never runs under the avatar.

## Desktop account menu — Figma audit item 3 (Nov 23, 2026)

The Figma audit said the desktop top-right controls (date, account button, refresh button) felt like three separate floating elements. The desktop now uses the same avatar and account menu pattern as phones.
- One circular purple avatar sits in the top-right on all screen sizes (initial when signed in, person icon when signed out). It opens the account menu with the email line, a sync status line, Refresh, and Sign in / Sign out.
- The separate desktop account button and refresh button are deleted. Refresh still works, it now lives in the menu.
- The date now sits directly under the "Your Journey" heading instead of floating top-right. On phones the date stays hidden, same as before.
- Sync status shows subtly in the menu: "Not synced yet" when signed out or no sync has run, "Synced just now" within 60 seconds of a sync, otherwise "Synced at h:mm AM/PM". It updates on every successful sync and each time the menu opens.
- Sign out in the menu is one tap on all sizes now. The desktop two-tap guard is gone with the exposed button it guarded; opening the menu is the guard.
- Phone behaviour is unchanged: same avatar, same menu, same one-tap sign-out.

## Today's reading and Start reading — Figma audit item 2 (Nov 23, 2026)

The Figma audit said the dashboard reports progress well but the user's main task is reading. A new "Today's reading" panel now sits right after the hero on the dashboard, on all screen sizes.
- It shows today's planned chapters by name (e.g. Colossians 1 · Colossians 2 · Colossians 3) with the count, and a gold "Start reading" button that opens the Read page.
- Start reading only navigates to the Read page. It does not mark anything complete; Ronnie still marks each chapter complete himself after reading it, same as before.
- Chapters already read today show with a checkmark and struck-through text. When all of today's chapters are done, the panel says "Today's reading is complete." and the button becomes a quieter link to the Read page. When no chapters are planned, it says "No scheduled chapters".
- The chapter list is a fixed set for the day (a new helper that mirrors the planned-chapters function without filtering out completed chapters), so a chapter does not roll off the list as soon as it is marked done. The rolling planned list used by the overview panel, the compact summary, and the Read view is unchanged.
- The hero buttons, the compact summary, the overview panel, and everything else are unchanged.

## Compact progress summary on mobile — Figma audit item 1 (Nov 23, 2026)

The Figma audit said the three full-width statistic cards take about a whole phone screen. On phones (max-width 700px) they are now one compact summary card instead of three stacked cards.
- Top row: "Today: X of Y" with a small progress bar underneath (X = chapters read today, Y = chapters planned today, from the same planned-chapters source the overview panel uses). The bar has a progressbar role with aria values for screen readers.
- Below it, two compact values side by side inside the same card: Current streak and Overall progress.
- All three numbers are still visible on phones; nothing was removed, only compacted. The three inner values have no separate card containers on mobile.
- Laptop and tablet are unchanged: the three stat cards stay exactly as they are.

## Header bug fix — account menu and avatar (Nov 23, 2026)

Ronnie sent a screenshot showing the account menu with both "Sign in" and "Sign out" at once, and the avatar with both the "R" initial and the person icon at once. Root cause: the code was hiding the extra one, but the styling overruled the hiding, so both showed anyway.
- The menu now shows only what applies: signed in, it shows Refresh and Sign out; signed out, it shows Refresh and Sign in.
- The avatar now shows only the "R" initial when signed in and only the person icon when signed out.
- Sign out in the menu is now one tap. Opening the menu is already the guard against an accidental tap, so the double-tap confirmation is gone there. The desktop account button still asks twice, because it sits out in the open.

## Figma 6: polish — bigger small text, darker muted text, one date (Nov 23, 2026)

The last of the Figma feedback items. On all sizes:
- Small text inside cards is now 12px (was 10–11px in places): stat labels, Reading rhythm labels, book footers, award cards, and the sign-in form labels, among others. Compact labels that have to stay small for layout reasons (calendar day cells, mobile nav labels, pager labels) are unchanged.
- Muted text is now a darker purple-gray (#4a4457, was gray-green #5a665e), so it reads clearly on white cards and the lavender background.
- The date now shows once, in the top bar. The repeated dates in the overview panel header and above "Your reading" are gone. Dates that carry distinct information (calendar month, selected day, projected finish date, target date) are unchanged.

## Figma 5: statistics simplified to three (Nov 23, 2026)

The four stat cards below the hero overlapped. Now there are three:
- Today's chapters (how many you read today), Current streak (days in a row), and Overall progress (chapters completed of the plan total).
- Chapters remaining moved to the Books page under Progress details, so it is still easy to find.
- Reading days and the IREAP total were already shown elsewhere on the dashboard, so they were removed from the stats and nothing is lost.
- The estimated finish date stays only in the Reading rhythm finish line, as before.

## Figma 4: carry-over summary on the dashboard (Nov 23, 2026)

The dashboard carry-over card listed every carried-over chapter as a dense wall of small text. Now it shows a short summary instead:
- The card keeps its label and big count number, and under it shows the first few books as chapter ranges, for example "Ruth 1–4, Philippians 1–4, James 1–5, and 53 more".
- "Show all" expands the full list right there on the dashboard; tapping again collapses it back to the summary. The button says whether the list is expanded, so a screen reader announces it.
- "Review carry-over plan" jumps to the Read page, where the full carry-over list already lives.
- The Read page carry-over list is unchanged, and the carry-over count itself is unchanged. Only the dashboard presentation changed.

## Figma 3: bottom navigation with More menu (Nov 23, 2026)

The bottom bar on phones had seven destinations and was crowded, with small labels. On phones only:
- The bottom bar now has five items: Home, Read, Calendar, Books, and More. The labels and icons are slightly bigger (11px labels, 22px icons) so they are easier to read.
- IREAP, Awards, and Settings moved into the More menu. Tapping More opens a small menu above the bottom bar with those three destinations. Tapping one opens that page and closes the menu. Tapping outside the menu or pressing Escape closes it.
- When you are on IREAP, Awards, or Settings, the More button shows as the active one in the bottom bar.
- On laptop and tablet nothing changes: the sidebar still shows all seven destinations.

## Figma 2: compact mobile hero (Nov 23, 2026)

The hero took up most of the first screen on phones and the progress ring sat detached below the buttons. On phones only:
- The progress ring now sits beside the heading instead of below the buttons.
- The supporting text is shorter on phones ("Miss a day? Pick up where you left off."). The full text still shows on laptop and tablet.
- The hero padding is smaller and the flame is smaller, so the hero is about half as tall as before and today's reading shows on the same screen.
- Below 360px wide the two hero buttons stack vertically.
- On laptop and tablet nothing changes.

## Figma 1: mobile header with avatar menu (Nov 23, 2026)

The account and refresh buttons took up the top-right corner on phones and squeezed the heading into narrow lines. On phones only:
- The account and refresh buttons are now one small purple circle with your first initial. Tapping it opens a menu with your account, Refresh, and Sign in or Sign out. Sign out still needs two taps to confirm.
- The heading now uses the full width and is 32px on phones.
- On laptop and tablet nothing changes: the account and refresh buttons work exactly as before.

## Visual refresh: smoother lavender look with consistent line icons (Nov 23, 2026)

Ronnie asked to apply the smoother look from the mockup to the real app. Styling only; no data, features, or wording changed.
- Page background is now a soft lavender (#e9e5f2) and cards are white with a softer purple-tinted shadow.
- Sidebar is white with a deep purple logo block at the top (white name text, light lavender "by Njenga wa Wainaina") and the active page highlighted in soft gold. The nav labels "Books of the Bible" and "Settings & backup" now fit on one line.
- Hero gradient is slightly darker and less saturated, so it feels calmer.
- Icons are now one consistent set of inline SVG line icons: sidebar and bottom navigation, Move up / Move down / Delete, the refresh button, the IREAP lightbulb, the award icons, and the lock status. No emoji is used as an icon any more. Checkmarks, the plus in button labels, the scroll-hint arrow, and the arrows in hero buttons are plain text and were left as they are.
- Serif headings stay.

## Shorter page titles and book card buttons on one line (Nov 23, 2026)

Ronnie asked for shorter headings and for the book card buttons to fit on one line on laptop and tablet.
- Titles: the Dashboard heading is now "Your Journey" (was "Your journey, one chapter at a time.") and the Books of the Bible heading is now "Progress Report" (was "Every book, one chapter at a time."). Both are changed in the page header and in the titles shown when switching views, so they stay changed when navigating.
- Book card buttons: Move up, Move down and Delete now stay on one line. The buttons share the row equally and no longer wrap. The book grid is now two columns up to 1280px wide, so the cards are wide enough on laptop and tablet. On narrow tablets (below 900px) the icon sits above the label inside each button, like on phones, so they still fit. On very narrow screens (below 768px) the grid is a single column. On phones nothing changes.

## Sticky pagination on all screen sizes and pinned sidebar (Nov 23, 2026)

Ronnie asked that the Books and Awards page controls stay visible while scrolling on bigger screens too, and that the left-hand menu stay pinned while the page scrolls.
- Pagination: the Books and Awards page controls are now sticky at the top on every screen size, not just phones. They keep a solid background so content scrolling underneath does not show through, and leave room on the right so they do not slide under the pinned account and refresh buttons. On phones only the arrow icons show (saving space); on tablet and desktop the full Previous and Next words show, since there is room for them.
- Sidebar: the left-hand menu now stays pinned in place while the main content scrolls on desktop and tablet. It scrolls independently within its own area if its content is ever taller than the screen, so no menu item becomes unreachable. On phones nothing changes, since the menu there is the bottom bar and the sidebar is hidden.

## Scrollable chapter lists with visible scrollbar and "Scroll for more" hint (Nov 23, 2026)

Ronnie asked that the three chapter lists on the Read page (Carry Over Chapters, Planned Daily Chapters, Completed Today) scroll when they have more than 5 items on phones, with a visible scrollbar and a flashing "Scroll for more" hint so it is obvious the lists scroll.
- Responsive list height: on phones the lists show 5 chapters and scroll after that; on tablets they show 8; on desktops they show 10. Each height includes a small peek of the next row so the cut is unmistakable. A list with 5 or fewer items on phones (8 tablet, 10 desktop) fits fully and does not scroll.
- Visible scrollbar: a solid deep purple thumb with a pale lavender track, 8px wide, on all sizes, in the app's purple theme (Firefox uses the same colors). It only appears when a list actually overflows. Note: on iOS Safari scrollbars are overlay-style and may fade out when not actively scrolling, so the purple thumb is clearly visible while scrolling and stays visible on desktop, but CSS alone cannot force iOS to keep it on screen permanently.
- "Scroll for more" hint: a small deep purple line under each list that reads "Scroll for more" with a down arrow on the same line. It gently flashes (soft opacity pulse) and the arrow bobs down. It shows only when the list scrolls and there is more below. When the list is scrolled all the way down, the hint stays and the arrow flips to point up; scroll back up and it flips back down. When a list fits fully it does not show. The hint is a visual cue only (hidden from screen readers) and respects reduced-motion settings.
- The old bottom fade that used to signal more content is removed, since the scrollbar and the hint now do that job.

## Reading rhythm subsections in one row on phones (Nov 23, 2026)

Ronnie asked that the three Reading rhythm subsections (This week, This month, Reading days) fit in one line in mobile view. The grid now stays 3 columns on phones instead of dropping to 2, so all three sit side by side in one row like on desktop. The cards have a little less padding and slightly smaller small text on phones so the narrower cards read well. The wording is unchanged.

## Account button is now sign in / sign out (Nov 23, 2026)

Ronnie asked that the pinned account button (top right, shows his username or "Signed out") stop refreshing and become a sign-in / sign-out button.
- Signed out: tapping it opens the Settings view and moves focus to the email field, so he lands on the sign-in form.
- Signed in: tapping it once shows a toast "Tap again to sign out." Tapping again within 5 seconds signs him out and shows "Signed out." Nothing happens on the first tap, so an accidental tap cannot sign him out. After 5 seconds the confirmation expires and a later tap starts it over.
- The button labels now say what it does: "Signed out. Tap to sign in." when signed out, "Signed in as [email]. Tap to sign out." when signed in. The button text is unchanged (username when signed in, "Signed out" when not).
- The Refresh button is unchanged and still refreshes. The Sign out button in Settings is unchanged and still signs out directly with no confirmation.
- The sign-out logic is now shared between the account button and the Settings button, so both call the same code.

## Home consolidation — Reading rhythm panel and target-date pace (Nov 23, 2026)

Ronnie asked to consolidate the Home page: remove the yellow dots from the calendar, fold the heatmap into the monthly calendar, and make the finish line a section in one panel that shows his current pace and how many chapters a day he needs to finish by a future date he picks. Titles updated at his request: the panel is now just "Reading rhythm" and the section is "The finish line".
- One panel, "Reading rhythm", now holds the three insight numbers (This week / This month / Reading days), the monthly calendar, and the finish line section. The old standalone "Reading calendar" panel is gone and "Up next" is now a full-width standalone panel.
- Calendar dots removed: the gold/green dot markers are gone from day cells in both the Home calendar and the Reading calendar view. The date number and the "N ch · IREAP x/y" text line remain, and the screen-reader label still reads the full info. The dot legend in the Reading calendar view is replaced with the shading note.
- Heatmap folded into the calendar: each day cell is now shaded purple by chapters read that day (1 = pale lavender, 2 = light purple, 3 = medium purple, 4+ = deep purple), reusing the heatmap shades. Darker cells (3 and 4+) use white text for contrast. The separate "Last 30 days" heatmap grid is removed. Both calendars show the note "Darker purple means more chapters read that day."
- The finish line section keeps the projected completion date and current pace, plus the "Original target: December 31, 2027" line. Added a date picker ("I want to finish by") and a pace line: pick a future date and it shows "To finish by [date], read X chapters a day." The target date is saved in state (same pattern as the schedule name) so it survives reloads and syncs.

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

## Phase 3 follow-up — Progress ring centering and sticky header (Nov 23, 2026)

Ronnie reported the circle completion graphic (progress ring) sits awkwardly at the bottom-left of the hero on phones, and asked for the Refresh button to stay visible while scrolling.
- Progress ring: on phones (≤700px) the ring is now centered below the buttons (`justify-self:center`) with top margin, instead of hugging the left edge with empty space to its right.
- Refresh button: the topbar (which holds the page title and the Refresh button) is now sticky (`position:sticky; top:0`) with the page background, so the Refresh button stays visible at the top while scrolling instead of scrolling away.

## Phase 3 follow-up — Awards pagination and centered burning bush (Nov 23, 2026)

Ronnie said the Awards page had not changed on his phone (the 1-column fix was live on the site but his phone was showing a cached copy) and asked for pagination plus text that fits. He also asked for the burning bush to be centered on the home page on phones.
- Awards pagination: the Awards page now shows 10 awards per page (23 awards = 3 pages) with Previous / "Page X of 3" / Next, using the same `buildPager` as Books. This keeps the page from being one very long scroll on phones and pairs with the 1-column cards so the text fits.
- Burning bush: on phones (≤700px) the hero art (burning bush SVG) is now centered (`.hero-art` flex, `justify-content:center`), matching the centered progress ring.
- Note: the Awards 1-column fix from the earlier push is live on the site (verified 154,207 bytes); if Ronnie still sees the old 2-column layout, his phone is showing a cached copy and he should close the app/tab fully and reopen it.

## Phase 3 follow-up — Mockup-first workflow: pinned Refresh, Books icons, sticky pagination (Nov 23, 2026)

Ronnie asked that changes be shown as a mockup before pushing the code live. All three changes below were made, shown as mockups, and approved by Ronnie before this push.
- Refresh button: only the Refresh button is now pinned on phones (≤700px) — `position:fixed` at the top right with a shadow, so it stays visible while scrolling. The whole topbar is no longer sticky; the page title scrolls away as normal. The title has right padding (115px) so it does not run under the pinned button.
- Books page, less wordy on phones (≤700px): the Up / Down / Remove buttons are now icon-only — up arrow, down arrow, and trash can — with the words hidden. The buttons are 44px square touch targets. The book card button now reads "Read next" instead of "Read this book next". The screen-reader labels (aria-labels) are kept, so the buttons are still announced by name.
- Pagination, sticky at the top on phones (≤700px): on the Books page the pagination now sits right underneath the "Sort by" select field instead of at the bottom of the list, and it sticks at the top while scrolling (top: 60px, so it clears the pinned Refresh button). The same method is used on the Awards page: the pagination sits right under the "Your awards" header and sticks while scrolling. The Today subsections and IREAP Tracker pagers were not changed (they are sub-list pagers, not page-level pagination).

## Phase 3 follow-up — Books buttons on one line, Refresh pinned at all sizes (Nov 23, 2026)

Ronnie approved both mockups before this push.
- Books buttons: on phones (≤700px) the three action buttons are now on one line, each a vertical stack with the icon on top and a small label underneath — "Move up", "Move down", "Delete". The book cards are now full-width (1 column) on phones, like the Awards page, because the two-column cards were too narrow for three labelled buttons to fit on one line. Desktop keeps 3 columns, tablet 2 columns. The screen-reader labels are unchanged.
- Refresh button: the pinned Refresh button now applies at all responsive sizes, not just phones. The fixed positioning is a base rule, and the topbar actions have right padding (115px) so the date label on desktop and tablet does not slide under the pinned button. On phones the actions padding is overridden to 0 (the date label is hidden there).

## Phase 3 follow-up — Awards as single cards (Nov 23, 2026)

Ronnie approved the mockup before this push.
- Awards: on phones (≤700px) the award cards are now single full-width cards (1 column), like the Bible books. The earlier 1-column rule was in the stylesheet before the base 2-column rule, so the base rule was overriding it and the page stayed 2-column with text breaking mid-word. The 1-column rule is now also in the later ≤700px media query (after the base rule), so it actually takes effect. The card layout (icon left, text middle, status right) is unchanged.

## Phase 3 follow-up — Expand and collapse for IREAP Tracker and Reflect (Nov 23, 2026)

Ronnie approved the mockup before this push.
- Today's reading, right-hand panel: the IREAP Tracker and "Reflect on your next chapter" subsections now each have an expand/collapse chevron button in their header. The chevron points up when the section is expanded and down when it is collapsed. Both sections start expanded, matching the previous always-visible behaviour. The toggle is available at all screen sizes (phone, tablet, desktop). The button has an aria-expanded attribute and an aria-label ("Collapse IREAP Tracker" / "Expand IREAP Tracker") so a screen reader announces its state and action.

## Phase 3 follow-up — Sticky pagination flush at the top (Nov 23, 2026)

Ronnie approved the mockup before this push.
- Sticky pagination (Books and Awards, phones ≤700px): the pagination now sticks flush at the very top of the screen (top: 0) instead of 60px below it. The 60px gap had let book cards scroll up and show through above the pagination, making a card look split in two. The container has right padding (120px) so the Next button clears the pinned Refresh button, which sits at the top-right on the same bar. The solid background covers the content scrolling beneath it.

## Phase 3 follow-up — Pagination as arrow icons on phones (Nov 23, 2026)

Ronnie approved the mockup before this push.
- Pagination (Books and Awards): on phones (≤700px) the Previous and Next buttons now show only arrow icons (left and right arrows) with no words, so the "Page 1 of 3" label fits on one line instead of wrapping to three lines. On larger screens the words still show next to the arrows. The buttons have aria-labels ("Previous page" / "Next page") so a screen reader still announces them by name. The IREAP Tracker's own pager was not changed.

## Phase 3 follow-up — Account button beside Refresh (Nov 23, 2026)

Ronnie approved the mockup before this push.
- Pinned account button: a new button sits beside the Refresh button at the top-right, pinned so it stays visible while scrolling. It shows the signed-in account name (the part before @, e.g. "ronniew3099") and "Signed out" when no one is signed in. Tapping it refreshes your reading progress, the same as Refresh. The button has an aria-label with the full email ("Signed in as ronniew3099@gmail.com. Tap to refresh.") so a screen reader announces the full account. The Refresh button is now just the arrow icon, with no word, to make space. The title and date label have extra right padding so they do not run under the wider pinned buttons.

## Phase 3 follow-up — Carry Over Chapters stacked vertically, pagination arrows moved in (Nov 23, 2026)

Ronnie approved these changes before this push.
- Today's reading lists (Carry Over Chapters, Planned reading, Completed today): the pagination arrows are gone. All chapters now show stacked vertically in a single list, in the same row format as before, with no pages to tap through. The list scrolls with the page.
- Sticky pagination (Books and Awards, phones): the pagination bar's right padding is now 175px instead of 120px, so the Next arrow sits clear of the pinned account button and Refresh icon at the top-right, with a gap between them. Previously the wider account button covered the Next arrow.

## Phase 3 follow-up — Scroll fade, "1 of 3" label, Reflect moved to IREAP page, Example collapsible (Nov 23, 2026)

Ronnie approved these changes before this push.
- Today's reading lists (Carry Over Chapters, Planned reading, Completed today): each list is now in a fixed-height box that shows 5 chapters at a time and scrolls vertically inside the box to reveal the rest. The 14 Carry Over chapters divide into groups of 5, 5, and 4 as you scroll. The bottom of the box fades out while there is more to see, so it is clear the list continues; the fade goes away once you reach the bottom. No arrow buttons on these lists.
- Pagination labels (Books, Awards, and IREAP Tracker): the label now reads "1 of 3" instead of "Page 1 of 3" or "Pg. 1 of 3", to save space.
- "Reflect on your next chapter" (the five scratch writing boxes) moved from the Read page to the IREAP page. It now sits on the IREAP page after the Example panel and before the "Progress, not perfection" box. The Read page side panel now shows only the IREAP Tracker. Nothing written in the Reflect boxes is saved, as before.
- The "Example: IREAP for Ruth 1" panel on the IREAP page is now expandable and collapsible, with the same chevron button as the Reflect section. It starts expanded.

## Phase 3 follow-up — Carry Over Chapters scroll to 5 at a time, pagination label Pg. (Nov 23, 2026)

Ronnie approved these changes before this push.
- Today's reading lists (Carry Over Chapters, Planned reading, Completed today): each list is now in a fixed-height box that shows 5 chapters at a time and scrolls vertically inside the box to reveal the rest. The 14 Carry Over chapters divide into groups of 5, 5, and 4 as you scroll. No arrow buttons and no page numbers on these lists.
- Pagination labels (Books, Awards, and IREAP Tracker): the label now reads "Pg. 1 of 3" instead of "Page 1 of 3", to save space.

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
