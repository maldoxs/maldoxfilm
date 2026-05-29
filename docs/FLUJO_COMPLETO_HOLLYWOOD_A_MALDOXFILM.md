# De Hollywood a MaldoxFilm — El proceso completo

> Cómo una película pasa de ser anunciada en Hollywood a reproducirse en MaldoxFilm.
> Todo este proceso ocurre en menos de 3 segundos cuando haces clic en reproducir.

---

## El diagrama completo

```
╔══════════════════════════════════════════════════════════════╗
║                    1. NACE LA PELÍCULA                       ║
║                                                              ║
║   Disney / Warner / Netflix / estudio independiente          ║
║   anuncia oficialmente que está produciendo una película     ║
╚══════════════════════════════════════════════════════════════╝
                            ↓
                    ¿Quién se entera?
                    ↙            ↘
          Estudio avisa         IMDB monitorea
          voluntariamente       festivales, noticias,
          (les conviene)        sindicatos de actores

╔══════════════════════════════════════════════════════════════╗
║                    2. AMAZON / IMDB                          ║
║                                                              ║
║   "El Registro Civil del cine"                               ║
║                                                              ║
║   Recibe la info → asigna ID único → publica el registro     ║
║                                                              ║
║   tt9876543  =  Avengers 5 (2026)                            ║
║     ├── Director                                             ║
║     ├── Actores                                              ║
║     ├── Género / duración                                    ║
║     └── Calificación de usuarios (cuando se estrena)         ║
╚══════════════════════════════════════════════════════════════╝
                            ↓
              TMDB detecta el nuevo tt·······

╔══════════════════════════════════════════════════════════════╗
║                    3. TMDB                                   ║
║                                                              ║
║   "El periodista visual del cine"                            ║
║                                                              ║
║   Crea su propio registro con más datos:                     ║
║                                                              ║
║   tmdb_id: 123456                                            ║
║     ├── imdb_id: tt9876543  ← guarda el de IMDB              ║
║     ├── Portada oficial (poster)                             ║
║     ├── Imágenes de fondo                                    ║
║     ├── Trailer de YouTube                                   ║
║     ├── Plataformas disponibles (Netflix, Disney+...)        ║
║     ├── Colecciones / sagas                                  ║
║     └── Traducido a 40+ idiomas                              ║
╚══════════════════════════════════════════════════════════════╝
                            ↓
              La película existe en ambas bases de datos
              ↓                              ↓
        IMDB ID universal              TMDB con API gratis
        (todo el mundo lo usa)         (los desarrolladores lo usan)


════════════════════════════════════════════════════════════════
              AHORA ENTRA EL USUARIO EN MALDOXFILM
════════════════════════════════════════════════════════════════

╔══════════════════════════════════════════════════════════════╗
║                    4. MALDOXFILM                             ║
║                                                              ║
║   Usuario busca "Avengers 5"                                 ║
║        ↓                                                     ║
║   Consulta TMDB API  →  devuelve tmdb_id: 123456             ║
║   con portada, actores, descripción                          ║
║        ↓                                                     ║
║   Muestra catálogo bonito al usuario                         ║
║        ↓                                                     ║
║   Usuario hace clic en REPRODUCIR                            ║
║        ↓                                                     ║
║   Pide a TMDB: "dame el IMDB ID de 123456"                   ║
║        ↓                                                     ║
║   TMDB responde: "tt9876543"                                 ║
╚══════════════════════════════════════════════════════════════╝
                            ↓

╔══════════════════════════════════════════════════════════════╗
║                    5. TORRENTIO                              ║
║                                                              ║
║   MaldoxFilm pregunta:                                       ║
║   "¿Tienes tt9876543 cacheado en Real-Debrid?"               ║
║        ↓                                                     ║
║   Torrentio revisa el caché de RD con tu token               ║
║        ↓                                                     ║
║   Devuelve lista de streams disponibles:                     ║
║     ├── Avengers5.2026.2160p.x265.mkv  →  URL               ║
║     ├── Avengers5.2026.1080p.x264.mkv  →  URL               ║
║     └── Avengers5.2026.720p.mkv        →  URL               ║
╚══════════════════════════════════════════════════════════════╝
                            ↓

╔══════════════════════════════════════════════════════════════╗
║                    6. REAL-DEBRID                            ║
║                                                              ║
║   MaldoxFilm elige el mejor stream                           ║
║        ↓                                                     ║
║   Si es x265 → pide transcode a RD                          ║
║        ↓                    ↓                               ║
║   RD convierte          RD devuelve                          ║
║   x265 → H.264          URL HLS (.m3u8)                      ║
╚══════════════════════════════════════════════════════════════╝
                            ↓

╔══════════════════════════════════════════════════════════════╗
║                    7. REPRODUCTOR                            ║
║                                                              ║
║   HLS.js reproduce el stream en el navegador                 ║
║                                                              ║
║              🎬 Usuario viendo la película ✅                 ║
╚══════════════════════════════════════════════════════════════╝
```

---

## ¿Quién hace qué? — Resumen de roles

| Pieza | Rol | Analogía |
|---|---|---|
| **Hollywood / Estudio** | Crea la película y avisa | El hospital que reporta un nacimiento |
| **IMDB (Amazon)** | Asigna el ID universal | El Registro Civil |
| **TMDB** | Enriquece con datos visuales y API gratis | El periodista visual |
| **Torrentio** | Busca el video entre torrents y verifica cuáles tiene RD | El catálogo del almacén |
| **Real-Debrid** | Tiene el archivo descargado y lo sirve — no busca nada | El almacén |
| **HLS.js** | Reproduce el stream en el navegador | El proyector |
| **MaldoxFilm** | Elige la mejor calidad y coordina todo | El cine completo |

---

## ⚠️ Confusión frecuente — RD vs Torrentio

**RD NO busca videos. Torrentio es el que busca.**

```
TORRENTIO   →  el que BUSCA y ENCUENTRA
                "¿Existe tt9876543 en algún torrent?"
                "¿Está ese torrent en el caché de RD?"
                Devuelve la lista de URLs disponibles

REAL-DEBRID →  el que TIENE y SIRVE
                No busca nada
                Solo entrega lo que ya tiene descargado
```

Torrentio devuelve varias versiones del mismo film:
```
  ├── 2160p x265 (4K)   →  URL
  ├── 1080p x264        →  URL
  ├── 720p x264         →  URL
  └── ...
```

**MaldoxFilm** elige la mejor de esa lista — no RD ni Torrentio:
```javascript
// Lógica de selección en MaldoxFilm:
1. RD + H264  →  compatible con Chrome sin transcode
2. RD + AAC   →  buen audio
3. Cualquier RD
4. Lo que haya
```

> RD es como un almacén con miles de películas ya descargadas.
> Torrentio es el catálogo que sabe qué hay en ese almacén.
> MaldoxFilm decide qué sacar del almacén.

---

## ¿Por qué dos IDs (IMDB + TMDB)?

```
IMDB  →  no tiene API gratuita para desarrolladores ❌
TMDB  →  API gratuita con portadas, trailers y más  ✅
          PERO guarda el IMDB ID internamente

Por eso MaldoxFilm:
  Usa TMDB para mostrar     →  portadas, búsqueda, catálogo
  Usa IMDB para buscar      →  streams en Torrentio y RD
```

---

## Dato clave

> Los estudios NO están obligados a registrar sus películas en IMDB.
> Lo hacen voluntariamente porque IMDB es donde millones de personas
> buscan información — es publicidad gratis para ellos.
> IMDB también monitorea festivales, noticias y sindicatos
> para registrar películas aunque nadie avise.
