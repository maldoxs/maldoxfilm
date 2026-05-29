# MaldoxFilm — Esquema General del Sistema

> Diagrama completo de todo el flujo: desde que se crea una película en Hollywood
> hasta que se reproduce en el navegador.

---

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                         EL MUNDO DEL CINE                                   ║
║                                                                              ║
║   Disney · Warner · Netflix · Sony · Estudio independiente                   ║
║   Anuncian / estrenan una película oficialmente                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
                    ↓ avisan voluntariamente (les conviene)
                    ↓ o IMDB lo detecta solo (festivales, noticias)
╔══════════════════════════════════════════════════════════════════════════════╗
║                    IMDB — "El Registro Civil del Cine"                       ║
║                              (Amazon, 1990)                                  ║
║                                                                              ║
║   Asigna ID universal único e inmutable:                                     ║
║   tt0078748 = Alien (1979)                                                   ║
║   tt0068646 = El Padrino (1972)                                              ║
║                                                                              ║
║   Registra: título · director · actores · año · duración · rating            ║
╚══════════════════════════════════════════════════════════════════════════════╝
                    ↓ comunidad detecta nuevo tt········
╔══════════════════════════════════════════════════════════════════════════════╗
║                    TMDB — "El Periodista Visual"                             ║
║                         (comunidad abierta, 2008)                            ║
║                                                                              ║
║   tmdb_id: 27205                                                             ║
║     ├── imdb_id: tt0078748  ← guarda el de IMDB como puente                 ║
║     ├── Portada / poster oficial                                             ║
║     ├── Imágenes de fondo                                                   ║
║     ├── Trailer de YouTube                                                   ║
║     ├── Plataformas disponibles                                              ║
║     ├── Colecciones / sagas                                                  ║
║     └── API GRATUITA para desarrolladores ✅                                 ║
╚══════════════════════════════════════════════════════════════════════════════╝
                    ↓
═══════════════════════════════════════════════════════════════════════════════
                      USUARIO ABRE MALDOXFILM
═══════════════════════════════════════════════════════════════════════════════
                    ↓
╔══════════════════════════════════════════════════════════════════════════════╗
║                    MALDOXFILM — CATÁLOGO                                     ║
║                                                                              ║
║   Consulta TMDB API con tmdb_id                                              ║
║   Muestra: portada · título · descripción · actores · trailer                ║
║                                                                              ║
║   ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐                   ║
║   │  Alien   │  │Padrino   │  │ Avatar   │  │   ...    │                   ║
║   │  🎬      │  │  🎬      │  │   🎬     │  │          │                   ║
║   └──────────┘  └──────────┘  └──────────┘  └──────────┘                   ║
║                                                                              ║
║              Usuario hace clic en "Alien" → REPRODUCIR                      ║
╚══════════════════════════════════════════════════════════════════════════════╝
                    ↓ pide IMDB ID a TMDB
                    ↓ TMDB responde: tt0078748
╔══════════════════════════════════════════════════════════════════════════════╗
║                    TORRENTIO — "El Catálogo del Almacén"                     ║
║                                                                              ║
║   Recibe: tt0078748                                                          ║
║   Busca en índices de torrents del mundo                                     ║
║                                                                              ║
║   Encuentra estas versiones:                                                 ║
║   ┌────────────────────────────────────────────┬──────────┐                 ║
║   │ Alien.1979.2160p.BluRay.x265.DTS.mkv       │ hash A   │                 ║
║   │ Alien.1979.1080p.BluRay.x264.AAC.mp4       │ hash B   │                 ║
║   │ Alien.Directors.Cut.1979.1080p.x264.AAC    │ hash C   │                 ║
║   │ Alien.1979.720p.x264.MP3.mkv               │ hash D   │                 ║
║   └────────────────────────────────────────────┴──────────┘                 ║
║                                                                              ║
║   Pregunta a RD por cada hash:                                               ║
║   "¿Tienes este torrent descargado?"                                         ║
║   hash A → ✅  hash B → ✅  hash C → ✅  hash D → ❌                        ║
║                                                                              ║
║   Devuelve solo los que RD tiene, con URLs directas                          ║
╚══════════════════════════════════════════════════════════════════════════════╝
                    ↓ lista de streams disponibles en RD
