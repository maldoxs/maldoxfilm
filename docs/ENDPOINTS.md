# 🔌 Endpoints Completos — Streamix 3.4

> Todos los endpoints con ejemplos de invocación reales listos para usar en browser, curl o fetch()

---

## 📌 Credenciales y Base URLs

```
TMDB_KEY  = d95e6a7e2ead40e949fcdb81f1f26f0b
TMDB_BASE = https://api.themoviedb.org/3
IMG_BASE  = https://image.tmdb.org/t/p/
LANG      = es-MX

ANIME_API = https://anime1v-api-cloned.onrender.com
ANIME_KEY = 86d6451302f2150096aa561c7311ed6f

TVLIBRE   = https://apifutboltv.onrender.com/api
TV_AUTH   = x-authorizer: colombia-tv-access

PLAYER    = https://unlimplay.com
IPTV_LIST = https://iptv-org.github.io/iptv/index.m3u
```

> **IDs de ejemplo usados en esta doc:**
> - Inception (película) → `27205`
> - Breaking Bad (serie) → `1396`
> - Attack on Titan (anime) → `1429`

---

## 🎬 TMDB — Películas

### Listas generales

#### Tendencias de la semana
```
# Browser / Postman
https://api.themoviedb.org/3/trending/movie/week?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b

# curl
curl "https://api.themoviedb.org/3/trending/movie/week?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b"

# fetch() JS
const data = await fetch('https://api.themoviedb.org/3/trending/movie/week?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b').then(r => r.json());
```

#### Tendencias del día
```
https://api.themoviedb.org/3/trending/movie/day?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Todo en tendencia (películas + series)
```
https://api.themoviedb.org/3/trending/all/week?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Populares
```
https://api.themoviedb.org/3/movie/popular?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Mejor valoradas
```
https://api.themoviedb.org/3/movie/top_rated?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### En cartelera ahora
```
https://api.themoviedb.org/3/movie/now_playing?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Próximos estrenos
```
https://api.themoviedb.org/3/movie/upcoming?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

---

### Películas por género (`/discover/movie`)

| Género | ID | URL completa |
|--------|----|-------------|
| Acción | 28 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=28&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Comedia | 35 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=35&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Drama | 18 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=18&sort_by=vote_count.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Terror | 27 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=27&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Sci-Fi | 878 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=878&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Thriller | 53 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=53&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Crimen | 80 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=80&sort_by=vote_count.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Fantasía | 14 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=14&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Animación | 16 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=16&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Documental | 99 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=99&sort_by=vote_average.desc&vote_count.gte=200&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Romance | 10749 | `https://api.themoviedb.org/3/discover/movie?language=es-MX&with_genres=10749&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |

---

### Detalle de película

#### Información completa (con elenco, trailers, recomendaciones)
```
# Ejemplo: Inception (id = 27205)
https://api.themoviedb.org/3/movie/27205?language=es-MX&append_to_response=credits,videos,recommendations,similar&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b

# curl
curl "https://api.themoviedb.org/3/movie/27205?language=es-MX&append_to_response=credits,videos,recommendations,similar&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b"

# fetch() JS
const movie = await fetch('https://api.themoviedb.org/3/movie/27205?language=es-MX&append_to_response=credits,videos,recommendations,similar&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b').then(r => r.json());
console.log(movie.title, movie.vote_average);
```

**Campos clave de la respuesta:**
```json
{
  "id": 27205,
  "title": "Inception",
  "overview": "...",
  "poster_path": "/9gk7adHYeDvHkCSEqAvQNLV5Uge.jpg",
  "backdrop_path": "/s3TBrRGB1iav7gFOCNx3H31MoES.jpg",
  "release_date": "2010-07-14",
  "vote_average": 8.4,
  "runtime": 148,
  "genres": [{ "id": 28, "name": "Acción" }],
  "credits": { "cast": [...], "crew": [...] },
  "videos": { "results": [{ "key": "YoHD9XEInc0", "type": "Trailer" }] },
  "recommendations": { "results": [...] }
}
```

---

### Reproducción de película

```
# Reproductor embed (se abre en iframe)
https://unlimplay.com/play/embed/movie/27205

