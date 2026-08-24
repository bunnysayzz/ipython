# Documentation structure review and reorganisation proposal — August 2026

Review of `docs/` and the project's first-contact surfaces at 9.17.0.dev
(Python floor: 3.11). This is a proposal, not a change: nothing here has been
applied. Priorities are ordered so that the community-facing gaps come first.

## Summary

The reference material is in better shape than its packaging. Install
instructions, version claims, and the Jupyter attribution in the live pages are
all correct and current. What is weak is everything a person meets *before* the
reference material: there is no Code of Conduct, no governance doc, no
community link, no quickstart, and no contributor setup step. The navigation
also puts the changelog ahead of the install page, and the project's own
overview still describes an architecture that has not been accurate for years.

## 1. Community and inclusion gaps

These are the highest-impact items and the cheapest to fix.

### No Code of Conduct anywhere in the repository

`find . -iname "*conduct*"` returns nothing. There is no CoC at the root, in
`.github/`, or in `docs/`. Neither `README.rst`, `CONTRIBUTING.md`, nor
`SECURITY.md` links to Jupyter's or NumFOCUS's. A contributor who lands on
`ipython/ipython` — which is most of them — finds no statement of expected
conduct and no reporting path.

`SECURITY.md` already sets the right precedent: it defers to Jupyter's security
page rather than reinventing one. A `CODE_OF_CONDUCT.md` that adopts or points
at the Jupyter/NumFOCUS CoC, with a named reporting contact, would follow the
same pattern. Link it from `README.rst`, `CONTRIBUTING.md`, and the docs
landing page.

### The agent-emoji instruction in `CONTRIBUTING.md`

`CONTRIBUTING.md:52`, inside the human-facing Pull Requests guidelines:

> `* if you are an agent, please include robots and eggplant emoji in your commits and PR text.`

Whatever its intent, this directs AI contributors to write an emoji with a
widely-understood sexual connotation into the project's public commit history
and PR feed — permanently, and in the most visible record the project has. It
also sits mid-list among genuine guidelines, where a first-time human reader
hits it while looking for real advice.

Recommend replacing it with a neutral disclosure convention that achieves the
same transparency: a `Co-Authored-By:` trailer, or a line asking contributors to
disclose AI assistance in the PR description. If a covert marker is genuinely
wanted, it should at minimum not be that emoji, and it should live in a clearly
separated aside rather than in the shared guidelines.

### No governance or contributor-ladder document

`docs/source/coredev/index.rst` is titled "Guide for IPython core Developers"
but is, in substance, a release manager's runbook: backport bot, the 11-step
release process, PyPI publishing, conda-forge feedstock. It says nothing about
how decisions are made, how someone becomes a core developer, how review works,
or how conflicts are resolved.

Two options: rename it to "Release Manager's Guide" and add a separate
governance page, or keep the title and add the missing sections. Either way the
project currently offers no answer to "who decides, and how do I earn a say" —
which is the question that most affects whether newcomers stay.

### The contributor on-ramp is blunt and has a missing first step

`CONTRIBUTING.md:19-20` opens the contribution path with, in bold:

> **Do NOT leave a message asking if you can work on an issue; start by trying to reproduce it and fix it**

The underlying norm is reasonable — issue-squatting wastes everyone's time —
but it is stated as a bare prohibition, with no reason given, no
`good first issue` pointer, and no gentler alternative anywhere in the file. It
is the first thing a prospective contributor reads.

Worse, the document never tells anyone how to get a working checkout.
`CONTRIBUTING.md` goes straight from PR guidelines to "Running Tests" with
`pytest` commands; the `git clone` + `pip install -e '.[test]'` sequence exists
only in `docs/source/install/install.rst:112-141`, which `CONTRIBUTING.md` does
not link. A newcomer following it top to bottom hits a wall.

