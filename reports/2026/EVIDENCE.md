# SCA-001 — Registro y preservación de evidencias

| ID | Evidencia | Estado |
|---|---|---|
| E01-E04 | Instalación inicial y `npm audit` | Salidas aportadas en conversación; capturas originales pendientes de anexar |
| E05-E07 | `check`, `lint`, `build` | Ejecutados satisfactoriamente |
| E08 | `npm ls minimatch`, `npm explain minimatch` | Ejecutados; dependencia vulnerable identificada |
| E09 | `npm audit fix --dry-run` | Ejecutado; 7 hallazgos persistentes |
| E10-E12 | Consultas al registro npm | Ejecutadas |
| E13 | `npm outdated` | Ejecutado |
| E14 | `audit-antes.json` | No recibido/verificado |
| E15 | Prueba de remediación y auditoría posterior | No ejecutada |
| E16 | Regresión funcional en navegador | No documentada |

**No subir archivos que revelen rutas privadas, nombres de usuario locales, tokens, claves o información de terceros.** Conservar registros originales de manera privada y publicar únicamente copias saneadas.

Para completar el caso: adjuntar salidas antes y después, versión de Node, diff de lockfile, validaciones de CI y acta de cierre con fecha.
