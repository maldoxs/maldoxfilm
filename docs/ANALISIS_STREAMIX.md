# 📱 Análisis Técnico — Streamix 3.4

> Ingeniería inversa del APK `streamix3.4.apk` para entender su arquitectura y flujos de datos.

---

## 1. Arquitectura General

Streamix es una **WebView app** — no hay lógica nativa relevante. Todo el producto es un único archivo:

```
assets/index.html  (290 KB — 5.606 líneas)
```

El código Kotlin/Java (`classes.dex`) solo actúa como contenedor: abre un WebView que carga ese HTML y expone un puente nativo llamado `AndroidApp`.

```
┌─────────────────────────────┐
│   App Android (Kotlin)      │
│                             │
│  ┌───────────────────────┐  │
│  │   WebView             │  │
│  │   assets/index.html   │  │
│  │   (toda la lógica)    │  │
│  └───────────────────────┘  │
│                             │
│  AndroidApp.openPlayer()    │  ← puente nativo para player Android
└─────────────────────────────┘
```

### Detección de plataforma (automática)

Al iniciar, el HTML detecta el entorno antes de renderizar nada:

| Plataforma | Criterio de detección |
|------------|----------------------|
| **TV** | UA contiene `webos`, `tizen`, `smarttv`, etc. O: pantalla ≥1280px + sin touch + DPR ≤1.5 |
| **Mobile** | Touch habilitado o pantalla ≤1024px |
| **Desktop** | Todo lo demás |

Aplica clase CSS al `<html>`: `tv-mode` / `mobile-mode` / `desktop-mode`, lo que activa layouts distintos.

---

## 2. Constantes y Configuración Global

```js
const TMDB_KEY    = 'd95e6a7e2ead40e949fcdb81f1f26f0b';
const LANG        = 'es-MX';
const TMDB        = 'https://api.themoviedb.org/3';
const IMG         = 'https://image.tmdb.org/t/p/';

const ANIME_API_BASE = 'https://anime1v-api-cloned.onrender.com';
const ANIME_API_KEY  = '86d6451302f2150096aa561c7311ed6f';

const TVLIBRE_BASE   = 'https://apifutboltv.onrender.com/api';
const API_AUTHORIZER = 'colombia-tv-access';   // header de auth

const SOURCES = [
  {
    name: 'StreanMix',
    movie: id        => `https://unlimplay.com/play/embed/movie/${id}`,
    tv:   (id, s, e) => `https://unlimplay.com/play/embed/tv/${id}/${s}/${e}`
  }
];
```

---

## 3. Fuentes de Datos

| Fuente | URL | Para qué |
|--------|-----|----------|
| **TMDB** | `api.themoviedb.org/3` | Catálogo de películas y series (metadata, posters, recomendaciones) |
| **unlimplay.com** | `unlimplay.com/play/embed/movie/{id}` | Reproductor embed — scraping del stream en tiempo real |
| **iptv-org** | `iptv-org.github.io/iptv/index.m3u` | Lista maestra de canales IPTV (M3U público) |
| **TVLibre / apifutboltv** | `apifutboltv.onrender.com/api` | Canales de deportes dinámicos + agenda deportiva |
| **anime1v API** | `anime1v-api-cloned.onrender.com` | Episodios de anime con servidores SUB/DUB |
| **YouTube embeds** | `youtube.com/embed/live_stream?channel=...` | Canales de noticias y TV colombiana/latina |
| **pelotalibretv2.pl** | `pelotalibretv2.pl/en-vivo/...` | Scrapers de deportes (ESPN, Fox Sports, etc.) |
| **Streams directos .m3u8** | Hardcodeados | DW Español, RTVE 24h, Canal Once MX |

---

## 4. Flujo: Películas

### 4.1 Carga del catálogo

```
Usuario → click "Películas"
    │
    ▼
