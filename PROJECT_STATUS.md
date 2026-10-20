# Project status — Grade 12 English

_Last updated: 20 October 2026 (end of Phase 2, awaiting approval for Phase 3)_

Live site: https://abdo098765432110-ux.github.io/grade12-english/
Repository: https://github.com/abdo098765432110-ux/grade12-english

## Summary

| Phase | Status |
|---|---|
| Phase 1 — student pages, quiz engine, content manager, empty content library | Done (19 Oct 2026) |
| Phase 1.5 — audit of the live site, fallback/loading/error states, navigation | Done (20 Oct 2026) |
| Phase 2 — data-driven content by unit and topic, answer checking, validation | Done in the project files (20 Oct 2026) |
| Phase 3 | Not started. Waiting for approval. |

The content library is still **empty**. No lessons, summaries, questions,
answers, explanations or reading passages have been written for the site.
All educational content will be supplied by the project owner.

## ⚠ The live site is still not working

Checked on 20 Oct 2026, repository commit `fb7d74e`:

- All the right files were uploaded, but **at the top level of the
  repository, not inside the `css`, `js` and `data` folders**. For example,
  `style.css` is at the top level, but the pages load `css/style.css`.
- So the live pages are still unstyled and show the red “could not be
  loaded” message. A copy of the zip file (`grade12-english (3).zip`) was
  also uploaded; GitHub does not unzip uploaded files.
- The fix is to upload the **folders themselves** (see README.md, “Publish on
  GitHub Pages”). The loose top-level copies (`style.css`, `common.js`,
  `grammar-lessons.json`, `package.json`, the zip, etc.) are not used and can
  be deleted afterwards.

## Phase 2 report — implemented features

**1. Content by unit and topic**
- New file `data/units.json` (empty). Units group all content and appear in
  the order listed.
- A topic is a grammar lesson, and its `id` is the topic ID.
- Lessons, summaries, questions and passages can have `unitId`. Summaries and
  questions can have `topicId`, and then belong to that lesson’s unit
  automatically.
- **Grammar Lessons:** grouped by unit, with unit filter links. Pages work by
  address: `?unit=<id>` for one unit, `?topic=<id>` for one lesson (the old
  `?lesson=<id>` still works). The breadcrumb shows the unit, and the
  previous/next links follow unit order.
- **Grammar Summaries:** grouped by unit, with `?unit=<id>` and
  `?topic=<id>`. Lessons link to their own summary.
- **Grammar Practice:** choose a unit, then a topic (both with question
  counts), the number of questions, and how to answer. `?unit=` and `?topic=`
  preselect the choices.
- **Reading:** grouped by unit, with `?unit=<id>`.

**2. Grammar practice — answering and marking**
- Multiple-choice questions come only from `data/grammar-questions.json`.
- Two ways to answer:
  - *One at a time*: as before, with feedback, try again and show answer.
  - *All at once* (new): every question on one page, then “Check my answers”.
- Answers are checked against the answer key in the data. Only exact matches
  count.
- **Unanswered questions:**
  - Before checking, the page lists the blank question numbers and offers
    “Go to question N” or “Check anyway”.
  - Blank questions are marked “Not answered” and score 0. A blank answer is
    never counted as correct.
  - Any choice that is not one of the options counts as unanswered.
- **Score summary:** the score, the percentage, and separate counts for
  correct, not correct and not answered. Each question gets its own feedback
  and explanation. “Retry missed questions” includes the unanswered ones.
- The review-honesty rules from Phase 1 still apply: unreviewed answers are
  “suggested”, and scores that include them are “provisional”.

**3. Reading comprehension**
- Each passage is shown beside its questions. Questions appear **exactly in
  the stored order** in both modes and are never shuffled.
- “All questions” is the default mode, and “One at a time” is a button away.
- **Formatting is kept exactly:** paragraphs, line breaks, and spacing inside
  a paragraph. Paragraph numbers can be turned off per passage
  (`showParagraphNumbers: false`).
- On wide screens the passage stays in view while students scroll through
  the questions. A long passage scrolls inside its own box, which can also
  be scrolled with the keyboard.
- Line breaks are also kept in lesson text, summaries, question prompts,
  options and explanations.

**4. Safety and validation**
- Missing required fields, invalid IDs, and links to units or topics that
  don’t exist are all reported.
- **Answer keys get specific messages:**
  - a letter (`"B"`) or a number (`2`) instead of the option text, with the
    option it probably meant;
  - a difference only in spaces or capital letters;
  - a missing answer.
- Questions with an invalid key are never shown to students, and the quiz
  engine refuses to mark them as a second safeguard.
- A `unitId` that disagrees with its topic’s unit gets a warning, and the
  topic’s unit is used.
- Each page’s “N items could not be shown” notice now counts only the
  content that page shows.
- **Empty states:**
  - every unit with no content says so (“No lessons in this unit yet”);
  - a filtered empty unit or topic gets its own message;
  - Practice disables Start for an empty unit or topic;
  - unknown unit or topic addresses show “Unit not found” or “Topic not
    found”.
