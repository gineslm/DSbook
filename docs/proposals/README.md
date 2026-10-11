# Propuestas y su registro

Una propuesta es un cambio acordado en la **forma de trabajar** (contenedor, contrato, guías, scripts), no en el contenido del Design System. Se redacta en Claude Design o con la persona responsable, se trae a este directorio y la aplica Claude Code. No sustituye a `CHANGELOG.md` y no es un informe de sesión.

## Flujo
1. La propuesta se guarda como `docs/proposals/<name>.md` con una decisión por sección (`D1`, `D2`…), su motivo y sus cambios.
2. Claude Code las aplica por bloques, con validación (`npm run check`, `npm test`) y revisión por la persona responsable.
3. Cuando termina cada decisión, su **estado** y lo que realmente se hizo se escriben en su propia sección, incluidas las correcciones a la premisa original.
4. La tabla de abajo recoge el estado de todas. Las descartadas se conservan con su motivo.
5. Una propuesta que cambia el framework termina con una versión nueva del framework ([../framework.md](../framework.md)).

## Estados
- **Applied**: hecha y validada.
- **Partial**: hecha en parte; lo que falta está en la nota.
- **Discarded**: la persona responsable decidió no aplicarla.
- **Approved**: decisión confirmada por la persona responsable, aún no aplicada.
- **Pending**: sin decidir, o no aplicada por falta de una decisión.

## Registro
| Propuesta | Decisión | Estado | Versión | Nota |
| --- | --- | --- | --- | --- |
| | | | | Aún no hay propuestas. |
