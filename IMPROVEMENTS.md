# IMPROVEMENTS.md

SYD_DAT_7 is the course material repository for the Sydney Data Science part-time
course Greg taught at General Assembly, cohort 7. Last commit is from August 2017
("Add slides on IBM, Google and Microsoft"); as of 2026-07-12 the repo is a
dormant teaching archive: ~30 Jupyter notebooks (labs, homework, weekly resources),
PowerPoint slide decks, and ~53 MB of teaching datasets in `data/`. The working
tree is clean. This document assesses what it would take to make the material
useful again — either as a revived course, a public learning resource, or a
properly archived artifact.

## Decide the repo's fate first

Everything below depends on one decision: is this (a) an archive, (b) a resource
you point people at, or (c) a base for future teaching? If (a), the only work
worth doing is the "Archive properly" quick wins. Recommendations below assume
(b) or (c).

## Bugs & Fixes (content rot after 9 years)

- The notebooks target 2017-era Python/pandas/scikit-learn. Almost none will run
  cleanly today: `pd.DataFrame.ix`, `sort()` vs `sort_values()`, `sklearn.cross_validation`
  (removed; now `sklearn.model_selection`), old matplotlib style APIs. Every notebook
  in `resources/` and `lab/` needs a modernization pass — start with the most-used
  ones: `resources/week1/numpy-and-pandas.ipynb`, `lab/Week1/lab1.ipynb`,
  `resources/week8/dimensionality-reduction.ipynb`.
- The spaCy migration (commits a2f7443/6f34a67 replaced NLTK) was against spaCy 1.x;
  the spaCy 2/3 API broke `nlp` loading, `Doc` attributes and model names
  (`en` → `en_core_web_sm`). `resources/Week9/natural language processing.ipynb`
  needs rework.
- `lab/` has no Week4 and no Week10; `resources/` mixes `week8` and `Week9`
  capitalization, and `resources/week8/slope-one filtering.ipynb` /
  `resources/week7/star wars network analysis.ipynb` have spaces in filenames —
  normalize names (lowercase, hyphens) so links and shell commands work reliably.
- Typo in `resources/onesleope.ipynb` (should be `one-slope` or `slope-one`),
  and "collaborative-recommedation.ipynb" (missing 'n'). Also "General Asembly"
  typo in README.md line 2.

## Improvements

- Adopt uv for environments: add a `pyproject.toml` at the repo root and
  `uv add pandas scikit-learn matplotlib spacy networkx jupyter`, committing
  `uv.lock`. Do NOT introduce a requirements.txt. Notebooks then run under
  `uv run jupyter lab`; any helper scripts run directly with `uv run script.py`.
- Move the 53 MB `data/` directory's larger files (e.g. `beer_reviews.csv.zip`,
  `facebook_combined.txt`, `stumbleupon.tsv`) out of git history if the repo is
  ever to be shared widely — or at minimum document their provenance/licences in
  a `data/README.md`. Several (Titanic, Sacramento real estate, StumbleUpon) are
  Kaggle datasets whose redistribution terms are worth checking.
- Convert the `.pptx` decks in `slides/` to Markdown/reveal.js or export PDFs so
  the content is diffable, searchable and viewable on GitHub. The "Extras -"
  decks (Bayes, SVM, LDA, Time Series, git) are the most reusable standalone
  material.

## Testing

- Add a smoke-test harness that executes every notebook headlessly
  (`uv run jupyter nbconvert --execute --to notebook` or `nbclient`) in CI, so
  future dependency bumps reveal which notebooks still run. This is the single
  highest-leverage guard for a notebook-heavy repo.
- Strip notebook outputs on commit (nbstripout or `jupyter nbconvert --clear-output`)
  to keep diffs reviewable — several notebooks carry large embedded outputs.

## Documentation

- README.md is two lines. Add: course outline (mirroring `schedule.md`), how to
  set up the environment with uv, a map of `lab/` vs `resources/` vs `homework/`,
  and the archive/status disclaimer with the year the course ran.
- `project-rubric.md` and `homework/Homework2.md`/`Homework3.md` reference course
  logistics that no longer apply; add a header noting they are historical.
- `past-projects/` deserves an index describing what each student project was.

## Security

- No secrets spotted in the tree (data files and notebooks only), but the
  student datasets should be checked for PII before any public re-share —
  `data/phonecase.csv`, `data/listeners.csv` and `data/user_brand.csv` look like
  user-level records of unknown provenance.

## Housekeeping / Modernization

- The default branch is `master`; rename to `main` if the GitHub remote is still
  active.
- Add `.gitignore` for `.ipynb_checkpoints/`, `__pycache__/`, `.venv/`.
- If archiving (option a): fix the README typo, add a one-paragraph status note
  ("taught 2017, kept for reference, notebooks target pandas 0.20-era APIs"),
  and mark the GitHub repo archived. That is a 15-minute job and makes every
  other item optional.

## Quick Wins

1. Fix "General Asembly" → "General Assembly" in README.md and add the status paragraph.
2. Add `.gitignore` (checkpoints, pycache).
3. Add `data/README.md` listing dataset sources.
4. Rename the two misspelled notebooks and remove spaces from filenames.
5. `git remote -v` check + archive the GitHub repo if this is purely historical.
