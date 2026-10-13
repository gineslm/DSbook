# DSBook

DSBook es un workspace para **definir, documentar y seguir un Design System**. Da a un equipo una guía de lo que contiene un Design System completo (14 categorías, 132 subsecciones), un lugar donde desarrollar cada subsección como una página y una matriz que muestra de un vistazo qué está Ready, In process, Pending o Not applicable.

Es un framework sin contenido propio: cada copia de este repositorio se convierte en un proyecto de Design System. Todo es HTML, CSS y JavaScript sin más: no necesita paso de build, ni backend, ni cuentas.

## Contenido
- [Objetivo](#objetivo)
- [Cómo funciona](#cómo-funciona)
- [Requisitos e instalación](#requisitos-e-instalación)
- [Empezar un proyecto](#empezar-un-proyecto)
- [Flujo de trabajo diario](#flujo-de-trabajo-diario)
- [Estados](#estados)
- [El contenedor](#el-contenedor)
- [Comprobaciones](#comprobaciones)
- [Mapa del repositorio](#mapa-del-repositorio)
- [Versiones del framework](#versiones-del-framework)
- [Límites conocidos](#límites-conocidos)
- [Licencia](#licencia)

## Objetivo
Un Design System es fácil de empezar y difícil de terminar: decisiones dispersas, páginas que nadie revisa, ninguna visión de lo que falta. DSBook le da un esqueleto fijo:
- **Una guía** que dice, para cada subsección, qué definir y cuáles son los criterios de aceptación.
- **Una página por subsección**, desarrollada como un documento real (la decisión, su razonamiento, ejemplos, tokens, reglas).
- **Un record por subsección**: estado, owner, notes, tasks y review, de modo que nada está "Ready" sin evidencia.
- **Un contenedor de solo lectura** que lo muestra todo con progreso, filtros y navegación.

## Cómo funciona
Tres roles comparten un repositorio, que es la fuente de verdad:

| Rol | Qué hace |
| --- | --- |
| **Claude Design** | El espacio de diseño y edición. Escribe las páginas (`sections/**/*.dc.html`), las notes, tasks y estados de `data/project.json`, y puede editar el texto de la guía o añadir subsecciones en `data/guide.json`. |
| **Claude Code** | Integra lo que produce Claude Design: revisa las diferencias, ejecuta las comprobaciones, hace los commits y mantiene coherentes el framework y su documentación. |
| **La persona responsable** | Decide, revisa y aprueba. Nada pasa a Ready (R) sin una review registrada. |

El contenedor (la interfaz web) solo **muestra** el estado del repositorio; nunca lo modifica. Todo se edita en Claude Design y se integra mediante Git. Las reglas de cada rol están en [CLAUDE.md](CLAUDE.md) y las guías en `docs/`.

Tres ideas que conviene tener presentes:
- **Framework y proyecto.** El contenedor, los scripts, el seed y las guías de `docs/` son el *framework*, compartido por todos los proyectos. `data/`, `sections/` y `assets/<category>/` son el *proyecto*. Ver [docs/framework.md](docs/framework.md).
- **Seed.** `seed/` contiene el catálogo inicial y un proyecto con todo en Pending. Un proyecto nuevo nace de él y desde entonces es dueño de sus datos.
- **Contrato.** Las identidades, estados y rutas siguen un [contrato de datos](docs/data-contract.md) estricto que `npm run check` hace cumplir.
- **Idioma.** El repositorio se escribe en castellano; los términos y las etiquetas se quedan en inglés ([docs/glossary.md](docs/glossary.md)).

## Requisitos e instalación
- **Node.js 18 o posterior**, para ejecutar las comprobaciones, los scripts y el servidor local (`npm start`). No hay paquetes que instalar.
- **Git**.
- Opcional: Chrome o Chromium y Node 22+, para la prueba de humo en navegador (se omite sin ellos).

```sh
git clone https://github.com/gineslm/DSbook.git my-design-system
cd my-design-system
```

## Empezar un proyecto
1. Copia o clona este repositorio en una carpeta nueva y **elimina su historial de Git** si quieres que el proyecto empiece limpio (borra `.git` y ejecuta `git init`).
2. Crea los datos del proyecto desde el seed:
   ```sh
   npm run init -- --id my-ds --name "My Design System"
   ```
   `--id` es un slug que identifica el proyecto; `--language` es `es` por defecto. Esto escribe `data/guide.json` y `data/project.json` (todo en Pending).
3. Adapta `README.md`, `CLAUDE.md`, `CHANGELOG.md` y `LICENSE` al proyecto: describen el proyecto, no el framework.
4. Comprueba y sirve:
   ```sh
   npm run check
   npm test
   npm start
   ```
   `npm start` imprime la dirección (el primer puerto libre desde 8000, de modo que varios proyectos pueden correr a la vez). Ábrela. No abras `index.html` directamente: cargar los archivos JSON requiere HTTP.
5. Haz commit y conecta tu remoto.

Más detalle en [docs/template.md](docs/template.md).

## Flujo de trabajo diario
1. **Elige una subsección** en el contenedor (o en `data/guide.json`). Su vista *Info* dice qué definir.
2. **Desarróllala en Claude Design.** La página va al `suggestedPath` de la subsección (`sections/<category>/<slug>.dc.html`) y sus imágenes a `assets/<category>/<slug>/`.
3. **Regístrala.** En `data/project.json` fija el estado (I mientras haya trabajo o review pendiente), el owner, las decisiones en `notes` y el trabajo no bloqueante en `tasks`.
4. **Entrégala.** Claude Design sigue la [guía de handoff](docs/handoff.md): comprueba que su base coincide con el repositorio y lista lo que cambió y lo que no pudo verificar.
5. **Intégrala con Claude Code.** Revisa las diferencias, ejecuta `npm run check` y `npm test` y haz commit. El changelog registra cada integración.
6. **Revisa y aprueba.** La persona responsable revisa según [docs/quality.md](docs/quality.md). Solo entonces una subsección pasa a R, con la persona, la fecha y la evidencia registradas.

Guías: [para Claude Design](docs/claude-design.md) · [para Claude Code](docs/claude-code.md) · [estilo de página](docs/page-style.md) · [criterios de review](docs/quality.md).

## Estados
| Estado | Significado |
| --- | --- |
| R · Ready | Desarrollo revisado, con persona, fecha y evidencia registradas. |
| I · In process | Trabajo o review en curso. |
| P · Pending | Desarrollo pendiente; estado inicial. |
| N · Not applicable | Fuera de alcance, con una justificación explícita. |

Una página existente no implica R. Las **tasks** (`pending` o `future`) no bloquean y nunca impiden Ready; el trabajo que bloquea mantiene la subsección en I.

## El contenedor
Un rail estrecho con el **logo de DSBook** (abre la página Framework; la home es el primer breadcrumb) y dos desplegables: el filtro de **estado** y el menú **Sections** (pasa el puntero por una categoría para ver sus subsecciones). Una cabecera fija muestra el breadcrumb y el estado a la izquierda y, a la derecha, las pestañas de vista, un campo de búsqueda que se despliega desde una lupa y los botones anterior/siguiente.

La home tiene las mismas tres vistas en `#/home/<view>`: **Content** (el resumen), **Info** (vacía por ahora, reservada para una guía del proyecto y del framework) y **Record** (el project record y, en solo lectura, las tasks de todas las secciones). Cada subsección tiene su propia dirección, `#/<category>.<slug>`, y tres vistas: **Content** (su página), **Info** (la guía: qué definir y los criterios de aceptación) y **Record** (estado, owner, notes, tasks y review). La home muestra dos barras: **Progress** (Ready sobre aplicables) y **Complexity** (aplicables sobre el total). Una subsección en I o R con página abre en Content; cualquier otra abre en Info. Las tres vistas (Content, Info y Record) se presentan como pestañas, con una línea bajo la cabecera y la activa marcada con un indicador. La vista Content de la home se puede ver como **Lista** (por defecto: todas las categorías con sus subsecciones, cada una contraíble) o como **Tarjetas** (una por categoría); el selector está a la derecha de «N subsecciones en M categorías» y la elección se mantiene mientras navegas.

Las páginas se muestran en un iframe aislado (`sandbox="allow-scripts"`). El contenedor no guarda borradores y no importa ni exporta nada.

## Comprobaciones
```sh
npm run check       # contrato, páginas, runtime vendorizado y versión del framework
npm test            # lógica, reglas de check, init, manifiesto del framework y una prueba de humo en navegador
npm run framework   # informe de la versión del framework (también --release y --compare)
npm run design-diff # compara un proyecto de Claude Design con el repositorio y protege su trabajo
npm run hooks:install # bloquea el push si hay trabajo de Claude Design sin integrar
npm run handoff-note # escribe el aviso (handoff-last.md) para Claude Design
```
No necesitan dependencias. Qué verifica cada una y cómo leer un error está en [docs/scripts.md](docs/scripts.md). No certifican la accesibilidad ni sustituyen la revisión visual.

## Mapa del repositorio
| Ruta | Qué es |
| --- | --- |
| `index.html`, `styles.css`, `app.js`, `model.js` | El contenedor y su lógica pura. |
| `data/guide.json`, `data/project.json` | El catálogo del proyecto y su record. Pertenecen al proyecto. |
| `seed/` | El catálogo inicial y un proyecto con todo en Pending. |
| `sections/` | Las páginas que desarrollan el Design System. Vacío en una copia nueva. |
| `assets/` | Recursos de las páginas; `assets/_runtime/` es el runtime vendorizado de React. |
| `docs/` | Guías permanentes. `docs/proposals/` registra los cambios acordados en la forma de trabajar. |
| `scripts/` | Comprobaciones, pruebas, `init` y las herramientas del manifiesto del framework. |
| `dsbook.json` | La versión del framework que lleva este repositorio y el hash de cada archivo del framework. |
| `CLAUDE.md` | El punto de entrada para los agentes. |

## Versiones del framework
El logo de DSBook en el contenedor abre la página **Framework**: la versión del framework y su origen, el repositorio del proyecto, el último commit en GitHub y un botón *Check for updates* en cada bloque (manual; las únicas llamadas de red).

`dsbook.json` registra la versión del framework, y `npm run check` falla si un archivo del framework cambia sin que se registre una versión nueva. Así todos los repositorios construidos sobre DSBook siguen siendo comparables: `npm run framework -- --compare <folder>` dice si dos de ellos difieren y en qué archivos. Las reglas de versionado y el flujo para portar un cambio están en [docs/framework.md](docs/framework.md); el historial está en [CHANGELOG.md](CHANGELOG.md).

## Límites conocidos
- Las páginas externas pueden impedir su incrustación en un iframe; siempre hay un enlace para abrirlas aparte.
- Los iframes se ejecutan con scripts pero sin acceso al contenedor. El contenido que necesite cookies, almacenamiento o capacidades adicionales debe abrirse aparte o adaptarse.
- El contenedor no edita páginas: eso ocurre en Claude Design o en código.
- R significa una review registrada según los criterios del proyecto, no una certificación de conformidad.
- Las guías Markdown se leen en el editor o en GitHub; no se convierten a HTML.

## Licencia
Creative Commons Attribution-NonCommercial 4.0 International ([LICENSE](LICENSE)). Puedes usar, adaptar y compartir DSBook dando crédito al original y enlazando la licencia; no puedes usarlo comercialmente. Los archivos vendorizados de React conservan su licencia MIT ([assets/_runtime/README.md](assets/_runtime/README.md)).

Crédito: *DSBook de Gines Lopez, https://github.com/gineslm/DSbook*.
