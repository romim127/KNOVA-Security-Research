# E-05 — Registros HTTP sanitizados

**Pendiente:** todavía no se incorporaron exportaciones Network/HAR/Burp verificadas.

Formato sugerido para futuras observaciones legítimas:

| Fecha (UTC) | Origen | Método | Host | Ruta SIN parámetros | HTTP | Duración | Observación |
|---|---|---|---|---|---|---|---|

No subir HAR bruto, headers `Cookie`/`Authorization`, cuerpos de respuestas de sesión, `state`, `code`, ni URL completas de autenticación.

**Alcance:** una sesión propia y observación pasiva. No escanear infraestructura ajena ni automatizar solicitudes.
