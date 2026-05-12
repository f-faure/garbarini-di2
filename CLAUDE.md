# Proyecto: Familia de Productos — Cátedra Garbarini
**Última actualización:** mayo 2026 · Estado: funcional ✓

Archivo principal: `actividad.html` (single-file, sin build tools, sin framework).

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
  { c2: [1,2,3,4,0], c3: [3,4,0,1,2] },  // Página 1
  { c2: [2,3,4,0,1], c3: [4,0,1,2,3] },  // Página 2
  { c2: [1,2,3,4,0], c3: [3,4,0,1,2] },  // Página 3
];
```

### Navegación
- Botón fijo en footer (siempre visible, no requiere scroll).
- Deshabilitado hasta completar **todas** las asignaciones de la página actual.
- Texto dinámico: "Guardar y continuar →" / "Guardar y terminar →" (última página).
- La última página pide confirmación antes de guardar.

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

### Array FAMILIAS (en `actividad.html`)
```js
const FAMILIAS = [
  { nombre:'ADACARPE',     imgs:['ADACARPE/9.png',    'ADACARPE/15.png',  'ADACARPE/37.png']  },
  { nombre:'ADN',          imgs:['ADN/5.png',          'ADN/8.png',        'ADN/16.png']        },
  { nombre:'C²A Lab',      imgs:['C2A Lab/28.png',     'C2A Lab/30.png',   'C2A Lab/29.png']    },
  { nombre:'DINA',         imgs:['DINA/12.png',        'DINA/13.png',      'DINA/14.png']       },
  { nombre:'ELIXIS',       imgs:['ELIXIS/25.png',      'ELIXIS/35.png',    'ELIXIS/36.png']     },
  { nombre:'FADBA',        imgs:['FADBA/11.png',       'FADBA/17.png',     'FADBA/aspiradora NUEVA ).png'] },
  { nombre:'LCB³',         imgs:['LCB3/6.png',         'LCB3/19.png',      'LCB3/20.png']       },
  { nombre:'LUMA',         imgs:['LUMA/2.png',         'LUMA/10.png',      'LUMA/18.png']       },
  { nombre:'MILTRYX',      imgs:['MILTRYX/3.png',      'MILTRYX/4.png',    'MILTRYX/7.png']     },
  { nombre:'NIDO',         imgs:['NIDO/23.png',        'NIDO/31.png',      'NIDO/33.png']       },
  { nombre:'NOROESTE',     imgs:['NOROESTE/38.png',    'NOROESTE/39.png',  'NOROESTE/40.png']   },  // ← .png no .jpg
  { nombre:'Origen',       imgs:['Origen/1.png',       'Origen/24.png',    'Origen/27.png']     },
  { nombre:'TRESEMES',     imgs:['TRESEMES/26.png',    'TRESEMES/32.png',  null]                },  // falta col3
  { nombre:'nulo estudio', imgs:['nulo estudio/21.png','nulo estudio/22.png','nulo estudio/41.png'] },
  { nombre:'zamba.',       imgs:['zamba/aragan desplegado 1.png','zamba/semiperfil nazi 2.png','zamba/aspiradora parada frontal.png'] },
];
```

### Notas sobre rutas
- `C²A Lab` → carpeta del sistema: `C2A Lab` (sin superíndice)
- `LCB³` → carpeta: `LCB3`
- `zamba.` → carpeta: `zamba` (Windows elimina el punto final al crear carpetas)
- NOROESTE: extensión `.png` (las imágenes eran .jpg pero se convirtieron al hacer trim)
- `null` como imagen → muestra placeholder `—` sin texto ni nombre de familia
- **No mostrar el nombre de la familia en ningún lugar de las tarjetas** (ni en fallback)

### Imágenes sueltas en raíz
- `34.png` — sin asignar a carpeta todavía
- `Redes positivo circular.png` → logo del header y favicon
- `Logo blanco.png` → logo en sidebar del modal
- `cambios.jpg` → referencia visual del reordenamiento (ya aplicado)

### Procesamiento de imágenes
- Recortadas con ImageMagick `-trim +repage` (elimina transparencia excesiva)
- Renombradas como números secuenciales (1–41) antes de distribuir en carpetas
- ImageMagick instalado en: `C:\Program Files\ImageMagick-7.1.2-Q16-HDRI\magick.exe`

---

## Logos e identidad visual

| Elemento | Archivo | Dónde aparece |
|----------|---------|---------------|
| Logo modal (sidebar violeta) | `Logo blanco.png` | `<img>` en `.m-side` |
| Logo header + favicon | `Redes positivo circular.png` | `<img>` en `.h-brand` + `<link rel="icon">` |

- El círculo `.g-logo` con "G" ya **no se usa** — reemplazado por `Redes positivo circular.png`
- El texto "Cátedra Garbarini / Diseño Industrial" ya **no se usa** en el modal — reemplazado por `Logo blanco.png`
- Footer modal: `DI2 · FADU · UBA` (no "DP2")

---

## Sistema de identidad — Garbarini Design System

Ver spec completo en `garbarini-system.md`. Reglas no negociables:

| Regla | Valor |
|-------|-------|
| Fondo | `--paper` `#F4F1EC` — violeta nunca es fondo general |
| Border-radius | `0` en todo. Única excepción histórica `.g-logo` (ya no se usa) |
| Fuente display | Klavika (4 .otf locales: Regular, LightItalic, Bold, BoldItalic) |
| Fuente mono | JetBrains Mono (Google Fonts CDN) |
| Acento selección | `box-shadow: inset 0 -3px 0 var(--teal)` en radio activo |
| Marcas de corte | `body::before` (TL) y `body::after` (BR), 18×18px, borde ink 35% |
| Imágenes | `filter: grayscale(100%) contrast(1.05); mix-blend-mode: multiply` |
| Jerarquía | Líneas (`--violet-line`), nunca sombras |
| `<em>` en títulos | `font-style:italic; font-weight:300; color:var(--violet)` |
| Eyebrow | Barra 22×2px violet + Klavika Bold 11px uppercase 0.24em tracking |
| Placeholder imagen | Fondo `--paper-warm` liso (sin rayas diagonales), texto `—` |

