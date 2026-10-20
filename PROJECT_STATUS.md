# Project status — Grade 12 English

_Last updated: 20 October 2026 (end of Phase 1 & 1.5, awaiting approval for Phase 2)_

Live site: https://abdo098765432110-ux.github.io/grade12-english/
Repository: https://github.com/abdo098765432110-ux/grade12-english

## Summary

| Phase | Status |
|---|---|
| Phase 1 — working core: student pages, quiz engine, content manager, empty content library | Done (19 Oct 2026) |
| Phase 1.5 — audit of the live site, fix, homepage/navigation, loading/empty/error states | Done in the project files (20 Oct 2026). **The live site stays broken until the corrected files are uploaded** (see “Exact next steps”). |
| Phase 2 | Not started. Waiting for approval. |

The content library is still **empty**. No lessons, summaries, questions,
answers, explanations or reading passages have been written for the site.
All educational content will be added by the project owner.

## Phase 1.5 — diagnosis

The live site showed plain, unstyled HTML because **the `css`, `js` and
`data` folders were never uploaded**. The repository (commit `5eb4af4`, “Add
files via upload”) contains only the 10 top-level files. Every stylesheet,
script and data file the pages request returns 404.

Paths, letter case, folder structure and GitHub Pages settings were all
checked and were already correct. The most likely cause is GitHub’s
“choose your files” upload button, which cannot select folders.

Full report: [docs/GITHUB_PAGES_DIAGNOSTIC.md](docs/GITHUB_PAGES_DIAGNOSTIC.md).

## Phase 1.5 — changes

- **Missing-file fallback (all pages).** A small inline script in `<head>`
  detects CSS/JS files that fail to load. It shows a red message naming them
  and replaces the endless “Loading…” with “This content could not be loaded.”
- **Loading state.** A spinner with “Loading…” (no animation for users who
  turn on reduced motion). After 8 seconds the text changes to “Still
  loading… This is taking longer than usual.” After 30 seconds the load stops
  and an error is shown.
- **Error state.** The page heading stays, plus “This content could not be
  loaded”, the reason (missing file / invalid JSON / timeout / no
  connection / incomplete download), a **Try again** button and a link home.
  The content manager instead shows a warning and stays usable, so a teacher
  can still reach their draft.
- **Empty states.** Unchanged from Phase 1 and re-tested: each section says
  e.g. “No lessons added yet” with a button to add content.
- **Navigation.** The menu now uses the full section names: Home, Grammar
  Lessons, Grammar Summaries, Grammar Practice, Reading Comprehension. Up to
  768 px wide, a **Menu** button opens and closes the links (it reports open
  or closed to screen readers, and Escape closes it). Without JavaScript, all
  links stay visible.
- **Homepage.** Section cards show “Loading…” and then the content counts,
  or “Could not load” if the data files are missing.
- **`tools/check-site.js`** (new). A pre-upload check for missing folders and
  files, upper/lower-case mismatches and absolute paths.
- **Clean-up.** `package.json` was removed: there are no packages, and the
  test commands are now written out in README.md. The README publish steps now
  explain how to upload folders.

## Project structure

See README.md (“Folder structure”). Summary:

```
index.html grammar.html summaries.html practice.html reading.html manage.html .nojekyll
css/style.css
js/  content-schema.js content-store.js common.js quiz-engine.js quiz-ui.js
     home.js grammar.js summaries.js practice.js reading.js manage.js
data/ grammar-lessons.json grammar-summaries.json grammar-questions.json reading-passages.json  (all empty)
docs/ CONTENT_FORMAT.md GITHUB_PAGES_DIAGNOSTIC.md
tools/ check-site.js validate-content.js
tests/ schema.test.js store.test.js quiz-engine.test.js check-site.test.js e2e_test.py
README.md PROJECT_STATUS.md
```

## Test results (20 Oct 2026)

Environment: Linux, Node.js 22.22.0, Python 3.13, Playwright 1.56 with
headless Chromium 141. The site was served from a `/grade12-english/`
subfolder, the same path as on GitHub Pages.

| Test | Result |
|---|---|
| Published repository served locally (before the fix) | Reproduced the live problem: 404 for `css/style.css` and every `js/…` file; unstyled page; Grammar page stuck on “Loading…” |
| `node tools/check-site.js` on the published repository | 37 problems found (3 missing folders, 34 missing references) |
| `node tools/check-site.js` on the fixed project | OK: 84 references checked, all present with exact case, all relative |
| `node tools/validate-content.js` | OK (empty library) |
| `node --test tests/*.test.js` | **41 / 41 passed** (36 from Phase 1 + 5 new site-check tests) |
| `python tests/e2e_test.py` | **115 / 115 checks passed** on three runs in a row |
| Project `data/` files after the tests | Still empty (verified) |

New browser checks in Phase 1.5:

- **Missing folders.** With `css/js/data` blocked, the red message names the
  missing files and no page stays on “Loading…”.
- **Stylesheet only missing.** The message shows and the content still loads.
- **Data files missing.** Error state on every student page (heading, reason,
  Try again). The content manager warns and stays usable. The home page shows
  “Could not load”. **Try again** recovers once the files are back.
- **Loading and timeout.** The spinner is shown, the slow notice appears
  after 8 s, and the load gives up after 30 s (tested with a simulated clock).
- **Navigation.** Five links in order; each opens the right page and is
  marked as current. At 375 px the Menu button sits beside the site name,
  opens and closes, and Escape returns focus to it. Without JavaScript, all
  links are visible and pages explain that JavaScript is needed.
- **Accessibility and layout.** Labels, names, headings, no sideways
  scrolling at 375, 768 and 1024 px, and colour contrast of at least 4.5:1.

Visual review of screenshots found one problem: on phones the Menu button
wrapped under the site name. It was fixed and re-tested at 320, 375 and
768 px.

## Not tested / known limitations

- **The live site has not been re-checked after the fix**, because the
  corrected files have not been uploaded yet. This session can read the
  repository but not push to it.
- Tested in **Chromium only**. Not tested in Firefox, Safari, on real phones,
  or with a screen reader. The axe-core accessibility scanner could not be
  installed here (download blocked); custom checks were used instead.
- At desktop widths the five full section names do not fit beside the site
  name, so the menu sits on its own row under it. This is intended.
- All Phase 1 limitations still apply: drafts are per browser; publishing is
  a manual upload; there is no saved student progress; the wording has not
  been checked against the official syllabus.

## Remaining tasks

- Owner: upload the corrected project, confirm the live site, then add content.
- Phase 2 and later: waiting for approval and instructions.
- Cross-browser and real-device testing; screen-reader check.

## Exact next steps

1. Unzip the delivered `grade12-english.zip` and open the `grade12-english` folder.
2. GitHub → repository → **Add file → Upload files**. Select everything in
   the folder and **drag** it onto the page (do not use “choose your files”).
3. Check that the file list includes `css/style.css`, `js/common.js` and
   `data/grammar-lessons.json`. Commit.
4. Optional: delete `package.json` from the repository. It is no longer used,
   and uploading does not remove it.
5. After 1–2 minutes, open https://abdo098765432110-ux.github.io/grade12-english/
   and reload with Ctrl+F5. Expected: a styled page with the five-link menu
   and the message “No content added yet”. If a red “could not be loaded”
   message appears instead, it names the files that are still missing.
6. Confirm, and approve Phase 2.