╔══════════════════════════════════════════════════════════════════════════════╗
║              MALDOXFILM — SELECCIÓN DE LA MEJOR VERSIÓN                      ║
║                                                                              ║
║   Lee el nombre de cada archivo y puntúa:                                    ║
║                                                                              ║
║   IMAGEN                          AUDIO                                      ║
║   ──────                          ─────                                      ║
║   x264 → Chrome ✅                AAC  → Chrome ✅                           ║
║   x265 → Chrome ❌ (transcode)    DTS  → Chrome ❌                           ║
║   2160p > 1080p > 720p            AC3  → Chrome ⚠️                          ║
║                                                                              ║
║   PRIORIDAD:                                                                 ║
║   1º RD + x264 + AAC  →  perfecto para Chrome          ✅✅                  ║
║   2º RD + x264        →  video ok, audio puede fallar  ✅⚠️                 ║
║   3º RD + AAC         →  audio ok, video necesita RD   ⚠️✅                 ║
║   4º Cualquier RD     →  último recurso con RD         ⚠️⚠️                 ║
║                                                                              ║
║   → Elige: Alien.1979.1080p.BluRay.x264.AAC.mp4  ✅✅                        ║
╚══════════════════════════════════════════════════════════════════════════════╝
                    ↓
          ┌─────────────────────────────────────┐
          │ ¿Qué codec tiene el archivo?        │
          └─────────────────────────────────────┘
                ↙ x264                  ↘ x265
╔═══════════════════════════╗  ╔══════════════════════════════════════╗
║  REAL-DEBRID              ║  ║  REAL-DEBRID — TRANSCODE             ║
║  Sirve directo            ║  ║                                      ║
║                           ║  ║  RD convierte x265 → H.264           ║
║  URL directa CDN          ║  ║  en sus servidores                   ║
║  .mp4 / .mkv              ║  ║  devuelve URL HLS (.m3u8)            ║
║                           ║  ║                                      ║
║  VENTAJAS:                ║  ║  VENTAJAS:                           ║
║  ✅ Instantáneo           ║  ║  ✅ Mejor imagen original (x265)     ║
║  ✅ Sin procesamiento      ║  ║  ✅ Archivos 4K disponibles          ║
║  ✅ Audio original        ║  ║  ✅ Chrome puede reproducirlo        ║
║                           ║  ║                                      ║
║  DESVENTAJAS:             ║  ║  DESVENTAJAS:                        ║
║  ❌ Menos calidad que     ║  ║  ❌ Tarda más en iniciar             ║
║     x265 al mismo tamaño  ║  ║  ❌ RD pierde algo de calidad       ║
║  ❌ Archivos más grandes  ║  ║     al convertir                    ║
║                           ║  ║  ❌ Depende del API de transcode RD  ║
║  CUÁNDO ES MEJOR:         ║  ║                                      ║
║  → Películas 1080p        ║  ║  CUÁNDO ES MEJOR:                   ║
║  → Reproducción           ║  ║  → Películas 4K / 2160p             ║
║    instantánea            ║  ║  → Calidad es prioridad             ║
║  → Conexión estable       ║  ║  → No importa esperar unos segundos ║
╚═══════════════════════════╝  ╚══════════════════════════════════════╝
         ↘                              ↙
╔══════════════════════════════════════════════════════════════════════════════╗
║              EN LA PRÁCTICA para MaldoxFilm                                  ║
║                                                                              ║
║  1080p x264 directo   =  calidad muy buena, arranca rápido                   ║
║  2160p x265 transcode =  calidad superior PERO:                              ║
║    · RD debe convertirlo (unos segundos extra)                               ║
║    · la conversión puede bajar un poco la calidad 4K                         ║
║    · si el transcode falla → cae a SV                                        ║
║                                                                              ║
║  → MaldoxFilm prefiere x264 directo sobre x265 transcodeado                 ║
║    salvo que solo haya 4K disponible                                         ║
╚══════════════════════════════════════════════════════════════════════════════╝
                    ↓
╔══════════════════════════════════════════════════════════════════════════════╗
║                    REPRODUCTOR — MaldoxFilm                                  ║
║                                                                              ║
║   x264 directo  →  <video src="url.mp4">        reproduce nativo Chrome     ║
║   x265 HLS      →  HLS.js + <video src=".m3u8"> reproduce con librería      ║
║                                                                              ║
║                        🎬 Alien (1979) reproduciéndose ✅                    ║
╚══════════════════════════════════════════════════════════════════════════════╝
                    ↓ si algo falla en cualquier paso
╔══════════════════════════════════════════════════════════════════════════════╗
║                         SISTEMA DE FALLBACK                                  ║
║                                                                              ║
║   ⚡ RD (mejor calidad)                                                      ║
║      ↓ falla                                                                 ║
║   🌐 Stream-Vault (11 proveedores alternativos)                              ║
║      ↓ falla                                                                 ║
║   ▶  UnlimPlay                                                               ║
║      ↓ falla                                                                 ║
║   ▶  VidSrc                                                                  ║
║                                                                              ║
║   → MaldoxFilm cambia de fuente automáticamente, sin que el usuario         ║
║     tenga que hacer nada                                                     ║
╚══════════════════════════════════════════════════════════════════════════════╝
```
