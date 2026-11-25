# KNOVA Security Research

**Independent security research, Software Composition Analysis (SCA), vulnerability management and secure software engineering.**

Investigación técnica, auditoría de dependencias, análisis de riesgo y documentación reproducible de hallazgos en entornos controlados.

> **Una instalación exitosa no equivale a una instalación segura.**

## Aviso visual de seguridad — Dependencias npm

![Advertencia de dependencia vulnerable — KNOVA NextGenAI](Advertencia%20de%20dependencia%20vulnerable.png)

[Ver tarjeta en tamaño completo](Advertencia%20de%20dependencia%20vulnerable.png) · [Consultar análisis técnico SCA-001](reports/2026/SCA-001-minimatch.md)

La tarjeta presenta un **ejemplo conceptual de advertencia**, basado en la dependencia afectada identificada en el análisis SCA-001. No reproduce una salida literal de npm.


## Casos de investigación

| ID | Fecha | Categoría | Estado |
|---|---|---|---|
| [SCA-001 — npm / minimatch](cases/npm-minimatch-CVE-2026-118764/README.md) | 2026-11-16 | Software Composition Analysis | Hallazgo documentado; remediación original no verificada |
| [GHV-001 — GitHub / Vercel](cases/github-vercel-2026-11-25/README.md) | 2026-11-25 | Disponibilidad, OAuth y GitHub Apps | Incidente observado; causa específica no confirmada |

El **estilo visual** de ambos casos se inspira en la tarjeta roja de seguridad ya publicada, pero son investigaciones independientes. No se ha establecido relación causal entre CVE-2026-118764 y los errores HTTP 500 de GitHub.

## Informes publicados

| ID | Fecha | Componente | Hallazgo | Estado |
|---|---|---|---|---|
| [SCA-001](reports/2026/SCA-001-minimatch.md) | 2026-11-16 | `minimatch@10.2.6` vía `typescript-eslint` | [GHSA-g3hv-75wg-m249](https://github.com/advisories/GHSA-g3hv-75wg-m249) · CVE-2026-118764 · DoS potencial por amplificación | **Remediación no verificada** |

## Metodología

1. Identificación de componentes y versiones instaladas.
2. Correlación de alertas con avisos oficiales, CVE y CWE.
3. Reconstrucción de dependencias directas y transitivas.
4. Evaluación del impacto y de las condiciones reales de exposición.
5. Plan de tratamiento y control de compatibilidad.
6. Validación posterior: auditoría, compilación, pruebas funcionales y riesgos residuales.

## Caso SCA-001

- [Informe de auditoría: alcance, evidencias, diagnóstico y tratamiento](reports/2026/SCA-001-minimatch.md)
- [Plan técnico de remediación y validación](reports/2026/SCA-001-remediation.md)
- [Guía de recolección de evidencias](reports/2026/EVIDENCE.md)

**Estado documental:** se verificó la detección y la compilación inicial. No se ha aplicado ni verificado una corrección en el entorno de la analista. El informe no afirma explotación de la vulnerabilidad.

## Divulgación responsable

Este repositorio documenta detecciones independientes de vulnerabilidades conocidas y evaluaciones de exposición. No reivindica el descubrimiento original de CVE ajenas ni publica credenciales, rutas personales o información sensible. Las conclusiones se limitan a la evidencia efectivamente obtenida.

**KNOVA | NextGenAI** — Security research & engineering.
