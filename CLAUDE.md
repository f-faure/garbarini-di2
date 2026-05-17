# Proyecto: Familia de Productos — Cátedra Garbarini
**Última actualización:** mayo 2026 · Estado: productivo ✓ · Hosteado en GitHub Pages

Archivos principales: `actividad.html` + `resultados.html` (single-file cada uno, sin build tools, sin framework).

---

## URLs de producción

| | URL |
|---|---|
| **Actividad (estudiantes)** | https://f-faure.github.io/garbarini-di2/actividad.html |
| **Resultados (docente/estudiantes)** | https://f-faure.github.io/garbarini-di2/resultados.html |
| **Repositorio GitHub** | https://github.com/f-faure/garbarini-di2 |
| **Google Sheet** | https://docs.google.com/spreadsheets/d/187jLF86er37boJRUQUIY4upTohRacCm4o6a40TskBmY |
| **Apps Script endpoint** | https://script.google.com/macros/s/AKfycbzROwQR6KcWpu0h6_upv9aBJHNtj8WxrDwQKsNI2wUB-q-_xThj2C4dD8qcjDuKi3lA/exec |
| **CSV publicado** | https://docs.google.com/spreadsheets/d/e/2PACX-1vT-RIlAQESokIDN2CW3OuO0t5oqZBavF_4bwITk3-DN_aFNDwDPbDQ6RBNK3fvBJq3Rj9e2gSuaMsUq/pub?gid=636223207&single=true&output=csv |

**Deploy:** `git add . && git commit -m "mensaje" && git push` → GitHub Pages publica automáticamente en 1-2 min.

---

## Contexto académico

- Taller: **Diseño Industrial 2 (DI2)** — Cátedra Garbarini, FADU UBA
- Trabajo práctico: clasificación de familias de productos por criterios de diseño
- Dos comisiones: **Violeta** y **Naranja** (únicos valores válidos, no agregar más)

---

## Estructura de la actividad

### Columnas
- **Col 1 — Referencia:** un producto por familia, con badge de letra (A–E). Solo lectura.
- **Col 2 y Col 3:** productos a clasificar. El estudiante asigna una letra a cada uno.
- **5 familias por página** → con 15 familias actuales = 3 páginas exactas (5+5+5).
- Las letras se reasignan A–E en cada página.

### Reglas de selección
- Cada letra solo puede usarse una vez por columna (exclusión mutua).
- Al seleccionar una letra, se deshabilitan las demás opciones con esa misma letra en esa columna.
- **Deselección:** clicar una opción ya seleccionada la desmarca. Funciona tanto con click en el `<input>` como en el `<label>` (usa `resolveRadio()` que resuelve el radio desde el target).
- Cards completadas: borde `--violet` + fondo violeta suave.
- Opciones deshabilitadas: fondo `--paper-warm`, `pointer-events:none`, sin interacción.

### Orden de productos — FIJO, no aleatorio
El orden de col2 y col3 está hardcodeado en `FIXED_SHUFFLES`. **No cambiar a aleatorio.**
- Es el mismo para todos los estudiantes en todas las sesiones.
- Garantiza que ninguna fila tenga la misma familia en las tres columnas (derangement matemático por rotación).
- Si se agregan páginas, extender `FIXED_SHUFFLES` con un nuevo objeto `{ c2:[...], c3:[...] }`.

```js
const FIXED_SHUFFLES = [
  { c2: [4,2,3,1,0], c3: [3,4,0,1,2] },  // Página 1 — c2[0] fue ajustado (ELIXIS en fila 1, ADN en fila 4)
  { c2: [2,3,4,0,1], c3: [4,0,1,2,3] },  // Página 2
  { c2: [1,2,3,4,0], c3: [3,4,0,1,2] },  // Página 3
];
```

### Navegación
- Botón fijo en footer (siempre visible, no requiere scroll).
- Deshabilitado hasta completar **todas** las asignaciones de la página actual.
- Texto dinámico: "Guardar y continuar →" / "Guardar y terminar →" (última página).
- La última página pide confirmación antes de guardar.
- **Durante el guardado:** botón muestra "Guardando…" y se deshabilita. Variable `_saving` previene doble-click. Sin esto el usuario llega a "página 5 de 3" (bug ya corregido).

### Timer
- Lo configura el docente vía URL: `actividad.html?tiempo=15` (minutos).
- Default: 10 min. El estudiante no puede modificarlo.
- Últimos 2 min: color `--danger` + animación blink.
- Al llegar a 0: guarda automáticamente + overlay "Tiempo terminado".

### Pantalla de cierre
- Al guardar la entrega final: oculta `#app`, muestra `#gracias`.
- Dispara `lanzarConfeti()` — implementación canvas pura, sin CDN, funciona offline.
- Mensaje: agradecimiento + link a Google Classroom (`https://classroom.google.com/u/2/c/Nzk2ODk4NDA1MzAy`).

---

## Datos: familias e imágenes

