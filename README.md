# Grade 12 English

A free English learning website for Egyptian Grade 12 students: grammar
lessons, grammar summaries, grammar practice with feedback and scoring, and
reading comprehension.

- Plain HTML, CSS, JavaScript and JSON. No build step, frameworks, packages,
  database, accounts, paid services or AI APIs.
- Works on GitHub Pages, including inside a repository subfolder
  (`https://<user>.github.io/<repository>/`). All paths are relative.
- **Starts with an empty content library.** All lessons, summaries, questions
  and passages are added by the teacher with the built-in content manager.

## Folder structure

```
grade12-english/
├── index.html             Home
├── grammar.html           Grammar Lessons (list, or one lesson: ?lesson=<id>)
├── summaries.html         Grammar Summaries
├── practice.html          Grammar Practice (?topic=<lesson id> preselects a topic)
├── reading.html           Reading Comprehension (list, or one passage: ?passage=<id>)
├── manage.html            Content manager (for teachers)
├── .nojekyll              Empty file: tells GitHub Pages to serve files as they are
├── css/
│   └── style.css          All styles
├── js/
│   ├── content-schema.js  Data format and validation rules
│   ├── content-store.js   Add/edit/delete/move/import logic, browser drafts
│   ├── common.js          Shared helpers: loading, empty and error states, menu
│   ├── quiz-engine.js     Quiz rules (answers, retry, scoring)
│   ├── quiz-ui.js         Quiz screens (used by Practice and Reading)
│   └── home.js, grammar.js, summaries.js, practice.js, reading.js, manage.js
├── data/                  The content (empty to start)
│   ├── grammar-lessons.json
│   ├── grammar-summaries.json
│   ├── grammar-questions.json
│   └── reading-passages.json
├── docs/
│   ├── CONTENT_FORMAT.md          Data format reference
│   └── GITHUB_PAGES_DIAGNOSTIC.md Why the first upload showed unstyled pages
├── tools/
│   ├── check-site.js       Checks files, folders, letter case and paths before upload
│   └── validate-content.js Checks the content in data/
├── tests/                 Automated tests (development only, not used by the site)
├── README.md
└── PROJECT_STATUS.md
```

The website itself needs only the six `.html` files, `.nojekyll`, and the
`css`, `js` and `data` folders. The other folders are documentation and
tools; uploading them is recommended but does no harm either way.

## Publish on GitHub Pages

**Upload the folders, not just the files.** The site breaks (plain, unstyled
pages that keep saying “Loading…”) if `css`, `js` or `data` is missing. See
[docs/GITHUB_PAGES_DIAGNOSTIC.md](docs/GITHUB_PAGES_DIAGNOSTIC.md).

1. Unzip the project and open the `grade12-english` folder, so you can see
   `index.html`, `css`, `js`, `data` and the other items.
2. On GitHub, open your repository and choose **Add file → Upload files**.
3. Select **everything** in the folder (Ctrl+A on Windows, Cmd+A on Mac) and
   **drag it onto the upload area**. Do **not** use the “choose your files”
   button: it cannot select folders.
4. Before committing, scroll through the list of files and check that it
   includes `css/style.css`, `js/common.js` and the `data/…json` files. Then
   choose **Commit changes**.
5. The first time only: open **Settings → Pages**, choose **Deploy from a
   branch**, then branch `main` and folder `/ (root)`, and save.
6. Wait 1–2 minutes and open `https://<user>.github.io/<repository>/`. If you
   still see the old page, force a reload: **Ctrl+F5** (Windows) or
   **Cmd+Shift+R** (Mac).

If the folders still do not upload, use **GitHub Desktop** (free), which
always keeps folders.

If something is still missing, every page shows a red message naming the
missing files.

## Run it on your computer

Browsers block data files when a page is opened directly from disk
(`file://`), so use any local web server, for example with Python:

```
cd grade12-english
python -m http.server 8000
```

Then open <http://localhost:8000/>.

## Adding and managing content

Open `manage.html` (also linked at the bottom of every page).

1. **Add and edit** lessons, summaries, grammar questions and reading
   passages (with their questions). You can also delete items, reorder them,
   filter questions by topic and search question text.
2. Your work is **saved automatically as a draft in this browser** (on this
   device only). Nothing is sent anywhere.
3. Tick **“Preview my draft on the student pages”** to see the draft on the
   real pages in your browser. Students do not see it.
4. **Publish:** open *Import & export* and choose *Download all four files*.
   Then, in your GitHub repository, open the `data` folder → **Add file →
   Upload files**, upload the four files (they replace the old ones) and
   commit.
5. **Back up** regularly with *Download backup file*. A backup (or any data
   file) can be **imported** on any computer. *Merge* adds new items and
   updates items with the same ID; *Replace* swaps the imported type
   completely. Files are checked before anything changes.

Notes:

- Anyone can open `manage.html` on the public site, but it only changes a
  draft in **their own** browser. Only people who can push to the GitHub
  repository can change the published website.
- The **“A teacher has reviewed this …”** tick decides how answers are shown.
  Until it is ticked, students see answers as *suggested answers*, never as
  definitely correct, and scores are marked *provisional*.
- Drafts live in the browser’s storage (usually about 5 MB). Clearing the
  browser’s site data deletes the draft, so download backups.

The data format is described in [docs/CONTENT_FORMAT.md](docs/CONTENT_FORMAT.md).

## Checks (need Node.js; no packages to install)

```
node tools/check-site.js        # before uploading: folders, files, letter case, relative paths
node tools/validate-content.js  # after editing data/*.json by hand
```

## Tests (for developers)

```
node --test tests/*.test.js     # unit tests (Node.js 18+)
python tests/e2e_test.py        # browser tests; needs: pip install playwright && python -m playwright install chromium
```

The browser tests serve the site from a subfolder (like GitHub Pages), type
placeholder test text into the content manager, and never change the files
in `data/`.