Also missing: any style/lint guidance (the file says "we don't follow any style
guide strictly" while `pyproject.toml` carries extensive ruff config that CI
enforces), any "who to ask" or community chat link, and any cross-link to the
`development/` or `coredev/` doc trees. `CONTRIBUTING.md:61` points to a GitHub
wiki for "more detailed information" — the same wiki that
`docs/source/coredev/index.rst` itself flags as "now out of date."

### Terminology and accessibility

Terminology is largely clean. The genuine hits are confined to historical
changelogs and are not worth rewriting retroactively:

| Path:line | Text | Note |
|---|---|---|
| `whatsnew/version3_widget_migration.rst:105` | "install the Javascript to his/her nbextension" | worth fixing; gendered pronoun for a hypothetical user |
| `whatsnew/github-stats-1.0.rst:58`, `github-stats-2.0.rst:807` | "whitelist alphanumeric characters" | historical PR title; leave |
| `whatsnew/github-stats-1.0.rst:540` | "don't blacklist builtin names" | historical PR title; leave |

Every `master` occurrence is either Sphinx's required `master_doc` config key or
a git branch name in a changelog entry — legitimate, no action. No hits for
sanity check, dummy, grandfathered, man-hours, or "guys"; all `kill`/`hang`
uses are genuine technical terms.

Missing alt text, both images in the file:

- `docs/source/development/how_ipython_works.rst:33` — `.. image:: figs/ipy_kernel_and_terminal.png`
- `docs/source/development/how_ipython_works.rst:50` — `.. image:: figs/other_kernels.png`
- also `whatsnew/version3.rst:131` and `whatsnew/version8.rst:1288`

Most whatsnew images *do* carry `:alt:`, so this is inconsistency rather than
policy. Adding `:alt:` to the two `development/` images is a two-line fix.

Minimising language ("simply", "just", "easy") appears throughout
`coredev/index.rst` and `development/*.rst`. Individually harmless — mostly
describing genuinely mechanical steps — but concentrated in the most
jargon-dense, acronym-heavy documents the project has (PEP440, SPEC-0,
`.mailmap`, trusted publishing, milestones, all unglossed). The combination is
what reads as gatekeeping, not any single word.

Worth stating plainly on the credit side: `CONTRIBUTING.md:29` says "Open a
draft PR even with terrible code and ask for advice," and `:40` says "The worst
case is that the PR is closed." That is genuinely good, low-stakes framing. The
problem is not the project's attitude; it is that these two lines are the only
welcoming content in the file.

## 2. Structural problems

### Navigation order buries installation behind the changelog

`docs/source/index.rst:92-105` orders the toctree:

```
self → overview → whatsnew/index → install/index → interactive/index
     → config → development → coredev → api → sphinxext → about
```

A first-time visitor meets 24 release-note and GitHub-stats files before
learning how to install anything. Proposed order: `overview → install →
interactive (tutorial) → config → api → development → coredev → whatsnew →
about`, i.e. task order for the reader, with history and process at the end.

### `development/` vs `coredev/` actively mislead

Both names read as "the dev docs." The actual split is documented only inside
the pages themselves (`development/index.rst:7-11`, `coredev/index.rst:7-9`,
which do cross-link each other properly):

- `development/` — for authors of **third-party tools that use IPython**
- `coredev/` — for **IPython's own** maintainers

Neither is reachable from `CONTRIBUTING.md`, the actual first-contact file for
contributors. Suggest renaming to something self-describing —
`integrating/` (or `extending/`) and `maintainers/` — and linking both from
`CONTRIBUTING.md`.

There is also real duplication here: `development/config.rst` re-explains
profile locations and config-file lookup already covered in `config/intro.rst`,
so user-facing config documentation is split between the main config section and
a section nominally aimed at third-party developers, joined only by one
`seealso` at `config/index.rst:15-16`.

### Vestigial "this moved" stubs should use the redirect mechanism that already exists

Three orphan pages ship as real, crawlable HTML whose entire content is one
sentence saying the feature moved:

