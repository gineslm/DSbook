# Changelog

The history of the DSBook framework. One entry per version recorded with `npm run framework -- --release <version>`; a project built on DSBook keeps its own changelog and notes there which framework version it adopted. See [docs/framework.md](docs/framework.md).

Format: version · date (YYYY-MM-DD) · summary.

## 1.1.0 · 2026-10-10
The DSB mark opens a new Framework page (`#/framework`): the framework version, release date and source, the project's repository and the latest commit on GitHub, each with a manual *Check for updates* button. `dsbook.json` gains `released` and `repository`; `data/project.json` gains an optional `repository`; `framework --release` takes `--repository`. Agents check the remote at the start of a session. The page is compact (the two blocks side by side, small buttons) and puts two short texts (what DSBook is, how it works with Claude Design, Claude Code and GitHub) on the left and the framework and project blocks on the right. It is titled "DSBook framework".

## 1.0.0 · 2026-10-10
First version of the framework as its own repository, extracted from the first project that used it (its pilots, up to its version 0.15.0). It contains:
- The container: icon rail with a status filter and a sections menu, a sticky header (breadcrumb, status, view tabs, search, previous/next), a summary home, category lists and three views per subsection (Content, Info, Record), with hash routes. Read-only.
- The data contract: `schemaVersion` 2, a project-owned guide (14 categories, 132 subsections) and record (status, owner, notes, tasks, review), the seed and `npm run init`.
- The `.dc.html` page model with a vendored React runtime and generated `support.js`.
- The scripts: `check`, `init`, `framework`, and tests (logic, check rules, init, framework manifest, a browser smoke test).
- The permanent guides in English: for Claude Design, for Claude Code, handoff, data contract, review criteria, page style and scripts.
- The framework manifest `dsbook.json` and the versioning policy.
- A CC BY-NC 4.0 license.
