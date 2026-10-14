# Changelog

The history of the DSBook framework. One entry per version recorded with `npm run framework -- --release <version>`; a project built on DSBook keeps its own changelog and notes there which framework version it adopted. See [docs/framework.md](docs/framework.md).

Format: version · date (YYYY-MM-DD) · summary.

## 2.6.1 · 2026-10-15
Las pruebas de `handoff-protection.test.mjs` excluyen `design-baseline.json` de la copia de trabajo, de modo que parten de un registro vacío. Con descartes ya registrados en un proyecto, dos pruebas que cuentan los descartes fallaban; ahora no dependen del estado del proyecto.

## 2.6.0 · 2026-10-14
`design-diff --record --discard` admite varias rutas: se repite el indicador (`--discard a --discard b --reason "…"`) y el motivo vale para todas. Antes solo aceptaba una y, como `--record` exige tener todo resuelto, no se podía descartar más de un archivo de Claude Design. Prueba nueva en `handoff-protection.test.mjs`; `docs/scripts.md` y el skill `dsbook-handoff` lo reflejan.

## 2.5.0 · 2026-10-13
Los botones Anterior / Siguiente de la cabecera de una sección se mueven solo entre las subsecciones que pasan los filtros de estado y la búsqueda (con filtros activos saltan las que no coinciden, aunque la sección abierta no coincida). `neighbors(guide,id,allowed)` en `model.js` acepta un conjunto opcional de IDs y la cabecera se actualiza al cambiar un filtro. Pruebas nuevas en `model.test.mjs` y `container.test.mjs`; `docs/quality.md` lo refleja.

## 2.4.0 · 2026-10-13
La vista por defecto de la home es la **Lista** (todas las categorías con sus subsecciones, contraíbles) en lugar de las tarjetas: `let homeLayout='list'` en `app.js`. El selector Lista / Tarjetas sigue ahí y la elección se mantiene mientras se navega. La prueba de navegador comprueba el nuevo valor por defecto y que el filtro de estado recorta la home en cualquiera de las dos vistas; `README.md` y `docs/quality.md` lo reflejan.

## 2.3.2 · 2026-10-13
Las pruebas del framework (`model`, `about`, `home` y `check`) dejan de depender de los datos del proyecto y usan el seed: añadir una categoría o una sección en un proyecto ya no las rompe. La prueba del proyecto real (`home.test.mjs`) sigue validando `data/`. Detectado al integrar la primera categoría nueva creada en Claude Design.

## 2.3.1 · 2026-10-12
`handoff-note` ya no etiqueta como «coincide con Claude Design» lo que la comparación no cubre (`scripts/`, `design-baseline.json`, `handoff-last.md`): no se sabe si coincide, así que no lleva etiqueta.

## 2.3.0 · 2026-10-12
Dos cambios de diseño del contenedor. La home tiene un selector de vista **Tarjetas / Lista** a la derecha de «N subsecciones en M categorías»: la lista muestra todas las categorías con sus subsecciones (respeta filtros y búsqueda), cada categoría se contrae con un chevron y hay un «Contraer todo / Expandir todo»; la elección se mantiene mientras se navega. Las vistas **Content / Info / Record** pasan de parecer botones a pestañas: texto sin caja, una línea bajo la cabecera y un indicador bajo la activa (siguen siendo enlaces, con `aria-current`). La prueba de navegador cubre el selector, la lista y el indicador de pestaña.

## 2.2.2 · 2026-10-12
`design-diff` deja de contar como trabajo de Claude Design los dos archivos que genera Claude Code y que Claude Design lee del repositorio (`design-baseline.json` y `handoff-last.md`): su copia siempre va un envío por detrás y se señalaba como «nuevo en Claude Design». Detectado en la primera sincronización real.

## 2.2.1 · 2026-10-12
`handoff-note` acorta la lista de IDs afectados cuando son más de 25 (por ejemplo tras una traducción) en lugar de listarlos todos.

## 2.2.0 · 2026-10-11
Protección del trabajo de Claude Design y menos lectura por MCP. `npm run design-diff -- --request` imprime un prompt que pide a Claude Design un `design-manifest.json` con el SHA-256 de todos sus archivos (con un nonce); `--manifest` lo compara **a tres bandas** (Claude Design ahora, el repositorio ahora y `design-baseline.json`, lo último integrado) y señala lo que Claude Design cambió y el repo no tiene (sin integrar), lo nuevo y lo que hay que confirmar; solo esos archivos se leen después. `--record` registra la integración y exige integrar, confirmar o descartar con motivo. Los SVG se comparan en forma canónica (Claude Design los reserializa al importarlos). `npm run hooks:install` activa un hook de git que bloquea el push mientras haya trabajo de Claude Design sin integrar o la comparación tenga más de 24 h; `handoff-note` se niega a avisar en ese caso. Claude Design recibe la instrucción de no sobrescribir su trabajo sin integrar (copia en `_backup/`). Nuevo skill `.claude/skills/dsbook-handoff/` con el procedimiento completo; `.claude/skills/` forma parte del framework. Nuevos: `scripts/handoff-protection.test.mjs`, `pre-push-check.mjs`, `hooks-install.mjs` y `hooks/pre-push`.

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