### Array FAMILIAS (estado actual en `actividad.html`)
```js
const FAMILIAS = [
  { nombre:'ADACARPE',     imgs:['ADACARPE/9.png',    'ADACARPE/15.png',  'ADACARPE/37.png']  },
  { nombre:'ADN',          imgs:['ADN/5.png',          'ADN/8.png',        'ADN/16.png']        },
  { nombre:'C²A Lab',      imgs:['C2A Lab/28.png',     'C2A Lab/30.png',   'C2A Lab/29.png']    },
  { nombre:'DINA',         imgs:['DINA/12.png',        'DINA/13.png',      'DINA/14.png']       },
  { nombre:'ELIXIS',       imgs:['ELIXIS/25.png',      'ELIXIS/35.png',    'ELIXIS/36.png']     },
  { nombre:'FADBA',        imgs:['FADBA/11.png',       'FADBA/17.png',     'FADBA/aspiradora NUEVA ).png'] },
  { nombre:'LCB³',         imgs:['LCB3/6.png',         'LCB3/20.png',      'LCB3/19.png']       },  // ← col2/col3 intercambiadas
  { nombre:'LUMA',         imgs:['LUMA/2.png',         'LUMA/10.png',      'LUMA/18.png']       },
  { nombre:'MILTRYX',      imgs:['MILTRYX/3.png',      'MILTRYX/4.png',    'MILTRYX/7.png']     },
  { nombre:'NIDO',         imgs:['NIDO/23.png',        'NIDO/31.png',      'NIDO/33.png']       },
  { nombre:'NOROESTE',     imgs:['NOROESTE/38.png',    'NOROESTE/39.png',  'NOROESTE/40.png']   },
  { nombre:'Origen',       imgs:['Origen/1.png',       'Origen/24.png',    'Origen/27.png']     },
  { nombre:'TRESEMES',     imgs:['TRESEMES/26.png',    'TRESEMES/32.png',  'TRESEMES/escala rec.PNG'] },  // ← antes null
  { nombre:'nulo estudio', imgs:['nulo estudio/21.png','nulo estudio/22.png','nulo estudio/41.png'] },
  { nombre:'zamba.',       imgs:['zamba/aragan desplegado 1.png','zamba/semiperfil nazi 2.png','zamba/aspiradora parada frontal.png'] },
];
```

### Notas sobre rutas
- `C²A Lab` → carpeta del sistema: `C2A Lab` (sin superíndice)
- `LCB³` → carpeta: `LCB3`
- `zamba.` → carpeta: `zamba` (Windows elimina el punto final al crear carpetas)
- NOROESTE: extensión `.png`
- **No mostrar el nombre de la familia en ningún lugar de las tarjetas** (ni en fallback)
- Fallback imagen: fondo `--paper-warm` liso, texto `—`

### Imágenes sueltas en raíz
- `34.png` — sin asignar todavía
- `Redes positivo circular.png` → logo del header y favicon
- `Logo blanco.png` → logo en sidebar del modal

### Procesamiento de imágenes
- Recortadas con ImageMagick `-trim +repage`
- ImageMagick: `C:\Program Files\ImageMagick-7.1.2-Q16-HDRI\magick.exe`

---

## Logos e identidad visual

| Elemento | Archivo | Dónde aparece |
|----------|---------|---------------|
| Logo modal (sidebar violeta) | `Logo blanco.png` | `<img>` en `.m-side` |
| Logo header + favicon | `Redes positivo circular.png` | `<img>` en `.h-brand` + `<link rel="icon">` |

- Footer modal: `DI2 · FADU · UBA` (no "DP2")

---

## Sistema de identidad — Garbarini Design System

Ver spec completo en `garbarini-system.md`. Reglas no negociables:

| Regla | Valor |
|-------|-------|
| Fondo | `--paper` `#F4F1EC` — violeta nunca es fondo general |
| Border-radius | `0` en todo |
| Fuente display | Klavika (4 .otf locales: Regular, LightItalic, Bold, BoldItalic) |
| Fuente mono | JetBrains Mono (Google Fonts CDN) |
| Acento selección | `box-shadow: inset 0 -3px 0 var(--teal)` en radio activo |
| Marcas de corte | `body::before` (TL) y `body::after` (BR), 18×18px, borde ink 35% |
| Imágenes | `filter: grayscale(100%) contrast(1.05); mix-blend-mode: multiply` |
| Jerarquía | Líneas (`--violet-line`), nunca sombras |
| `<em>` en títulos | `font-style:italic; font-weight:300; color:var(--violet)` |
| Eyebrow | Barra 22×2px violet + Klavika Bold 11px uppercase 0.24em tracking |
| Placeholder imagen | Fondo `--paper-warm` liso, texto `—` |

---

## Persistencia — Google Sheets (producción)

**localStorage eliminado.** Todo va a Google Sheets vía Apps Script.

### Flujo de datos
`actividad.html` → `fetch POST` (mode: no-cors) → **Apps Script** → **Google Sheet "Respuestas"**

