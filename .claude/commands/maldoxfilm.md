# Skill: MaldoxFilm — Contexto Completo del Proyecto

Cuando este skill se carga, tienes todo el contexto del proyecto MaldoxFilm.
No preguntes nada que ya esté aquí. Trabaja directamente.

---

## ¿Qué es MaldoxFilm?

App de streaming estilo Netflix que corre 100% en el navegador, sin instalar nada.
Es una SPA (Single Page Application) en un solo archivo HTML.

- **URL producción:** https://maldoxfilm.netlify.app
- **Repo GitHub:** https://github.com/maldoxs/maldoxfilm
- **Archivo principal:** `/Users/angelomaldonado/Desktop/Streamix-local/assets/index.html`
- **Deploy:** Netlify automático con `npx netlify-cli deploy --prod --dir=assets`

**Regla del usuario: nunca hacer commit sin preguntar primero.**
**Flujo: deploy Netlify primero → usuario confirma que funciona → push GitHub.**

---

## El ecosistema de streaming — conocimiento base

### IMDB (Amazon, 1990)
- "El Registro Civil del Cine"
- Asigna IDs universales únicos: `tt0078748` = Alien (1979)
- Formato: prefijo `tt` + números secuenciales
- Controlado por Amazon, IDs inmutables, nadie los puede cambiar
- No tiene API gratuita para desarrolladores

### TMDB (The Movie Database, 2008)
- "El Periodista Visual" — alternativa open source a IMDB
- Tiene su propio ID numérico: `27205` = Alien (1979)
- Guarda el IMDB ID internamente como puente
- API GRATUITA — por eso MaldoxFilm la usa para el catálogo
- Provee: portadas, trailers YouTube, actores, descripción, colecciones

### Torrents
- No son el archivo — son un MAPA de dónde están los pedazos del archivo
- Cada torrent tiene un HASH único (su huella digital)
- Mientras más gente comparte → más rápido descarga
- Problema: pueden tardar horas en descargar

### Torrentio
- Buscador de torrents especializado en películas/series
- Recibe IMDB ID → busca torrents → verifica cuáles tiene RD cacheados
- Devuelve lista de streams con URLs directas
- NO es RD — es el CATÁLOGO del almacén, RD es el almacén
- URL: `https://torrentio.strem.fun/realdebrid=TOKEN|qualityfilter=other,scr,cam/stream/movie/IMDBID.json`

### Real-Debrid (RD)
- Servidor premium que descarga torrents a máxima velocidad y los guarda
- NO busca nada — solo sirve lo que ya tiene descargado
- NO organiza por IMDB ID — organiza por hash de torrent
- Se llena con lo que usuarios premium pidieron descargar
- **Transcode:** convierte x265 → H.264 HLS para que Chrome lo reproduzca
- API transcode: `GET /rest/1.0/streaming/transcode/{id}`
- API downloads: `GET /rest/1.0/downloads?limit=500`

### x265 vs x264
- x265 = mejor calidad, menos tamaño, Chrome NO puede reproducirlo
- x264 = compatible con Chrome directamente
- Chrome no pagó licencia HEVC → no reproduce x265
- Safari/FireTV/Android sí reproducen x265 (pagaron licencia)
- Solución: RD transcodifica x265 → H.264 HLS → Chrome lo reproduce

### Codecs de audio
- DTS / TrueHD → Chrome NO los reproduce
- AAC / MP3 → Chrome SÍ los reproduce
- AC3 / Dolby → Chrome parcialmente

---

## Arquitectura de MaldoxFilm

```
Usuario busca película
        ↓
TMDB API (tmdb_id) → portada, info, actores
        ↓
Usuario hace clic en REPRODUCIR
        ↓
TMDB API → convierte tmdb_id → imdb_id
        ↓
Torrentio → busca streams con imdb_id
        ↓
MaldoxFilm puntúa y elige mejor stream:
  Prioridad 1: RD + x264 + AAC  ✅✅
  Prioridad 2: RD + x264        ✅⚠️
  Prioridad 3: RD + AAC         ⚠️✅
  Prioridad 4: Cualquier RD     ⚠️⚠️
        ↓
¿Es x264? → video.src directo
¿Es x265? → RD transcode → HLS.js
        ↓
Fallback automático:
  ⚡ RD → 🌐 Stream-Vault → ▶ UnlimPlay → ▶ VidSrc
```

---

## Constantes clave en el código

```javascript
// ~línea 3807 en index.html
var SV_BASE    = 'https://stream-vault-two-phi.vercel.app';
var SV_SRC_IDX = 98;
var RD_TOKEN   = 'Z5EQ6B2BAS6ZKOYCD6EWAQENYST6B6TUQKQOWAVYJ26RBVYI7HQA';
var RD_SRC_IDX = 99;
var TMDB_KEY   = 'd95e6a7e2ead40e949fcdb81f1f26f0b';
```