loadMoviesPage()
    │
    ├── TMDB: /trending/movie/week              → carrusel "Tendencias"
    ├── TMDB: /discover/movie?with_genres=28    → Acción       (lazy)
    ├── TMDB: /discover/movie?with_genres=35    → Comedia      (lazy)
    ├── TMDB: /discover/movie?with_genres=18    → Drama        (lazy)
    ├── TMDB: /discover/movie?with_genres=27    → Terror       (lazy)
    ├── TMDB: /discover/movie?with_genres=878   → Sci-Fi       (lazy)
    ├── TMDB: /discover/movie?with_genres=53    → Suspenso     (lazy)
    ├── TMDB: /discover/movie?with_genres=80    → Crimen       (lazy)
    ├── TMDB: /discover/movie?with_genres=14    → Fantasía     (lazy)
    ├── TMDB: /discover/movie?with_genres=16    → Animación    (lazy)
    ├── TMDB: /discover/movie?with_genres=99    → Documentales (lazy)
    └── TMDB: /discover/movie?with_genres=10749 → Romance      (lazy)
```

> Los carruseles con `lazy` no cargan hasta que el usuario hace scroll hacia ellos (`IntersectionObserver`).

Función base de fetch TMDB (con caché en memoria):

```js
async function tmdb(endpoint) {
  if (_tmdbCache[endpoint]) return _tmdbCache[endpoint];  // caché hit
  const sep = endpoint.includes('?') ? '&' : '?';
  const res = await fetch(`${TMDB}${endpoint}${sep}api_key=${TMDB_KEY}`);
  const data = await res.json();
  _tmdbCache[endpoint] = data;   // guardar en caché
  return data;
}
```

### 4.2 Detalle de una película

```
Click en card
    │
    ▼
showDetailPage(id, 'movie')
    │
    ├── Oculta otras secciones
    ├── _preheatPlayerFrame()  → pre-conecta TCP con unlimplay.com
    │
    ▼
dpLoadDetail(id, 'movie')
    │
    └── TMDB: /movie/{id}?language=es-MX
              &append_to_response=credits,videos,recommendations,similar
                │
                ▼
            dpRenderPage(data)
                ├── Título, año, rating, géneros, sinopsis
                ├── Backdrop como fondo
                ├── Elenco (credits)
                ├── Trailer (videos → YouTube embed)
                ├── Recomendaciones (cards clickables)
                └── Botón "▶ Reproducir" → openPlayer()
```

### 4.3 Reproducción

```
openPlayer({ id, type:'movie', title, seasons })
    │
    ├── ¿Corre en app Android nativa?
    │     └── AndroidApp.openPlayer(id, 'movie', ...) → player nativo
    │
    └── ¿Corre en web/WebView?
          │
          ▼
      loadPlayerSource()
          │
          └── url = SOURCES[0].movie(id)
                  = 'https://unlimplay.com/play/embed/movie/{TMDB_ID}'
                │
                ▼
            iframe.src = url
                │
                └── unlimplay.com recibe el TMDB ID,
                    scrapea el stream en tiempo real,
                    devuelve el player embebido con el video
                    (puede tardar hasta 15–22 segundos)
```

**Mensajes de carga progresivos** (para no perder al usuario):

| Tiempo | Mensaje |
|--------|---------|
| 0s | 🔌 Conectando con el servidor... |
| 3.5s | 📡 Obteniendo el contenido... |
| 7s | 🎬 Preparando el reproductor... |
| 11s | ⏳ Casi listo, un momento... |
| 16s | 🌐 Verificando la señal... |
| 22s | ⚠️ Tardando más de lo normal... |

---

## 5. Flujo: Series

Idéntico a películas hasta `openPlayer`. La diferencia:

```
openPlayer({ id, type:'tv', seasons, startS, startE })
    │
    ├── url = SOURCES[0].tv(id, season, episode)
    │       = 'https://unlimplay.com/play/embed/tv/{id}/{season}/{episode}'
    │
    └── En paralelo:
        TMDB: /tv/{id}/season/{startSeason}
            └── Obtiene cantidad de episodios
                └── renderEpisodeControls() → controles S/E en el player
