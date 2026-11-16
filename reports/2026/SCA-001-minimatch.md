# SCA-001 — Auditoría de dependencia transitiva vulnerable: minimatch

**Fecha de observación:** 2026-11-16  
**Unidad de análisis:** KNOVA | NextGenAI  
**Disciplina:** Software Composition Analysis (SCA) / Third-party risk  
**Estado:** **Hallazgo confirmado en dependencias; remediación pendiente de verificación**  
**Criticidad del aviso:** MODERADA (según npm/GHSA); **prioridad de tratamiento: requiere corrección planificada y seguimiento**.

## 1. Resumen ejecutivo

Durante una instalación de dependencias con npm en un entorno Node.js/TypeScript se identificó una dependencia transitiva afectada por una vulnerabilidad pública. El caso ilustra un riesgo de la cadena de suministro: npm puede instalar correctamente paquetes compatibles con un manifiesto y un archivo de bloqueo, aunque alguna de sus versiones tenga un aviso de seguridad vigente. No implica que todas las instalaciones de npm presenten la misma vulnerabilidad. La ejecución de `npm.cmd install` fue exitosa (120 paquetes añadidos, 121 auditados) y, sin embargo, `npm audit` notificó **7 hallazgos moderados**, relacionados con la cadena de dependencias de `typescript-eslint` y con la biblioteca `minimatch`.

Se confirmó `minimatch@10.2.6` bajo `@typescript-eslint/typescript-estree@8.72.2`. El componente declara `minimatch: ^10.2.2`, rango que excluye la versión `11.0.1` identificada como corregida. Las siete entradas del reporte no equivalen a siete CVE independientes.

**Conclusión ejecutiva:** la vulnerabilidad de una dependencia de desarrollo es real como hallazgo de composición de software, pero **la explotabilidad en un entorno productivo no está determinada, ya que depende de la exposición y de las entradas procesadas**. El tratamiento debe centrarse en actualizar de forma compatible, documentar el riesgo mientras subsista y verificar los resultados.

## 2. Identificación del aviso