# Patrón general
https://unlimplay.com/play/embed/movie/{TMDB_ID}
```

---

## 📺 TMDB — Series

### Listas generales

#### Tendencias de la semana
```
https://api.themoviedb.org/3/trending/tv/week?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Populares
```
https://api.themoviedb.org/3/tv/popular?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Mejor valoradas
```
https://api.themoviedb.org/3/tv/top_rated?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### En emisión actualmente
```
https://api.themoviedb.org/3/tv/on_the_air?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Emiten hoy
```
https://api.themoviedb.org/3/tv/airing_today?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

---

### Series por plataforma

| Plataforma | ID Red | URL completa |
|-----------|--------|-------------|
| Netflix | 213 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_networks=213&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Amazon Prime | 1024 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_networks=1024&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| HBO | 49 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_networks=49&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Disney+ | 2739 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_networks=2739&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Apple TV+ | 2552 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_networks=2552&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |

---

### Series por género

| Género | ID | URL completa |
|--------|----|-------------|
| Acción y Aventura | 10759 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=10759&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Drama | 18 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=18&sort_by=vote_count.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Comedia | 35 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=35&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Crimen | 80 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=80&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Sci-Fi y Fantasía | 10765 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=10765&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Misterio | 9648 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=9648&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Animación | 16 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=16&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |
| Documental | 99 | `https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=99&sort_by=vote_average.desc&vote_count.gte=100&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b` |

---

### Detalle de serie

#### Información completa
```
# Ejemplo: Breaking Bad (id = 1396)
https://api.themoviedb.org/3/tv/1396?language=es-MX&append_to_response=credits,videos,recommendations,similar&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b

# curl
curl "https://api.themoviedb.org/3/tv/1396?language=es-MX&append_to_response=credits,videos,recommendations,similar&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b"
```

**Campos clave de la respuesta:**
```json
{
  "id": 1396,
  "name": "Breaking Bad",
  "number_of_seasons": 5,
  "number_of_episodes": 62,
  "first_air_date": "2008-01-20",
  "vote_average": 9.5,
  "poster_path": "/ggFHVNu6YYI5L9pCfOacjizRGt.jpg",
  "credits": { "cast": [...] },
  "videos": { "results": [...] },
  "recommendations": { "results": [...] }
}
```

#### Episodios de una temporada
```
# Breaking Bad Temporada 1
https://api.themoviedb.org/3/tv/1396/season/1?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b

# fetch() JS
const season = await fetch('https://api.themoviedb.org/3/tv/1396/season/1?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b').then(r => r.json());
console.log(season.episodes.length); // cantidad de episodios
```

#### Detalle de episodio específico
```
# Breaking Bad S1E1
https://api.themoviedb.org/3/tv/1396/season/1/episode/1?language=es-MX&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

**Campos clave:**
```json
{
  "episode_number": 1,
  "name": "Pilot",
  "overview": "...",
  "runtime": 58,
  "still_path": "/..."
}
```

---

### Reproducción de serie

```
# Breaking Bad S2E3
https://unlimplay.com/play/embed/tv/1396/2/3

# Patrón general
https://unlimplay.com/play/embed/tv/{TMDB_ID}/{SEASON}/{EPISODE}
```

---

## 🎌 TMDB — Anime

> Filtro combinado: `with_genres=16` (Animación) + `with_keywords=210024` (anime japonés)

#### Anime popular
```
https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=16&with_keywords=210024&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Anime en emisión reciente (últimos 7 días)
```
https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=16&with_keywords=210024&with_status=0&air_date.gte=2026-05-18&sort_by=first_air_date.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Anime próximos a estrenar
```
https://api.themoviedb.org/3/discover/tv?language=es-MX&with_genres=16&with_keywords=210024&first_air_date.gte=2026-05-25&sort_by=popularity.desc&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b
```

#### Reproducción de anime (igual que serie)
```
# Attack on Titan S1E1 (id = 1429)
https://unlimplay.com/play/embed/tv/1429/1/1
```

---

## 🍿 Anime API — anime1v-api-cloned.onrender.com

### 1. Buscar anime
```
# Browser / Postman
https://anime1v-api-cloned.onrender.com/api/v1/anime/search?q=attack+on+titan&apiKey=86d6451302f2150096aa561c7311ed6f

# curl
curl "https://anime1v-api-cloned.onrender.com/api/v1/anime/search?q=attack+on+titan&apiKey=86d6451302f2150096aa561c7311ed6f"