```

El player de series incluye:
- Selector de temporada/episodio
- Auto-siguiente episodio (con countdown)
- Progreso guardado por temporada y episodio

---

## 6. Flujo: Canales TV en Vivo

### 6.1 Carga inicial

```
loadIPTVChannels()
    │
    ├── ¿Hay caché en localStorage? (TTL: 24h)
    │     └── SÍ → usar caché + ir a TVLibre Sports
    │
    └── NO → fetch('https://iptv-org.github.io/iptv/index.m3u')
                │
                ▼
            parseM3U(text)
                ├── Parsea #EXTINF: name, logo, group, country, language
                └── Extrae URL del canal
                │
                ▼
            isSpanishChannel(ch)  ← filtro de idioma/país
                ├── language: es, spa, lat, castellano, mx, co, ar...
                ├── country: CO, MX, AR, ES, PE, VE, CL, EC, GT...
                └── group: contiene algún código de país hispano
                │
                ▼
            Merge con EXTRA_CHANNELS (canales hardcodeados)
                └── Si ya existe por nombre → no duplicar
                │
                ▼
            getPopularityScore() → ordena por popularidad
                │
                ▼
            Guardar en localStorage + renderizar grid
                │
                ▼
            loadTVLibreSports()   ← en paralelo
            loadTVLibreSchedule()
            loadTVLibreCategories()
```

### 6.2 TVLibre Sports API

```
GET apifutboltv.onrender.com/api/channels
    Header: x-authorizer: colombia-tv-access
    │
    ▼
Filtrar: categorias.includes('deporte')
    │
    ▼
Merge con iptvChannels:
    ├── Canal nuevo → agregar al grid
    └── Canal existente → reemplazar URL con url_decoded de TVLibre
                          (considerada más confiable)
```

### 6.3 Reproducción de un canal

```
click en canal
    │
    ▼
openChannelPlayer(channel)
    │
    └── tryChannelUrls(channel.urls)
          │
          ├── Para cada URL en orden:
          │
          ├── ¿URL contiene 'youtube.com/embed'?
          │     └── iframe.src = url
          │           ├── onload → ocultar loading
          │           └── timeout 10s → probar siguiente URL
          │
          ├── ¿URL termina en '.m3u8'?
          │     └── video nativo + hls.js
          │           ├── Hls.isSupported() → new Hls() → loadSource()
          │           ├── MANIFEST_PARSED → play + ocultar loading
          │           └── ERROR fatal → probar siguiente URL
          │
          └── Cualquier otra URL:
                └── iframe.src = url
                      ├── onload → ocultar loading
                      └── timeout 10s → probar siguiente URL
```

---

## 7. Flujo: Anime

```
Usuario abre un anime (detectado por: genre=16 + keyword=210024 en TMDB)
    │
    ▼
openPlayer({ id, type:'tv', ... })
    │
    ├── INMEDIATO: iframe con unlimplay (StreamiX server)
    │
    └── EN PARALELO: _fetchAnimeServersForEpisode(season, episode)
          │
          ├── anime1Search(título)          → busca el anime
          ├── anime1Info(best_match.url)    → obtiene episodios
          └── anime1Episode(episode.url)    → obtiene servidores del episodio
                │
                ▼
            _renderAnimeSourceSelector(servers)
                ├── 🇲🇽 StreamiX (unlimplay — siempre disponible)
                ├── 🇯🇵 SUB: [servidor1, servidor2, ...]
                └── 🇲🇽 DUB: [servidor1, servidor2, ...]
