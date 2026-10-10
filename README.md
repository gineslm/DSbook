# DSBook

DSBook is a workspace to **define, document and track a Design System**. It gives a team a guide of what a complete Design System contains (14 categories, 132 subsections), a place to develop each subsection as a page, and a matrix that shows at a glance what is ready, in process, pending or not applicable.

It is a framework with no content of its own: each copy of this repository becomes one Design System project. Everything is plain HTML, CSS and JavaScript; it needs no build step, no backend and no accounts.

## Contents
- [Goal](#goal)
- [How it works](#how-it-works)
- [Requirements and installation](#requirements-and-installation)
- [Start a project](#start-a-project)
- [Day-to-day workflow](#day-to-day-workflow)
- [Statuses](#statuses)
- [The container](#the-container)
- [Checks](#checks)
- [Repository map](#repository-map)
- [Framework versions](#framework-versions)
- [Known limits](#known-limits)
- [License](#license)

## Goal
A Design System is easy to start and hard to finish: scattered decisions, pages nobody reviews, no view of what is missing. DSBook gives it a fixed skeleton:
- **A guide** that says, for every subsection, what to define and what the acceptance criteria are.
- **A page per subsection**, developed as a real document (the decision, its rationale, examples, tokens, rules).
- **A record per subsection**: status, owner, notes, tasks and review, so nothing is "ready" without evidence.
- **A read-only container** that shows all of it with progress, filters and navigation.

## How it works
Three roles share one repository, which is the source of truth:

| Role | What it does |
| --- | --- |
| **Claude Design** | The design and editing space. It writes the pages (`sections/**/*.dc.html`), the notes, tasks and statuses in `data/project.json`, and may edit the guide text or add subsections in `data/guide.json`. |
| **Claude Code** | Integrates what Claude Design produces: reviews differences, runs the checks, commits, and keeps the framework and its documentation consistent. |
| **The responsible person** | Decides, reviews and approves. Nothing becomes Ready (R) without a recorded review. |

The container (the web interface) only **shows** the state of the repository; it never modifies it. Everything is edited in Claude Design and integrated through Git. The rules for each role are in [CLAUDE.md](CLAUDE.md) and the guides in `docs/`.

Three ideas to keep in mind:
- **Framework and project.** The container, the scripts, the seed and the guides in `docs/` are the *framework*, shared by every project. `data/`, `sections/` and `assets/<category>/` are the *project*. See [docs/framework.md](docs/framework.md).
- **Seed.** `seed/` holds the initial catalog and an all-Pending project. A new project is born from it and from then on owns its data.
- **Contract.** Identities, statuses and paths follow a strict [data contract](docs/data-contract.md) that `npm run check` enforces.

## Requirements and installation
- **Node.js 18 or later**, to run the checks and the scripts. There are no packages to install.
- **Python 3** (or any static file server) to serve the container locally.
- **Git**.
- Optional: Chrome or Chromium and Node 22+, for the browser smoke test (it is skipped without them).

```sh
git clone https://github.com/gineslm/DSbook.git my-design-system
cd my-design-system
```

## Start a project
1. Copy or clone this repository into a new folder and **remove its Git history** if you want the project to start clean (delete `.git`, then `git init`).
2. Create the project's data from the seed:
   ```sh
   npm run init -- --id my-ds --name "My Design System"
   ```
   `--id` is a slug that identifies the project; `--language` defaults to `en`. This writes `data/guide.json` and `data/project.json` (everything Pending).
3. Adapt `README.md`, `CLAUDE.md`, `CHANGELOG.md` and `LICENSE` to the project: they describe the project, not the framework.
4. Check and serve:
   ```sh
   npm run check
   npm test
   python3 -m http.server 8000 --bind 127.0.0.1
   ```
   Open http://localhost:8000. Do not open `index.html` directly: loading the JSON files requires HTTP.
5. Commit and connect your remote.

More detail in [docs/template.md](docs/template.md).

## Day-to-day workflow
1. **Pick a subsection** in the container (or in `data/guide.json`). Its *Info* view says what to define.
2. **Develop it in Claude Design.** The page goes to the subsection's `suggestedPath` (`sections/<category>/<slug>.dc.html`), its images to `assets/<category>/<slug>/`.
3. **Record it.** In `data/project.json` set the status (I while there is work or review pending), the owner, the decisions in `notes` and non-blocking work in `tasks`.
4. **Hand it over.** Claude Design follows the [handoff guide](docs/handoff.md): it checks that its base matches the repository, lists what it changed and what it could not verify.
5. **Integrate with Claude Code.** Review the differences, run `npm run check` and `npm test`, commit. The changelog records each integration.
6. **Review and approve.** The responsible person reviews against [docs/quality.md](docs/quality.md). Only then a subsection moves to R, with the person, date and evidence recorded.

Guides: [for Claude Design](docs/claude-design.md) · [for Claude Code](docs/claude-code.md) · [page style](docs/page-style.md) · [review criteria](docs/quality.md).

## Statuses
| Status | Meaning |
| --- | --- |
| R · Ready | Reviewed development, with person, date and evidence recorded. |
| I · In process | Work or review in progress. |
| P · Pending | Development pending; initial status. |
| N · Not applicable | Out of scope, with an explicit justification. |

An existing page does not imply R. **Tasks** (`pending` or `future`) are non-blocking and never prevent Ready; work that blocks keeps the subsection in I.

## The container
A narrow rail with the **DSB** mark (home) and two dropdowns: **status** filter and **sections** menu (hover a category to see its subsections). A sticky header shows the breadcrumb and the status on the left, and on the right the view tabs, a search field that unfolds from a magnifier, and previous/next buttons.

Each subsection has its own address, `#/<category>.<slug>`, and three views: **Content** (its page), **Info** (the guide: what to define and the acceptance criteria) and **Record** (status, owner, notes, tasks and review). The home page shows two bars: **Progress** (Ready over applicable) and **Complexity** (applicable over total). A subsection in I or R with a page opens on Content; any other opens on Info.

Pages are shown in an isolated iframe (`sandbox="allow-scripts"`). The container keeps no drafts and imports or exports nothing.

## Checks
```sh
npm run check       # contract, pages, vendored runtime and framework version
npm test            # logic, check rules, init, framework manifest and a browser smoke test
npm run framework   # framework version report (also --release and --compare)
```
They need no dependencies. What each one verifies and how to read an error is in [docs/scripts.md](docs/scripts.md). They do not certify accessibility or replace visual review.

## Repository map
| Path | What it is |
| --- | --- |
| `index.html`, `styles.css`, `app.js`, `model.js` | The container and its pure logic. |
| `data/guide.json`, `data/project.json` | The project's catalog and its record. Project-owned. |
| `seed/` | The initial catalog and an all-Pending project. |
| `sections/` | The pages that develop the Design System. Empty in a fresh copy. |
| `assets/` | Page resources; `assets/_runtime/` is the vendored React runtime. |
| `docs/` | Permanent guides. `docs/proposals/` records agreed changes to the way of working. |
| `scripts/` | Checks, tests, `init` and the framework manifest tools. |
| `dsbook.json` | The framework version this repository carries and the hash of each framework file. |
| `CLAUDE.md` | The entry point for agents. |

## Framework versions
`dsbook.json` records the framework version, and `npm run check` fails if a framework file changes without a new version being recorded. This keeps every repository built on DSBook comparable: `npm run framework -- --compare <folder>` says whether two of them differ and in which files. The versioning rules and the workflow to port a change are in [docs/framework.md](docs/framework.md); the history is in [CHANGELOG.md](CHANGELOG.md).

## Known limits
- External pages may prevent embedding in an iframe; there is always a link to open them separately.
- Iframes run with scripts but without access to the container. Content that needs cookies, storage or extra capabilities must be opened separately or adapted.
- The container does not edit pages: that happens in Claude Design or in code.
- R means a recorded review according to the project's criteria, not a conformance certification.
- Markdown guides are read in the editor or on GitHub; they are not converted to HTML.

## License
Creative Commons Attribution-NonCommercial 4.0 International ([LICENSE](LICENSE)). You may use, adapt and share DSBook giving credit to the original and linking the license; you may not use it commercially. The vendored React files keep their MIT license ([assets/_runtime/README.md](assets/_runtime/README.md)).

Credit: *DSBook by Gines Lopez, https://github.com/gineslm/DSbook*.
