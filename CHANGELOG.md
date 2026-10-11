# Changelog

The history of the DSBook framework. One entry per version recorded with `npm run framework -- --release <version>`; a project built on DSBook keeps its own changelog and notes there which framework version it adopted. See [docs/framework.md](docs/framework.md).

Format: version · date (YYYY-MM-DD) · summary.

## 2.1.0 · 2026-10-11
Dos scripts para el traspaso entre Claude Code y Claude Design. `npm run design-diff` compara un proyecto de Claude Design (una carpeta con sus archivos, o solo el listado de rutas de `list_files`) con el repositorio y lo agrupa por tipo: diseño, estructura, framework y documentación del proyecto. `npm run handoff-note` escribe `handoff-last.md` en la raíz, el aviso con lo que cambia respecto a lo último publicado, los IDs afectados y qué debe comprobar Claude Design; se ejecuta antes del commit y por eso el hash va en el prompt posterior. `docs/handoff.md` fija el orden (`design-diff` al traer y antes del commit, `handoff-note` antes del commit, prompt tras el push) y una plantilla de prompt. Los scripts no se conectan a Claude Design: el agente lo hace por MCP y les entrega el listado o la carpeta. Nueva prueba: `scripts/handoff.test.mjs`.

## 2.0.1 · 2026-10-11
Las guías describen el ciclo de trabajo completo: GitHub como única fuente de verdad, Claude Design que construye, Claude Code que se conecta por MCP al proyecto de Claude Design y trae los cambios, implementación local, validación de la persona responsable, push a GitHub y confirmación de Claude Design. Se añade la regla de accesos: un agente al que le falta git o la conexión a Claude Design se detiene y lo pide. Cada proyecto registra su repositorio y su proyecto de Claude Design en la sección "Conexiones del proyecto" de su `CLAUDE.md`. Tras cada push, Claude Code escribe el aviso para Claude Design (commit, versión, lo cambiado agrupado por tipo —diseño, contenido de Claude Code, framework, estructura— y qué debe comprobar), descrito en `docs/handoff.md`. La prueba de `check` con página fixture parte ahora de un proyecto vacío (seed), para no depender de las páginas ni de los estados del proyecto.

## 2.0.0 · 2026-10-11
El framework pasa a castellano (MAJOR: `check` exige `lang="es"` en las páginas `.dc.html`). Los textos descriptivos, las guías de `docs/`, la interfaz del contenedor, los mensajes de los scripts y de sus pruebas, el seed (guía restaurada del castellano original, con IDs y rutas sin cambios) y la ficha de componente se escriben en castellano; los términos y las etiquetas (Ready, task, review, token, Content, Record…) se quedan en inglés según el nuevo `docs/glossary.md`. `init` usa `es` como idioma por defecto. Las entradas anteriores de este changelog se quedan en inglés. Los proyectos con páginas en otro idioma tienen que adaptarlas antes de adoptar esta versión.

## 1.2.0 · 2026-10-10
The home page becomes a record: Content, Info (empty, reserved for a guide to the project and the framework) and Record views at `#/home/<view>`. Record shows the project record (`record`: owner, notes, tasks, optional in `project.json`) and, read-only and unfiltered, the tasks of every section. Tasks have one shape and one validation everywhere. A Not applicable section cannot have tasks: adding one means reopening it (guides for Claude Design and Claude Code). `home` and `framework` are reserved category IDs. The Sections menu cascade opens from pointer events and does not depend on hover-capability media queries (Firefox reported none on a touch-capable PC and showed the inline accordion); a browser test emulates touch.

## 1.1.1 · 2026-10-10
The progress test no longer assumes a project without Not applicable sections, so it keeps passing once a project triages its statuses.

## 1.1.0 · 2026-10-10
The DSB mark opens a new Framework page (`#/framework`): the framework version, release date and source, the project's repository and the latest commit on GitHub, each with a manual *Check for updates* button. `dsbook.json` gains `released` and `repository`; `data/project.json` gains an optional `repository`; `framework --release` takes `--repository`. Agents check the remote at the start of a session. The page is compact (the two blocks side by side, small buttons) and puts two short texts (what DSBook is, how it works with Claude Design, Claude Code and GitHub) on the left and the framework and project blocks on the right. It is titled "DSBook framework" and, while the copy has not been started as a project, shows only the framework block. `npm start` runs `scripts/serve.mjs`, which takes the first free port from 8000. The logo is a vector (`assets/_framework/dsbook-logo.svg`, Roboto Bold and Medium as outlines) used in the rail and as the tab icon.

## 1.0.0 · 2026-10-10
First version of the framework as its own repository, extracted from the first project that used it (its pilots, up to its version 0.15.0). It contains:
- The container: icon rail with a status filter and a sections menu, a sticky header (breadcrumb, status, view tabs, search, previous/next), a summary home, category lists and three views per subsection (Content, Info, Record), with hash routes. Read-only.
- The data contract: `schemaVersion` 2, a project-owned guide (14 categories, 132 subsections) and record (status, owner, notes, tasks, review), the seed and `npm run init`.
- The `.dc.html` page model with a vendored React runtime and generated `support.js`.
- The scripts: `check`, `init`, `framework`, and tests (logic, check rules, init, framework manifest, a browser smoke test).
- The permanent guides in English: for Claude Design, for Claude Code, handoff, data contract, review criteria, page style and scripts.
- The framework manifest `dsbook.json` and the versioning policy.
- A CC BY-NC 4.0 license.
