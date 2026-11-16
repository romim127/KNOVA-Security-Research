# SCA-001 — Registro y preservación de evidencias

| ID | Evidencia | Estado |
|---|---|---|
| E01-E03 | Instalación inicial y `npm audit` | Salidas aportadas en conversación; capturas originales pendientes de anexar |
| E04-E06 | `check`, `lint`, `build` | Ejecutados satisfactoriamente |
| E07 | `npm ls minimatch`, `npm explain minimatch` | Ejecutados; dependencia vulnerable identificada |
| E08 | `npm audit fix --dry-run` | Ejecutado; 7 hallazgos persistentes |
| E09-E11 | Consultas al registro npm | Ejecutadas |
| E12 | `npm outdated` | Ejecutado |
| E13 | `audit-antes.json` | No recibido/verificado |
| E14 | Prueba de remediación y auditoría posterior | No ejecutada |
| E15 | Regresión funcional en entorno consumidor | No documentada |

**No subir archivos que revelen rutas privadas, nombres de usuario locales, tokens, claves o información de terceros.** Conservar registros originales de manera privada y publicar únicamente copias saneadas.

Para completar el caso: adjuntar salidas antes y después, versión de Node, diff de lockfile, validaciones de CI y acta de cierre con fecha.