- **Older files keep working:** version 1 files are upgraded when loaded
  (`lessonId` → `topicId`, `topic` → `topicId`), with a validator reminder. If
  `data/units.json` is missing, the site behaves as if there are no units.

**5. Content manager**
- New **Units** tab: add, edit, reorder and delete units.
  - Renaming a unit’s ID updates everything linked to it.
  - Deleting a unit keeps its content and only removes the link.
- Every editor has a Unit field. Summaries and questions also have a Topic
  field, grouped by unit; choosing a topic selects its unit.
- Questions can be filtered by unit. The passage editor has a “Show
  paragraph numbers” option.
- Export, import and backup now include `units.json` (five data files).
  Imports of version 1 files are upgraded automatically.

## Data format

Version 2, documented in [docs/CONTENT_FORMAT.md](docs/CONTENT_FORMAT.md)
(units and topics, web addresses, answer-key rules, formatting, older files).

## Test results (20 Oct 2026)

Environment: Linux, Node.js 22.22.0, Python 3.13, Playwright 1.56 with
headless Chromium 141. The site was served from a `/grade12-english/`
subfolder, the same path as on GitHub Pages. All test content is placeholder
text such as “Test prompt q1”, supplied by the tests and never written to
`data/`.

| Test | Result |
|---|---|
| `node --test tests/*.test.js` (unit tests) | **57 / 57 passed** (41 before + 16 new) |
| `python tests/e2e_test.py` (browser tests) | **161 / 161 checks passed**, on two runs in a row (115 before + 46 new) |
| `node tools/check-site.js` | OK: every referenced file exists with exact case; all paths are relative |
| `node tools/validate-content.js` | OK (empty library, version 2) |
| Project `data/` files after the tests | Still empty (verified) |

New tests in Phase 2:

- **Unit tests** cover:
  - unit validation;
  - `unitId`/`topicId` checks;
  - the version 1 → 2 upgrade (files, imports, drafts);
  - answer-key messages (letter, number, case, missing);
  - grouping and ordering by unit;
  - unit rename and delete cascades;
  - `gradeAll`: blank and invalid answers are never correct, nothing
    answered scores 0, unreviewed wording, written answers not scored;
  - blank-question numbers;
  - the quiz refusing a question without a valid key.
- **Browser tests (student pages)** cover:
  - unit links and grouping, including per-unit empty states;
  - `?unit=` / `?topic=` / `?lesson=` addresses, “Unit not found”, and the
    unit in the breadcrumb;
  - line breaks kept in lessons, and the old `lessonId` upgraded on the
    summaries page;
  - practice unit/topic counts, an empty unit disabling Start, a question
    with key `"B"` skipped with a notice, and `?topic=` preselecting the unit;
  - answer-all mode: blank questions listed, “Check anyway” scoring 0 with
    “Not answered” on each, retry missed, full score;
  - reading: spacing kept exactly, questions in stored order in both modes,
    sticky passage, matched/different/not answered counts, paragraph numbers
    off;
  - a missing `units.json`, and accessibility checks on the new screens.
- **Browser tests (content manager)** cover:
  - adding units, and a lesson in a unit;
  - choosing a topic selecting its unit, and a conflicting unit being
    refused;
  - what each question stores; the unit filter;
  - passage line breaks and the paragraph-number setting;
  - unit contents counts, unit ID rename and delete cascades;
  - the `units.json` download;
  - a version 1 import upgraded, and a letter answer key rejected with a
    hint.
- **Visual review** of screenshots found three layout issues, all fixed and
  re-tested: extra space above the question list, single cards stretching
  the full width, and extra space at the top of the practice panel.

## Not tested / known limitations

- The live site could not be re-checked: the repository still has the files
  outside their folders (see above). This session can read the repository
  but not write to it.
- Tested in **Chromium only**. Not tested in Firefox, Safari, on real phones,
  or with a screen reader.
- Text formatting covers paragraphs, line breaks and spacing. Bold or
  italic text inside content is not supported (it would need a small
  formatting syntax).
- Only multiple-choice questions are scored; written answers are compared by
  the student.
- All Phase 1 limitations still apply: drafts are per browser; publishing is
  a manual upload; there is no saved student progress; the wording has not
  been checked against the official syllabus.

## Exact next steps

1. Fix the live site: upload the `css`, `js`, `data`, `docs`, `tools` and
   `tests` **folders** from the delivered zip, following README.md (drag the
   folders on a computer, or use GitHub Desktop). Check that the upload list
   shows `css/style.css` before committing.
2. Optionally delete the loose top-level copies and the uploaded zip from the
   repository.
3. Open the live site and reload with Ctrl+F5. Expected: a styled page that
   says “No content added yet”.
4. Add units first, then lessons (topics), summaries, questions and
   passages. Use the content manager, or prepare JSON files following
   docs/CONTENT_FORMAT.md.
5. Confirm, and approve Phase 3.