---

## Funciones críticas

### rdGetStream (~línea 3829)
- Obtiene IMDB ID desde TMDB
- Consulta Torrentio con el token RD
- Elige el mejor stream (H264 > AAC > RD > cualquiera)
- Intenta resolver URLs proxy de Torrentio
- Busca rdId en /downloads de RD para poder llamar transcode
- Devuelve `{ url, rdId }`

### Bloque de reproducción RD (~línea 6372)
- Si tiene rdId → llama transcode API → HLS.js
- Si no → video.src directo con timeout 8s
- Fallback a SV si falla

### loadPlayerSource (~línea 6237)
- Función principal que decide qué fuente cargar
- Revisa `_activeSrcIdx` para saber qué fuente usar

---

## Errores conocidos y resueltos

### ❌ `_safePlay is not defined` — Mayo 2026 — RESUELTO
**Causa:** Se usaba `_safePlay(video)` en 4 lugares pero nunca fue definida.
**Fix:** Reemplazar por `video.play().catch(()=>{})` en todos los puntos.
**Lección:** Nunca crear referencias a funciones que no existen.

### ❌ CDN hash ≠ RD download ID
**Causa:** El hash en la URL CDN (`cdn99.real-debrid.com/dl/HASH/`) no es el mismo que el `id` en `/downloads`.
**Fix:** Buscar en `/downloads` por URL exacta o filename para obtener el ID real.

### ❌ Torrentio devuelve URL proxy, no CDN directa
**Causa:** Torrentio a veces devuelve `torrentio.strem.fun/resolve/...` en lugar de CDN directa.
**Fix:** El navegador sigue el redirect automáticamente en `video.src`. Para transcode, intentar resolver con fetch primero.

### ❌ x265 pantalla negra sin error
**Causa:** Chrome no puede decodificar x265, no dispara evento `error` ni `loadedmetadata`.
**Fix:** Timeout de 8s + verificar `video.readyState < 2`.

---

## Stack tecnológico

| Tecnología | Uso |
|---|---|
| HTML5 `<video>` | Reproductor base |
| HLS.js | Reproduce streams HLS de transcode RD |
| Shaka Player | Reproduce streams de Stream-Vault |
| TMDB API | Catálogo, portadas, metadata |
| Torrentio | Buscador de streams en RD |
| Real-Debrid API | Transcode x265, lista de downloads |
| Netlify | Hosting estático gratuito |

---

## Pendiente de implementar

### FASE 2 — Scoring inteligente de streams
Puntuar cada stream en dos dimensiones:
- Nota técnica: resolución + codec video + codec audio
- Nota idioma: doblaje ES, subtítulos ES, multi-idioma
Elegir el de mayor score total en lugar de solo calidad técnica.

```javascript
// Keywords que detectar en el nombre del archivo:
// DUAL, MULTI, LATINO, CASTELLANO, ESP, SPANISH, SUB, SUBS
```

### FASE 3 — Player avanzado con Plyr
- Plyr sobre HLS.js → UI estilo Netflix
- Selector de subtítulos (via OpenSubtitles API con IMDB ID)
- Selector de pista de audio (español/inglés)
- Selector de calidad

### OpenSubtitles API
- Ya tenemos el IMDB ID → llamar API → obtener .vtt
- Cargar con `<track>` en HTML5 video
- Selector de idioma en UI del player

---

## Documentación del proyecto

Carpeta `docs/` en el repo:
```
docs/
 ├── README.md                              ← índice
 ├── ESQUEMA_GENERAL.md                     ← diagrama completo del sistema
 ├── FLUJO_COMPLETO_HOLLYWOOD_A_MALDOXFILM  ← de Hollywood al reproductor
 ├── IMDB_TMDB_EXPLICADO.md                 ← qué son y cómo funcionan
 ├── TORRENTS_Y_TORRENTIO_EXPLICADO.md      ← qué es un torrent
 ├── SELECCION_MEJOR_STREAM.md              ← lógica de selección
 ├── FLUJO_RD_TORRENTIO.md                  ← detalle técnico + errores
 ├── X265_EXPLICADO.md                      ← el problema del codec
 ├── ENDPOINTS.md                           ← referencia APIs
 └── ANALISIS_STREAMIX.md                   ← origen del proyecto
```

---

## Contexto del usuario

- Desarrollador activo aprendiendo el ecosistema de streaming
- Quiere entender cada pieza, no solo copiar código
- Prefiere explicaciones simples con analogías
- Cuando algo no funciona, quiere saber el porqué exacto
- Flujo de trabajo: Netlify primero, GitHub después, siempre preguntar antes de commit