---

## Persistencia y exportación

### localStorage
- Key: `dp2_responses`
- Acumula todas las entregas. **Solo funciona hosteado en servidor** (no desde `file://`).
- Para producción: migrar `saveAll()` a un POST a Google Apps Script → Google Sheets.

### Panel docente
- URL: `actividad.html?admin`
- Muestra tabla de todas las entregas con conteo.
- "Exportar Excel" → `garbarini_familia_productos_YYYYMMDD.xlsx` (SheetJS CDN).
- "Borrar datos" → con confirmación.

### Estructura del Excel por fila
`Nombre | Comision | Fecha | Tiempo | P{n}_{familia}_REF | P{n}_{familia}_C2 | P{n}_{familia}_C3 | P{n}_{familia}_C2ok | P{n}_{familia}_C3ok`

- `_REF` = letra correcta de esa familia en esa página
- `_C2` / `_C3` = lo que respondió el estudiante
- `_C2ok` / `_C3ok` = "OK" o "MAL" (corrección automática)

---

## Bugs corregidos (historial)

| Bug | Causa | Solución |
|-----|-------|----------|
| Pantalla en blanco al iniciar | `getElementById('gp-total')` nulo tras mover barra de progreso al header | Eliminar referencias a `gp-done` y `gp-total` |
| Letra no deshabilita otras opciones | Eventos `click`/`change` no capturaban clicks en `<label>` | `resolveRadio()` resuelve el radio desde label.htmlFor |
| Confetti no funcionaba | Dependía de CDN que no carga offline | Implementación propia con Canvas API + requestAnimationFrame |
| Imágenes NOROESTE no cargaban | Código decía `.jpg`, archivos convertidos a `.png` | Corregir extensión en array FAMILIAS |
| Orden aleatorio en cada sesión | `derangement()` aleatorio | `FIXED_SHUFFLES` hardcodeado |
| Nombre de familia visible | `.pname` y `.pimg-fallback` mostraban el nombre | Eliminados; fallback muestra solo `—` |

---

## Hosting (pendiente)

Para que múltiples estudiantes puedan guardar en un único lugar:
1. Subir archivos a **GitHub Pages** (gratis)
2. Crear endpoint en **Google Apps Script** que reciba POST y escriba en Google Sheets
3. Reemplazar `localStorage` en `saveAll()` por `fetch(ENDPOINT, { method:'POST', body:JSON.stringify(row) })`
4. Para cerrar la actividad: archivar el deployment de Apps Script, o agregar `?tiempo` fijo y fecha de cierre en el código

---

## Para agregar una familia nueva

1. Crear carpeta con el nombre del grupo dentro de `Familia Garbarini/`
2. Poner 3 imágenes: col1 (referencia), col2, col3
3. Agregar entrada al array `FAMILIAS` en `actividad.html`
4. Si el total de familias cambia de 15, revisar `FIXED_SHUFFLES` y agregar página si es necesario
5. Correr ImageMagick trim sobre las imágenes nuevas antes de usar
