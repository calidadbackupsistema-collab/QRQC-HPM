# HPM · ROCA + QRQC-D  (GitHub Pages + Firebase)
Hyundai Polytech México · Sistema de gestión de calidad · Código de documento `HPM-CALIDAD-FO-01`

| Archivo | Qué es |
|---|---|
| `index.html` | **QRQC-D** – Tablero de Respuesta Rápida a Problemas de Calidad Digital: KPIs (Reclamos oficiales <7 NCR, FTQ GP12 <1%), tendencia mensual, tablero Occurrence/Detection y seguimiento de acciones. |
| `roca.html` | **ROCA (Root Cause & Corrective Action)**: 2-Panel, Auditoría 9B, 8D, DMAIC y DOE. Botón **Enviar a QRQC-D** alimenta el tablero. |
| `js/hpm-common.js` | Logo, textos ES/KO/EN y utilidades compartidas. |
| `js/hpm-sync.js` | Capa de datos: localStorage ⇄ Firestore, login y sincronización en vivo. |
| `js/firebase-config.js` | **Aquí pegas la configuración de tu proyecto Firebase.** |
| `firestore.rules` | Reglas de seguridad (solo correos autorizados). |

Sin configurar Firebase las apps funcionan en **modo local** (datos solo en el navegador, sin login).

## 1) Firebase (una sola vez)
1. https://console.firebase.google.com → **Agregar proyecto**.
2. **Compilación → Authentication → Comenzar** → habilita **Correo/contraseña** (y **Google**, opcional).
3. **Compilación → Firestore Database → Crear base de datos** (modo producción, región cercana).
4. **Configuración del proyecto → Tus apps → Web (`</>`)** → copia el `firebaseConfig` y pégalo en `js/firebase-config.js`.
5. **Firestore → Reglas** → pega el contenido de `firestore.rules` → **Publicar**.
6. **Usuarios autorizados:** en Authentication → Users crea a cada persona (correo/contraseña). Después, en Firestore crea la colección `hpm_allowed` y un documento por persona **cuyo ID sea su correo en minúsculas** (ej. `alberto.gv01@gmail.com`, sin campos obligatorios). Quien no esté ahí no puede leer ni escribir aunque inicie sesión.
7. Authentication → Settings → **Authorized domains** → agrega `TU_USUARIO.github.io`.

> La `apiKey` de Firebase web **no es un secreto**; la protección real son las reglas + la lista `hpm_allowed`. Nunca subas claves de servicio ni cuentas de administrador al repositorio.

## 2) GitHub Pages
1. Crea un repositorio y sube **todo el contenido de esta carpeta** (incluye `.nojekyll`).
2. **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)`** → Save.
3. Queda en `https://TU_USUARIO.github.io/NOMBRE_REPO/` (QRQC-D) y `.../roca.html` (ROCA).

## Cómo se guardan los datos
Una colección por módulo (`quality_8d_reports_v1`, `hpm_9basics_reports_v1`, `hpm_2panel_reports_v1`, `hpm_dmaic_reports_v1`, `hpm_doe_reports_v1`, `hpm_qrqcd_board_v1`, `hpm_qrqcd_kpi_v1`, `hpm_qrqcd_meta_v1`) y un documento por registro.
- Primer inicio de sesión: los datos locales existentes se **fusionan y suben** a la nube.
- Después, la nube es la fuente de verdad y los cambios de otros usuarios aparecen en vivo.
- Si dos personas editan el **mismo** registro a la vez, gana el último guardado.
- Límite de Firestore: **1 MB por registro**. Las fotos se comprimen automáticamente (QRQC-D 1280 px, 2-Panel 560 px); los archivos adjuntos del 8D se limitan a 700 KB con Firebase activo.

## Enlaces entre las dos apps
- ROCA → QRQC-D: botón **Enviar a QRQC-D** (actualiza el mismo renglón si ya se envió).
- QRQC-D → ROCA: botón 🔗 del renglón abre `roca.html?open=<tipo>:<id>` con el análisis cargado.