```

API Anime endpoints:

```
GET anime1v-api-cloned.onrender.com/api/v1/anime/search?q={título}&apiKey={key}
GET anime1v-api-cloned.onrender.com/api/v1/anime/info?url={url}&apiKey={key}
GET anime1v-api-cloned.onrender.com/api/v1/anime/episode?url={url}&apiKey={key}
```

---

## 8. Sistema de Reproducción (reproductor unificado)

El player maneja 3 tipos de fuente con un único motor:

| Tipo de URL | Motor | Fallback |
|-------------|-------|---------|
| `.m3u8` | `hls.js` (cargado dinámicamente) | Native `<video>` en Safari/iOS |
| `youtube.com/embed` | `<iframe>` | Timeout 10s → siguiente URL |
| Cualquier otra | `<iframe>` | Timeout 10s → siguiente URL |

```js
// Decisor de motor en tryNext():
if (url.includes('youtube.com/embed')) {
  → iframe
} else if (url.endsWith('.m3u8') || url.includes('.m3u8')) {
  → hls.js / video nativo
} else {
  → iframe genérico
}
```

`hls.js` se carga **dinámicamente** (solo cuando se necesita):
```
https://cdnjs.cloudflare.com/ajax/libs/hls.js/1.4.12/hls.min.js
```

---

## 9. Sistema de Progreso (localStorage)

Guarda automáticamente cada 15 segundos mientras se reproduce:

```js
PROG_KEY = 'sx_prog_v2'

// Estructura por ítem:
{
  "movie:12345": { pct: 67.3, title: "Inception", ts: 1716800000000 },
  "tv:1399":     { pct: 45.1, season: 2, episode: 5, title: "GOT", ts: ... }
}
```

El porcentaje se calcula en base al runtime conocido de TMDB:

```js
const elapsed = (Date.now() - playerStartTime) / 1000 / 60;  // minutos
const runtime = playerState.runtimeMin || 22;                  // fallback 22min (episodio)
const pct = Math.min((elapsed / runtime) * 100, 95);
```

**"Continuar viendo"** muestra ítems con `pct > 3%` y `pct < 97%`, ordenados por timestamp.

---

## 10. Sistema de Caché de Canales (localStorage)

```js
CK  = 'streamix_iptv_robust_v7'   // canales cacheados (JSON)
CTK = 'streamix_iptv_time_v7'     // timestamp del caché
OD  = 86400000                     // TTL: 24 horas
```

Si el caché existe y tiene menos de 24h → se usa directamente (sin fetch a iptv-org).

---

## 11. Búsqueda Unificada

```
doSearchUnified(query)
    │
    ├── Filtro local sobre iptvChannels (nombre, país, grupo)
    │     └── Hasta 50 resultados, ordenados por popularidad
    │
    └── TMDB: /search/multi?query={q}&language=es-MX
                └── Filtra: solo movies y series (excluye personas)
                    Requiere: poster_path o nombre
    │
    ▼
Renderiza sección "📺 Canales TV" + sección "🎬 Películas & Series"
```

---

## 12. Canales EXTRA (hardcodeados)

Canales con URLs fijas definidos directamente en el código (`EXTRA_CHANNELS`):

| Canal | País | Fuente |
|-------|------|--------|
| Caracol Noticias | CO | YouTube embed |
| Canal RCN | CO | YouTube embed |
| Señal Colombia | CO | YouTube embed |
| Teleantioquia | CO | YouTube embed |
| CNN en Español | Internacional | YouTube embed |
| France 24 Español | Internacional | YouTube embed |
| RT en Español | Internacional | YouTube embed |
| NTN24 | CO | YouTube embed |
| TN Todo Noticias | AR | YouTube embed |
| Telefe | AR | YouTube embed |
| Telemundo | US | YouTube embed |
| Univision | US | YouTube embed |
| Cartoon Network LA | Internacional | YouTube embed |
| Nickelodeon LA | Internacional | YouTube embed |
| DW Español | Internacional | `.m3u8` directo (Akamai) |
| RTVE 24h | ES | `.m3u8` directo (Akamai) |
| Canal Once MX | MX | `.m3u8` directo |
| ESPN 1/2/3 | CO | pelotalibretv2.pl |
| Fox Sports 1/2/3 | CO | pelotalibretv2.pl |
| Win Sports+ | CO | pelotalibretv2.pl |
| TyC Sports | AR | pelotalibretv2.pl |
| DSports / DirecTV | CO | pelotalibretv2.pl |

---

## 13. Mi Lista (Watchlist)

```js
KEY = 'sx_mylist'   // array de IDs TMDB como strings

