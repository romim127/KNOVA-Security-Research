# SCA-001 — Plan de remediación y verificación

**Estado: propuesta técnica; no ejecutada ni validada en el entorno observado.**

## 1. Preservar el estado inicial

Ejecutar en la raíz del proyecto:

```powershell
git status
npm.cmd ls minimatch
npm.cmd audit --json > audit-antes.json
```

Guardar `package.json` y `package-lock.json` en un commit o copia de seguridad. Revisar y sanear cualquier evidencia antes de publicarla. `npm audit` puede devolver código de salida no cero cuando encuentra vulnerabilidades: eso no implica que no se haya creado el archivo JSON.

Crear rama de prueba (si el proyecto usa Git):

```powershell
git switch -c security/sca-001-minimatch
```

## 2. Ruta recomendada: actualizar a una publicación upstream corregida

Consultar las versiones efectivamente disponibles en el registro:

```powershell
npm.cmd view typescript-eslint version
npm.cmd view @typescript-eslint/typescript-estree@latest dependencies.minimatch
```

**Si una nueva publicación oficial declara un rango que admita una versión de minimatch corregida**, actualizar el paquete principal siguiendo las restricciones de compatibilidad de ESLint y Node.js, y volver a instalar desde el lockfile generado. No asumir que el cierre de un issue garantiza que el paquete ya está publicado.

```powershell
npm.cmd install --save-dev typescript-eslint@latest
npm.cmd ls minimatch
npm.cmd audit
npm.cmd run check
npm.cmd run lint
npm.cmd run build
```

Ejecutar además pruebas de uso real en navegador y del proceso de lint. Si la nueva versión no está publicada o no resuelve la dependencia, mantener abierto el hallazgo y evaluar contingencia.

## 3. Ruta de contingencia: override acotado

**Advertencia:** forzar `minimatch@11` bajo una herramienta que declara `^10.2.2` cruza una versión mayor, potencialmente incompatible. Es una **hipótesis de remediación que requiere validación**, no una solución declarada como probada.

Solo en una rama de prueba y con respaldo, agregar al `package.json` raíz este bloque, sin borrar otras propiedades:

```json
{
  "overrides": {
    "@typescript-eslint/typescript-estree": {
      "minimatch": "^11.0.1"
    }
  }
}
```

Si `package.json` ya contiene `overrides`, integrar cuidadosamente sin sobrescribir los existentes. Luego:

```powershell
npm.cmd install
npm.cmd ls minimatch
npm.cmd explain minimatch
npm.cmd audit
npm.cmd run check
npm.cmd run lint
npm.cmd run build
```

Realizar pruebas reales de ESLint sobre archivos representativos, ejecutar la aplicación con `npm.cmd run dev`, comprobar la consulta HTTP y el DOM, y verificar compatibilidad en escritorio/móvil. Revisar el diff de `package.json` y `package-lock.json`.

### Criterio de aceptación

- La rama afectada ya no resuelve `minimatch@10.2.6` ni otra versión vulnerable para el GHSA analizado.
- `npm audit` deja de reportar el aviso investigado.
- `check`, `lint`, `build` y pruebas funcionales finalizan correctamente.
- La instalación reproducida con `npm.cmd ci` mantiene ese resultado.
- Se documentan el diff, las evidencias de antes/después y cualquier riesgo residual.

**Si alguno falla, no fusionar el override.** Volver al estado previo con control de versiones, por ejemplo descartando la rama de prueba sin fusionarla. No ejecutar `npm audit fix --force` de manera indiscriminada.

## 4. Prevención automatizada

En los proyectos que cuenten con CI, ejecutar como controles mínimos:

```powershell
npm.cmd ci
npm.cmd audit --audit-level=moderate
npm.cmd run check
npm.cmd run lint
npm.cmd run build
```

Configurar Dependabot u otra solución para propuestas de actualización revisables. Los avisos que no puedan corregirse de inmediato deben contar con propietario, fecha de revisión, evaluación de exposición y aceptación de riesgo explícita cuando corresponda.

## 5. Dictamen de remediación

**Pendiente de ejecución y comprobación.** Este archivo contiene un procedimiento operativo, no una constancia de corrección. La aceptación final requiere evidencia verificable de ejecución y resultados.