### Saves intermedios
- Se guarda al completar **cada página** (estado: `"Página N completa"`)
- Se guarda al finalizar (estado: `"Completado"`)
- Se guarda si se agota el tiempo (estado: `"Tiempo agotado"`)
- El Apps Script hace **upsert** por nombre+comisión (sobrescribe la fila si ya existe)

### Estructura del Google Sheet (hoja "Respuestas")
| Columna | Contenido |
|---------|-----------|
| Nombre y apellido | Texto libre |
| Comisión | Violeta / Naranja |
| Respuestas | JSON: `{ "P1_ADACARPE": { REF, C2, C2ok, C3, C3ok }, ... }` |
| Última actualización | Timestamp es-AR |
| Estado | Página N completa / Completado / Tiempo agotado |

### Apps Script (`codigo-apps-script.gs`)
- `doPost(e)`: recibe JSON, upserta fila en el Sheet
- `doGet()`: devuelve todas las filas como JSON (para resultados.html)
- **Error histórico:** `ContentService.createTextResponse` no existe → usar `createTextOutput`
- **Error histórico:** `SpreadsheetApp.getActiveSpreadsheet()` falla en script standalone → usar `openById(SHEET_ID)`
- Al actualizar el código: **Administrar implementaciones → ✏️ → Nueva versión → Implementar** (no crear nueva implementación, la URL no cambia)

### Panel docente
- URL: `actividad.html?admin`
- Muestra botón directo al Google Sheet (ya no tiene tabla local)

---

## Página de resultados (`resultados.html`)

Dashboard independiente, sin relación con `actividad.html`. Lee datos **directamente del CSV publicado** de Google Sheets (sin Apps Script).

### Fuente de datos
- Google Sheet → **Archivo → Compartir → Publicar en la web → Hoja "Respuestas" → CSV**
- URL en `resultados.html`: constante `SHEET_CSV`
- **No usar** `/export?format=csv` (falla CORS). Usar `/pub?output=csv` con el ID publicado.

### Contenido
- **3 métricas globales:** total entregas · entregas completas · % acierto global
- **Por familia:** barras horizontales ordenadas de mayor a menor % de aciertos (verde ≥70%, violeta ≥40%, naranja <40%)
- **Por estudiante:** tabla ordenada por % con mini-barra y estado
- Botón **Actualizar ↻** recarga en tiempo real

### Parser CSV
El campo "Respuestas" contiene JSON con comillas escapadas (`""`). El parser custom en `resultados.html` hace unescape correcto de `""` → `"` dentro de campos entrecomillados. También elimina BOM si existe.

---

## Bugs corregidos (historial completo)

| Bug | Causa | Solución |
|-----|-------|----------|
| Pantalla en blanco al iniciar | `getElementById('gp-total')` nulo | Eliminar referencias a `gp-done` y `gp-total` |
| Letra no deshabilita otras opciones | Eventos no capturaban clicks en `<label>` | `resolveRadio()` resuelve el radio desde label.htmlFor |
| Confetti no funcionaba | Dependía de CDN | Canvas API puro + requestAnimationFrame |
| Imágenes NOROESTE no cargaban | Código decía `.jpg`, archivos son `.png` | Corregir extensión en array FAMILIAS |
| Orden aleatorio en cada sesión | `derangement()` aleatorio | `FIXED_SHUFFLES` hardcodeado |
| Nombre de familia visible | `.pname` y `.pimg-fallback` mostraban el nombre | Eliminados; fallback muestra solo `—` |
| "Página 5 de 3" al hacer doble-click en Continuar | `navNext()` async sin bloqueo de re-entrada | Variable `_saving` + deshabilitar botón durante await |
| Apps Script no escribía en Sheet | `createTextResponse` no existe + `getActiveSpreadsheet()` falla en standalone | Usar `createTextOutput` + `openById(SHEET_ID)` |
| CSV de resultados no cargaba | URL `/export` bloqueada por CORS | Usar URL `/pub` del Sheet publicado en la web |
| JSON no parseaba en resultados | CSV escapaba `"` como `""` pero parser no hacía unescape | Parser custom con manejo de `""` dentro de campos |

---

## Para agregar una familia nueva

1. Crear carpeta con el nombre del grupo dentro de `Familia Garbarini/`
2. Poner 3 imágenes: col1 (referencia), col2, col3
3. Agregar entrada al array `FAMILIAS` en `actividad.html`
4. Si el total de familias cambia de 15, revisar `FIXED_SHUFFLES` y agregar página
5. Correr ImageMagick trim sobre las imágenes nuevas
6. `git add . && git commit -m "..." && git push`

---

## Tipografías

- Los 4 archivos `.otf` de Klavika deben estar en la raíz del repo junto al HTML
- El navegador los descarga desde GitHub Pages — no hace falta tenerlos instalados localmente
- Desde `file://` no cargan (política de seguridad del navegador) → usar servidor local: `python -m http.server 8080`