// Ejemplo:
["12345", "67890", "11223"]
```

Operaciones:
- **Agregar:** `dpMyList.push(id)` → guardar en localStorage
- **Quitar:** `dpMyList.splice(idx, 1)` → guardar en localStorage
- **Renderizar:** consulta TMDB por cada ID para obtener poster/título

---

## 14. Agenda Deportiva (TVLibre)

```
GET apifutboltv.onrender.com/api/schedule/today
    Header: x-authorizer: colombia-tv-access
    │
    └── Retorna eventos del día:
        { hora, nombre_evento, canales: [{ nombre }] }
        │
        └── Renderiza en sección "Agenda" con badges clickables
            (cada badge → playTVLibreChannel(nombre))
```

---

## 15. Algoritmo de Popularidad

Usado para ordenar los canales IPTV:

```js
function getPopularityScore(ch) {
  let score = 0;
  // Keywords conocidos → +100
  if (['caracol','rcn','cnn','espn','telemundo',...].some(k => name.includes(k)))
    score += 100;
  // Por grupo:
  if (group.includes('noticias'))    score += 40;
  if (group.includes('deportes'))    score += 50;
  if (group.includes('entretenimiento')) score += 20;
  // Por país principal:
  if (['CO','MX','AR','ES','US'].includes(country)) score += 10;
  // Tiene logo:
  if (logo && logo.length > 10) score += 5;
  return score;
}
```

---

## 16. Diagrama Completo de la App

```
┌─────────────────────────────────────────────────────────┐
│                      STREAMIX 3.4                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  HOME                                                   │
│  ├── Trending (TMDB)                                    │
│  ├── Continuar viendo (localStorage)                    │
│  └── Agenda deportiva (TVLibre API)                     │
│                                                         │
│  PELÍCULAS ──── TMDB API ──── unlimplay.com embed       │
│                                                         │
│  SERIES ──────── TMDB API ──── unlimplay.com embed      │
│                  (+ episodios por temporada)            │
│                                                         │
│  ANIME ─────────  TMDB API ──── unlimplay.com           │
│                  + anime1v API (SUB/DUB)                │
│                                                         │
│  CANALES TV                                             │
│  ├── iptv-org M3U (canales hispanos filtrados)          │
│  ├── EXTRA_CHANNELS (hardcodeados)                      │
│  │   ├── YouTube embeds                                 │
│  │   ├── .m3u8 directos (DW, RTVE, Canal Once)         │
│  │   └── pelotalibretv2.pl (deportes)                  │
│  └── TVLibre API (deportes dinámicos)                   │
│                                                         │
│  BÚSQUEDA ─────── TMDB /search/multi + filtro local     │
│                                                         │
│  PERSISTENCIA (localStorage)                            │
│  ├── sx_prog_v2          → progreso de reproducción     │
│  ├── sx_mylist           → Mi Lista                     │
│  └── streamix_iptv_*     → caché de canales (24h)       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 17. Limitaciones en Entorno Web Local

| Función | En Android APK | En localhost |
|---------|---------------|--------------|
| Catálogo TMDB | ✅ | ✅ |
| Reproductor películas/series | ✅ | ✅ (unlimplay iframe) |
| Canales TV .m3u8 | ✅ | ✅ |
| YouTube embeds | ✅ | ✅ |
| Player nativo Android | ✅ | ❌ (`AndroidApp` no existe) |
| localStorage | ✅ | ✅ |
| Anime SUB/DUB | ✅ | ✅ |

> En web, cuando `window.AndroidApp` no existe, el player web toma el control automáticamente — no hay error, simplemente usa el iframe en lugar del player nativo.