# fetch() JS
const results = await fetch('https://anime1v-api-cloned.onrender.com/api/v1/anime/search?q=attack+on+titan&apiKey=86d6451302f2150096aa561c7311ed6f').then(r => r.json());
const bestMatch = results.data.results[0];
console.log(bestMatch.url); // → usar en el siguiente endpoint
```

**Respuesta:**
```json
{
  "data": {
    "results": [
      { "title": "Attack on Titan", "url": "https://anime1v.com/anime/shingeki-no-kyojin/" }
    ]
  }
}
```

### 2. Info del anime (lista de episodios)
```
# Browser / Postman
https://anime1v-api-cloned.onrender.com/api/v1/anime/info?url=https://anime1v.com/anime/shingeki-no-kyojin/&apiKey=86d6451302f2150096aa561c7311ed6f

# curl
curl "https://anime1v-api-cloned.onrender.com/api/v1/anime/info?url=https%3A%2F%2Fanime1v.com%2Fanime%2Fshingeki-no-kyojin%2F&apiKey=86d6451302f2150096aa561c7311ed6f"
```

**Respuesta:**
```json
{
  "data": {
    "title": "Attack on Titan",
    "episodes": [
      { "number": "1", "url": "https://anime1v.com/anime/shingeki-no-kyojin/ep-1/" }
    ]
  }
}
```

### 3. Servidores del episodio (links SUB / DUB)
```
# Browser / Postman
https://anime1v-api-cloned.onrender.com/api/v1/anime/episode?url=https://anime1v.com/anime/shingeki-no-kyojin/ep-1/&apiKey=86d6451302f2150096aa561c7311ed6f

# curl
curl "https://anime1v-api-cloned.onrender.com/api/v1/anime/episode?url=https%3A%2F%2Fanime1v.com%2Fanime%2Fshingeki-no-kyojin%2Fep-1%2F&apiKey=86d6451302f2150096aa561c7311ed6f"
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "streamLinks": {
      "SUB": [{ "server": "Zoro", "url": "https://..." }],
      "DUB": [{ "server": "Gogoanime", "url": "https://..." }]
    }
  }
}
```

### Flujo completo en JS
```js
const KEY = '86d6451302f2150096aa561c7311ed6f';
const BASE = 'https://anime1v-api-cloned.onrender.com';

// 1. Buscar
const search = await fetch(`${BASE}/api/v1/anime/search?q=naruto&apiKey=${KEY}`).then(r => r.json());
const animeUrl = search.data.results[0].url;

// 2. Obtener episodios
const info = await fetch(`${BASE}/api/v1/anime/info?url=${encodeURIComponent(animeUrl)}&apiKey=${KEY}`).then(r => r.json());
const epUrl = info.data.episodes[0].url; // episodio 1

// 3. Obtener stream
const ep = await fetch(`${BASE}/api/v1/anime/episode?url=${encodeURIComponent(epUrl)}&apiKey=${KEY}`).then(r => r.json());
const streamUrl = ep.data.streamLinks.SUB[0].url;
console.log(streamUrl); // → URL final del video
```

---

## ⚽ TVLibre API — apifutboltv.onrender.com

> **Header requerido en todas las peticiones:** `x-authorizer: colombia-tv-access`

### Todos los canales
```
# curl
curl -H "x-authorizer: colombia-tv-access" https://apifutboltv.onrender.com/api/channels

# fetch() JS
const channels = await fetch('https://apifutboltv.onrender.com/api/channels', {
  headers: { 'x-authorizer': 'colombia-tv-access' }
}).then(r => r.json());

// Filtrar solo deportes
const sports = channels.filter(ch =>
  ch.categorias.some(c => c.toLowerCase().includes('deporte'))
);
```

**Respuesta:**
```json
[
  {
    "nombre": "ESPN",
    "categorias": ["Deportes"],
    "url": "https%3A%2F%2Fstream...",
    "url_decoded": "https://stream-directo.m3u8"
  }
]
```

### Canal específico
```
# curl
curl -H "x-authorizer: colombia-tv-access" https://apifutboltv.onrender.com/api/channels/ESPN
```

### Categorías disponibles
```
# curl
curl -H "x-authorizer: colombia-tv-access" https://apifutboltv.onrender.com/api/channels/categories

# fetch() JS
const cats = await fetch('https://apifutboltv.onrender.com/api/channels/categories', {
  headers: { 'x-authorizer': 'colombia-tv-access' }
}).then(r => r.json());
```

### Agenda deportiva de hoy
```
# curl
curl -H "x-authorizer: colombia-tv-access" https://apifutboltv.onrender.com/api/schedule/today