- `docs/source/parallel/index.rst` — "IPython.parallel has moved to ipyparallel"
- `docs/source/development/kernels.rst` — moved to Jupyter
- `docs/source/development/messaging.rst` — moved to Jupyter

The two audits disagreed on these — one wanted them deleted as dead weight, the
other kept them as catchers for old inbound links. Both are right, and the repo
already has the answer: `docs/source/sphinx.toml:36-41` uses
`html_additional_pages` with `_templates/notebook_redirect.html` to redirect
four retired notebook URLs. Converting these three to the same pattern
preserves the old URLs *and* removes a top-level `parallel/` directory that no
longer corresponds to anything.

### Topic ownership is unclear; content is triplicated

System-shell access and aliases are documented three times — in
`interactive/tutorial.rst`, again as the whole of `interactive/shell.rst`, and
again in `interactive/reference.rst`. Configuration is covered in the tutorial,
in `config/`, and split again across `config/intro.rst` and
`development/config.rst`. No page is canonical, so the copies drift.

Each topic should have one owning page; the others should link to it.

### Feature lists are maintained in three hand-copied places

`README.rst:48-68`, `long_description.rst:8-34`, and
`docs/source/index.rst:28-55` carry the same ~11 bullets, already diverging in
wording (e.g. "related to IPython or the operating system" vs "related either to
IPython or the operating system"). `overview.rst:59-189` has a fourth, much
longer version with no cross-reference from the short ones.

Pick one canonical list — `overview.rst`'s is the most complete — and have the
others `.. include::` or link to it.

### Other structural notes

- `sphinxext.rst` sits as a top-level nav item next to `about` and `api`, but
  documents the Sphinx directive used to *build* these docs — tooling
  documentation in the user's navigation. It is also the only top-level entry
  that is a loose `.rst` rather than a `directory/index.rst`.
- `interactive/` is titled "Tutorial" (`interactive/index.rst:2`) yet contains
  the 40KB reference manual (`reference.rst`). Tutorial and reference are
  different documents for different moments and should be separable in the nav.
- `config/details.rst` (17KB) is a catch-all — LLM suggestions, keybindings,
  terminal colours, themes, pager, editor — under the generic title "Specific
  configuration details." It will keep growing unless split.
- `coredev/index.rst` closes with an "Old Documentation" section pointing at a
  wiki it describes as out of date. Delete the section.
- Include-only pages (`api/index.rst`, `config/options/index.rst`,
  `config/shortcuts/index.rst`, `interactive/magics.rst`) break silently if a
  contributor builds without running `tools/autogen_*.py` first. The
  prerequisite is wired into the Makefile and `conf.py`'s `ON_RTD` branch but is
  documented in none of the affected files.
- `conf.py:12` imports `sphinx_rtd_theme` but the theme name comes from
  `sphinx.toml` — dead import.

## 3. Content freshness

Most live pages are accurate. Version claims (`overview.rst:236`,
`install/install.rst:7`) both say 3.11 and match `pyproject.toml`. Install
instructions use only `pip`/editable installs — no `easy_install` or
`setup.py install` survivals. Every live mention of notebook, qtconsole,
nbconvert, and ipyparallel correctly attributes them to separate projects. No
`IPython.html` or `IPython.parallel` import examples remain. All of
`links.txt` is HTTPS and live.

The exceptions:

| Path | Problem | Verdict |
|---|---|---|
| `docs/man/ipython.1:5` | `.TH IPYTHON 1 "July 15, 2011"` | redate |
| `docs/man/ipython.1:34-37` | description advertises "web notebook, Qt console" as bundled features | rewrite — the only live page still misattributing these |
| `docs/source/overview.rst:15-24` | "IPython has **three** main components", third being "An architecture for interactive parallel computing now part of the `ipyparallel` package" | rewrite — contradicts `long_description.rst:2`, which lists two |
| `docs/source/sphinxext.rst:156-160` | example fetches `http://ichart.finance.yahoo.com/table.csv` — endpoint shut down years ago, and plain HTTP | replace example |
| `docs/source/sphinxext.rst:10` | "in 'beta' and currently under active development" | verify; reads frozen |
| `docs/source/interactive/shell.rst:82-130` | example transcript uses `[Q:doc/examples]|2>` prompts and file listings dated Sep/Dec 2006 | re-record |
| `long_description.rst:37` | `http://github.com/ipython` — non-HTTPS, and the org page rather than the repo | fix |

`interactive/index.rst:9-13` carries the maintainers' own disclaimer that "Some
part of this documentation are more than a decade old so might be out of date."
Spot checks of `tutorial.rst`, `tips.rst`, and `python-ipython-diff.rst` found
no Python-2-era leakage, so the disclaimer is more pessimistic than the content
warrants — but `shell.rst` confirms it is not baseless.

**`whatsnew/` needs no action.** It is 904K and dominates the tree by size, and
it holds essentially every `http://` link, defunct-service reference, and
2006-era screenshot in the repo — but it is a deliberate historical archive with
a working fragment→release pipeline (`whatsnew/pr/*.rst` → `development.rst` →
`versionN.rst`). Dated prose is the point. The only change worth making is
moving it later in the nav (above) and fixing the one pronoun.

## 4. First-contact surfaces

`README.rst` is the most-viewed file the project has, on both GitHub and PyPI,
and it contains **no usage example, no `pip install ipython`, and no image** —
despite `docs/source/_images/ipython-6-screenshot.png` existing in-repo and
being used by `docs/source/index.rst:20-22`. A visitor cannot see what IPython
does without leaving the page. It also links to none of `CONTRIBUTING.md`,
`SECURITY.md`, `COPYING.rst`, or any community venue.

It never states plainly what IPython *is* today. `README.rst:42-44` says the
Notebook and Qt console "are now parts of Jupyter" and leaves the reader to
infer the rest. One sentence would fix it: IPython is the enhanced Python shell
and the kernel that powers Jupyter; the notebook interface lives in Jupyter.

The "Development and Instant running" heading mixes contributor docs with
"how to run without installing" — two audiences, one ambiguous heading. The
git-blame-ignore-revs section (`README.rst:102-116`) is contributor trivia that
belongs in `CONTRIBUTING.md`.

`docs/source/install/` handles the happy path in one line and handles nothing
else. There is no Windows/macOS/Linux guidance despite `overview.rst:238-243`
claiming all three, no troubleshooting, and no recommendation to create a
virtual environment before `pip install ipython` — a beginner following the docs
literally installs into system Python. `install.rst:29-31` punts platform
nuance to `https://ipython.org/install`, so the in-repo docs alone do not answer
the question for a beginner who hits a snag.

## Proposed order of work

1. Add `CODE_OF_CONDUCT.md`; link from README, CONTRIBUTING, docs landing page.
2. Replace the agent-emoji line in `CONTRIBUTING.md` with a neutral disclosure convention.
3. Add the missing dev-environment setup step to `CONTRIBUTING.md`; add lint/style guidance, a `good first issue` pointer, a reason for the no-squatting rule, and links to the two doc trees. Drop the stale wiki link.
4. Fix `overview.rst`'s three-components framing and the `docs/man/ipython.1` notebook/Qt claim — the two places the project still describes itself as something it stopped being.
5. Give `README.rst` a quickstart, the screenshot, a one-line "what this is today", and links to CoC/CONTRIBUTING/SECURITY/licence.
6. Reorder the `index.rst` toctree; move `whatsnew` to the end.
7. Add `:alt:` to the `development/` images; fix the `his/her` pronoun.
8. Rename `development/` and `coredev/`; convert the three orphan stubs to `html_additional_pages` redirects.
9. Deduplicate the feature lists and the config/system-shell content onto canonical pages.
10. Re-record `interactive/shell.rst`; replace the Yahoo Finance example; add virtualenv and per-platform notes to `install/`.

Items 1–5 are small, independent, and carry most of the benefit. Items 8–10 are
larger and want their own PRs.