| Indicador | Valor |
|---|---|
| Componente | `minimatch` |
| GHSA | [GHSA-g3hv-75wg-m249](https://github.com/advisories/GHSA-g3hv-75wg-m249) |
| CVE | [CVE-2026-118764](https://nvd.nist.gov/vuln/detail/CVE-2026-118764) |
| CWE | CWE-405 — Asymmetric Resource Consumption |
| Severidad reportada | Moderate |
| Componente local afectado | `minimatch@10.2.6` |
| Versión corregida indicada | `11.0.1` |
| Mecanismo | Posible DoS por amplificación de `globParts` al analizar determinados patrones glob |

**Evaluación de alcance:** el análisis de patrones podría consumir recursos de manera desproporcionada bajo ciertas entradas. No se ha reproducido un ataque ni demostrado un punto de entrada remoto a la dependencia afectada. El paquete está instalado como `devDependency`, lo cual exige analizar la exposición de la cadena de herramientas (por ejemplo, procesamiento de entradas no confiables en CI), sin presuponer vulnerabilidad remota del sitio.

## 3. Activo, entorno y límites

- Activo observado: árbol de dependencias npm instalado en un entorno local de desarrollo.
- Sistema operativo: Windows, terminal PowerShell; npm 11.11.0.
- Herramientas: ESLint 9.39.5, TypeScript 5.9.3 y Vite 7.3.8 (según evidencias recibidas).
- La versión numérica de Node.js, el archivo `audit-antes.json` y pruebas de exposición en producción no forman parte de la evidencia disponible.
- Las salidas de terminal de la evaluación inicial sustentan el registro cronológico; faltan exportaciones saneadas de esas evidencias al repositorio.

## 4. Evidencia cronológica y reproducibilidad

| Ref. | Comando / prueba | Resultado observado |
|---|---|---|
| E01 | `npm.cmd -v` | `11.11.0` |
| E02 | `npm.cmd install` | `added 120 packages, and audited 121 packages`; `7 moderate severity vulnerabilities` |
| E03 | `npm.cmd audit` | Aviso `GHSA-g3hv-75wg-m249` en `minimatch`; 7 hallazgos moderados |
| E04 | `npm.cmd run check` | `tsc --noEmit`, sin errores |
| E05 | `npm.cmd run lint` | `eslint .`, sin errores |
| E06 | `npm.cmd run build` | `vite v7.3.8`; compilación correcta; 4 módulos transformados |
| E07 | `npm.cmd ls minimatch` y `npm.cmd explain minimatch` | `minimatch@10.2.6` bajo typescript-estree; también `minimatch@3.1.5` en rama ESLint |
| E08 | `npm.cmd audit fix --dry-run` | `up to date`; permanecen 7 hallazgos; sin aplicar cambios |
| E09 | `npm.cmd view typescript-eslint version` | `8.72.2` |
| E10 | `npm.cmd view @typescript-eslint/typescript-estree version` | `8.72.2` |
| E11 | `npm.cmd view @typescript-eslint/typescript-estree@latest dependencies.minimatch` | `^10.2.2` |
| E12 | `npm.cmd outdated` | Saltos mayores disponibles en ESLint, TypeScript y Vite; no acreditan por sí solos remediación de minimatch |

### Árbol confirmado

```text
proyecto-npm
├── eslint@9.39.5
│   └── minimatch@3.1.5
└── typescript-eslint@8.72.2
    └── @typescript-eslint/typescript-estree@8.72.2
        └── minimatch@10.2.6  <-- versión afectada
```

`@typescript-eslint/typescript-estree@8.72.2` declara `"minimatch": "^10.2.2"`. En SemVer, dicho rango permite desde 10.2.2 y antes de 11.0.0; por tanto, una instalación ordinaria no selecciona automáticamente `minimatch@11.0.1`.

## 5. Análisis de causa raíz

El problema detectado no se origina en la versión del ejecutable npm, sino en la **resolución de dependencias transitivas**. El árbol analizado incorpora herramientas de desarrollo que imponen rangos de compatibilidad; el rango declarado por `typescript-estree` mantiene instalada una versión afectada de `minimatch`.

La simulación `audit fix --dry-run` conservó los siete hallazgos. No se debe interpretar esta simulación como prueba de que una corrección real será imposible.

El issue público [typescript-eslint#13125](https://github.com/typescript-eslint/typescript-eslint/issues/13125) documenta esta incompatibilidad de rango y su discusión de mantenimiento. Un cambio fusionado en el repositorio del mantenedor no equivale necesariamente a una publicación disponible en npm para los consumidores.

## 6. Riesgo, impacto y prioridad de respuesta

**Confirmado:** instalación de versión afectada de una biblioteca, advertencia reproducible y dependencia que impide adoptar automáticamente la primera versión corregida.

**Potencial:** agotamiento de recursos durante la evaluación de determinados patrones glob; impacto principal en disponibilidad.

**No confirmado:** exposición de entradas controladas por atacantes, explotación exitosa, incidente real, acceso remoto a esta ruta de código o presencia de ese componente en un artefacto de producción.

**Tratamiento propuesto:** no aceptar la vulnerabilidad por mera ausencia de fallos de compilación. Establecer seguimiento, actualizar componentes compatibles y exigir verificación posterior documentada. Un `build` exitoso no es una prueba de seguridad.

## 7. Solución técnica y controles de cierre

La resolución tiene dos rutas, descritas con comandos y procedimientos de reversión en [SCA-001-remediation.md](SCA-001-remediation.md):

**Ruta A — Preferente:** incorporar una versión oficial publicada de `typescript-eslint` cuyo `@typescript-eslint/typescript-estree` declare una versión corregida de `minimatch`; instalar y repetir todas las pruebas. No confundir la rama `main` del proyecto upstream con el paquete publicado.

**Ruta B — Contingencia controlada:** en una rama separada, evaluar `overrides` de npm dirigidos a `@typescript-eslint/typescript-estree > minimatch` para probar `^11.0.1`. Al tratarse de un cambio de versión mayor fuera del rango declarado por upstream, **no garantiza compatibilidad** y solo debe promoverse si pasa auditoría, tipado, lint, build y pruebas funcionales.

**Criterios mínimos de cierre:** `npm ls minimatch` sin versiones afectadas por este aviso; `npm audit` sin el GHSA investigado; archivos de bloqueo reproducibles; `check`, `lint`, `build` y pruebas funcionales aprobadas; evidencia antes/después; documentación de riesgos residuales. Otros avisos no relacionados requieren triage propio.

## 8. Integración preventiva al SDLC

- Auditar dependencias en CI con `npm ci` y `npm audit --audit-level=moderate`.
- Detectar actualizaciones con Dependabot o equivalente; revisar pull requests y pruebas antes de fusionar.
- Mantener `package-lock.json` bajo control de versiones.
- Generar alertas, responsables, plazo de respuesta y excepciones documentadas.
- Separar claramente severidad del aviso, explotabilidad del activo y prioridad operacional.

> **Una instalación exitosa no equivale a una instalación segura.** La auditoría de dependencias debe incorporarse al ciclo de desarrollo; no puede depender solamente de comprobaciones manuales.

## 9. Dictamen

**Hallazgo confirmado; riesgo en cadena de desarrollo bajo tratamiento; remediación efectiva no acreditada.** Se requiere aplicación controlada del parche y verificación técnica. El reporte documenta una CVE pública preexistente, con atribución al aviso original y evaluación propia de la cadena de dependencias observada.

## 10. Referencias

- [GitHub Security Advisory](https://github.com/advisories/GHSA-g3hv-75wg-m249)
- [NVD CVE-2026-118764](https://nvd.nist.gov/vuln/detail/CVE-2026-118764)
- [Issue upstream #13125](https://github.com/typescript-eslint/typescript-eslint/issues/13125)
- [Pull request upstream #13145](https://github.com/typescript-eslint/typescript-eslint/pull/13145)
- [npm audit](https://docs.npmjs.com/cli/v11/commands/npm-audit)
- [npm overrides](https://docs.npmjs.com/cli/v11/configuring-npm/package-json#overrides)

**Control documental:** 2026-11-16 · Versión 1.0 · Próxima revisión tras remediación validada.