# fetch() JS
const schedule = await fetch('https://apifutboltv.onrender.com/api/schedule/today', {
  headers: { 'x-authorizer': 'colombia-tv-access' }
}).then(r => r.json());

schedule.forEach(ev => console.log(`${ev.hora} - ${ev.nombre_evento}`));
```

**Respuesta:**
```json
[
  {
    "hora": "20:00",
    "nombre_evento": "Colombia vs Brasil",
    "canales": [
      { "nombre": "ESPN" },
      { "nombre": "Win Sports+" }
    ]
  }
]
```

---

## 🔍 TMDB — Búsqueda unificada

```
# Buscar "breaking bad"
https://api.themoviedb.org/3/search/multi?language=es-MX&query=breaking+bad&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b

# Buscar "inception"
https://api.themoviedb.org/3/search/multi?language=es-MX&query=inception&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b

# curl
curl "https://api.themoviedb.org/3/search/multi?language=es-MX&query=breaking+bad&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b"

# fetch() JS
const q = 'breaking bad';
const results = await fetch(`https://api.themoviedb.org/3/search/multi?language=es-MX&query=${encodeURIComponent(q)}&api_key=d95e6a7e2ead40e949fcdb81f1f26f0b`).then(r => r.json());

// Filtrar solo películas y series (excluir personas)
const filtered = results.results.filter(i => i.media_type !== 'person' && (i.poster_path || i.name || i.title));
```

**Respuesta:**
```json
{
  "results": [
    {
      "id": 1396,
      "media_type": "tv",
      "name": "Breaking Bad",
      "poster_path": "/ggFHVNu6YYI5L9pCfOacjizRGt.jpg",
      "vote_average": 9.5
    }
  ]
}
```

---

## 🖼️ TMDB — Imágenes

```
# Poster pequeño (listas, carruseles en TV)
https://image.tmdb.org/t/p/w185/ggFHVNu6YYI5L9pCfOacjizRGt.jpg

# Poster mediano (móvil/desktop)
https://image.tmdb.org/t/p/w342/ggFHVNu6YYI5L9pCfOacjizRGt.jpg

# Poster grande
https://image.tmdb.org/t/p/w500/ggFHVNu6YYI5L9pCfOacjizRGt.jpg

# Backdrop (fondo de la página de detalle)
https://image.tmdb.org/t/p/original/tsRy63Mu5cu8etL1X7ZLyf7UP1M.jpg

# Patrón general
https://image.tmdb.org/t/p/{tamaño}{poster_path_del_response}
```

| Tamaño | Uso |
|--------|-----|
| `w185` | Miniaturas TV mode |
| `w342` | Posters móvil/desktop |
| `w500` | Posters grandes |
| `original` | Backdrops (fondo detalle) |

---

## 📡 Canales TV en Vivo

### Lista IPTV master (iptv-org)
```
# curl
curl https://iptv-org.github.io/iptv/index.m3u

# fetch() JS
const m3u = await fetch('https://iptv-org.github.io/iptv/index.m3u').then(r => r.text());
```

### Streams .m3u8 directos

```
# DW Español — pegar directo en VLC o hls-js demo
https://dwamdstream102.akamaized.net/hls/live/2015525/dwstream102/index.m3u8

# RTVE 24h
https://rtvelivestream.akamaized.net/rtvesec/24h/24h_main.m3u8

# Canal Once MX
https://live.canaloncelive.tv/livepkgr2/smil:internacional.smil/playlist.m3u8

# Testear con curl
curl -I https://dwamdstream102.akamaized.net/hls/live/2015525/dwstream102/index.m3u8
```

### Canales deportivos — pelotalibretv2.pl

```
# Abrir en browser (devuelve página con player embed)
https://pelotalibretv2.pl/en-vivo/espn-1.php
https://pelotalibretv2.pl/en-vivo/fox-sports.php
https://pelotalibretv2.pl/en-vivo/win-sports-plus.php
https://pelotalibretv2.pl/en-vivo/directv-sports.php
https://pelotalibretv2.pl/en-vivo/tyc-sports.php
https://pelotalibretv2.pl/en-vivo/tnt-sports.php
https://pelotalibretv2.pl/en-vivo/tnt-sports-chile.php
https://pelotalibretv2.pl/en-vivo/tudn.php
https://pelotalibretv2.pl/en-vivo/gol-tv.php
```

### YouTube embeds (canales en vivo)

```
# Patrón
https://www.youtube.com/embed/live_stream?channel={CHANNEL_ID}&autoplay=1

# Ejemplos
https://www.youtube.com/embed/live_stream?channel=UC2Xq2PK-got3Rtz9ZJ32hLQ&autoplay=1  → Caracol Noticias
https://www.youtube.com/embed/live_stream?channel=UCSCAJJ1RJ4rfRTVW4gjYTpQ&autoplay=1  → Canal RCN
https://www.youtube.com/embed/live_stream?channel=UC_lEiu6917IJz03TnntWUaQ&autoplay=1  → CNN en Español
https://www.youtube.com/embed/live_stream?channel=UCUdOoVWuWmgo1wByzcsyKDQ&autoplay=1  → France 24
https://www.youtube.com/embed/live_stream?channel=UCEKvLP5nC_9HlnSTR-gkp4A&autoplay=1  → RT en Español
https://www.youtube.com/embed/live_stream?channel=UCT4Jg8h03dD0iN3Pb5L0PMA&autoplay=1  → TN Todo Noticias
https://www.youtube.com/embed/live_stream?channel=UCUP6qv-_EIL0hwTsJaKYnvw&autoplay=1  → Telefe
https://www.youtube.com/embed/live_stream?channel=UCajrNVKvG2xVlVcSr3GcsDA&autoplay=1  → Telemundo
https://www.youtube.com/embed/live_stream?channel=UCpjkQFR6E1cBoAuFMwnNIJw&autoplay=1  → TVN Chile
```

---

## 🎥 Reproductor — unlimplay.com

```
# Película: Inception
https://unlimplay.com/play/embed/movie/27205

# Serie: Breaking Bad S3E7
https://unlimplay.com/play/embed/tv/1396/3/7

# Anime: Attack on Titan S1E1
https://unlimplay.com/play/embed/tv/1429/1/1

# Patrón película
https://unlimplay.com/play/embed/movie/{TMDB_MOVIE_ID}

# Patrón serie/anime
https://unlimplay.com/play/embed/tv/{TMDB_TV_ID}/{SEASON}/{EPISODE}
```

> unlimplay recibe el TMDB ID, scrapea el stream y lo devuelve en el iframe. Demora 10–22 segundos.

---

## 💾 localStorage — Claves de persistencia

| Clave | Contenido | TTL |
|-------|-----------|-----|
| `sx_prog_v2` | Progreso de reproducción `{ "movie:27205": { pct: 67, title: "Inception" } }` | Permanente |
| `sx_mylist` | Watchlist `["27205", "1396"]` | Permanente |
| `streamix_iptv_robust_v7` | JSON de canales cacheados | 24 horas |
| `streamix_iptv_time_v7` | Timestamp del caché | 24 horas |

```js
// Leer progreso en consola del browser
JSON.parse(localStorage.getItem('sx_prog_v2'))

// Leer Mi Lista
JSON.parse(localStorage.getItem('sx_mylist'))

// Limpiar caché de canales (fuerza refetch)
localStorage.removeItem('streamix_iptv_robust_v7')
localStorage.removeItem('streamix_iptv_time_v7')
```

---

## 🗂️ Referencia rápida — IDs TMDB

### Géneros películas
| ID | Género | ID | Género |
|----|--------|----|--------|
| 28 | Acción | 14 | Fantasía |
| 35 | Comedia | 16 | Animación |
| 18 | Drama | 99 | Documental |
| 27 | Terror | 10749 | Romance |
| 878 | Sci-Fi | 53 | Thriller |
| 80 | Crimen | — | — |

### Géneros series
| ID | Género | ID | Género |
|----|--------|----|--------|
| 10759 | Acción y Aventura | 10765 | Sci-Fi y Fantasía |
| 18 | Drama | 9648 | Misterio |
| 35 | Comedia | 16 | Animación |
| 80 | Crimen | 99 | Documental |

### Redes/Plataformas
| ID | Plataforma |
|----|-----------|
| 213 | Netflix |
| 1024 | Amazon Prime Video |
| 49 | HBO |
| 2739 | Disney+ |
| 2552 | Apple TV+ |
