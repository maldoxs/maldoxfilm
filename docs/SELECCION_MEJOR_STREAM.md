# Cómo elige MaldoxFilm la mejor versión — Explicación Completa

> Torrentio devuelve una lista de versiones disponibles.
> MaldoxFilm tiene que leer esa lista y elegir la mejor para Chrome.
> Aquí se explica toda la lógica de esa decisión.

---

## El problema de partida

Cuando buscas "Alien (1979)" en MaldoxFilm, Torrentio puede devolver
20, 50 o hasta 100 versiones distintas del mismo film:

```
Alien.1979.2160p.BluRay.x265.DTS.mkv
Alien.1979.2160p.BluRay.x265.TrueHD.mkv
Alien.1979.1080p.BluRay.x264.AAC.mp4
Alien.1979.1080p.WEB.x265.AC3.mkv
Alien.Directors.Cut.1979.1080p.BRrip.x264.AAC.mp4
Alien.1979.720p.x264.MP3.mkv
Alien.1979.480p.DVDRip.XviD.mp3.avi
...
```

MaldoxFilm tiene que leer esos nombres y entender cuál es mejor
para reproducir en Chrome. Hay dos dimensiones que importan:

---

## Dimensión 1 — Calidad de IMAGEN

### Resolución (de mejor a peor)

```
2160p  =  4K Ultra HD      ← la mejor imagen, archivos de 40-80 GB
1080p  =  Full HD          ← muy buena imagen, archivos de 8-15 GB
720p   =  HD               ← buena imagen, archivos de 3-6 GB
480p   =  DVD              ← calidad media, archivos de 1-2 GB
```

### Codec de video (cómo está comprimida la imagen)

El codec define si Chrome puede reproducirlo directamente o no:

| Codec | También llamado | Chrome | ¿Necesita transcode? |
|---|---|---|---|
| **x264** | H.264, AVC | ✅ Sí | No |
| **x265** | H.265, HEVC | ❌ No | Sí — RD lo convierte |
| **AV1** | AV1 | ✅ Sí | No |
| **XviD / DivX** | MPEG-4 | ⚠️ A veces | Depende |

> x265 produce mejor imagen que x264 al mismo tamaño,
> pero Chrome no puede decodificarlo. Por eso necesita
> que RD lo transcodifique antes de enviarlo.

---

## Dimensión 2 — Calidad de AUDIO

### Formatos de audio (de mejor a peor)

```
DTS-HD / TrueHD  →  audio Blu-ray lossless, sin pérdida    ← el mejor
DTS              →  audio cine profesional, multicanal      ← excelente
AC3 / Dolby DD   →  Dolby Digital 5.1 canales              ← muy bueno
AAC              →  audio moderno, estéreo eficiente        ← bueno
MP3              →  audio básico, estéreo                   ← aceptable
```

### ¿Qué entiende Chrome?

| Formato audio | Chrome | Problema |
|---|---|---|
| **AAC** | ✅ Sí | Ninguno |
| **MP3** | ✅ Sí | Ninguno |
| **AC3 / Dolby** | ⚠️ Parcial | Depende del archivo |
| **DTS** | ❌ No | Chrome no lo decodifica |
| **TrueHD** | ❌ No | Chrome no lo decodifica |

---

## El doble problema de los archivos 4K

Los torrents 4K casi siempre combinan los formatos más problemáticos:

```
Alien.1979.2160p.BluRay.x265.DTS.mkv
  ├── Video: x265  →  Chrome ❌ (necesita transcode)
  └── Audio: DTS   →  Chrome ❌ (no lo decodifica)
  → Doble problema: hay que transcodificar todo ❌❌
```

Mientras que un 1080p típico:
```
Alien.1979.1080p.BluRay.x264.AAC.mp4
  ├── Video: x264  →  Chrome ✅
  └── Audio: AAC   →  Chrome ✅
  → Reproduce directo sin tocar nada ✅✅
```

---

## La lógica de selección de MaldoxFilm

MaldoxFilm lee el nombre del archivo y puntúa cada versión:

### Prioridad 1 — RD + x264 + AAC
```
Compatible 100% con Chrome
Sin transcode necesario
Audio perfecto para navegador
→ IDEAL ✅✅
```

### Prioridad 2 — RD + x264 (cualquier audio)
```
Video compatible directo
Audio puede tener problema (DTS/TrueHD)
Pero al menos el video funciona
→ BUENO ✅⚠️
```

### Prioridad 3 — RD + AAC (cualquier video)
```
Audio compatible
Video puede ser x265 → necesita transcode de RD
→ ACEPTABLE ⚠️✅
```

### Prioridad 4 — Cualquier stream con URL de RD
```
Lo que haya disponible en RD
Puede necesitar transcode de video y/o audio
→ ÚLTIMO RECURSO RD ⚠️⚠️
```

### Prioridad 5 — Sin RD
```
Torrent normal, sin servidor premium
Descarga lenta, puede fallar
→ EVITAR si hay alternativa ❌
```

---

## Ejemplo real — Lista de Alien en Torrentio

```
┌─────────────────────────────────────────────┬────────┬───────┬──────────┐
│ Archivo                                     │ Video  │ Audio │ Para Chrome│
├─────────────────────────────────────────────┼────────┼───────┼──────────┤
│ Alien.1979.2160p.BluRay.x265.DTS.mkv        │ x265 ❌│ DTS ❌│ ❌❌ malo  │
│ Alien.1979.2160p.BluRay.x265.TrueHD.mkv     │ x265 ❌│ HD ❌ │ ❌❌ malo  │
│ Alien.1979.1080p.BluRay.x264.AAC.mp4        │ x264 ✅│ AAC ✅│ ✅✅ IDEAL │  ← MaldoxFilm elige este
│ Alien.1979.1080p.WEB.x265.AC3.mkv           │ x265 ❌│ AC3 ⚠️│ ❌⚠️ medio│
│ Alien.Directors.Cut.1979.1080p.x264.AAC.mp4 │ x264 ✅│ AAC ✅│ ✅✅ bueno │
│ Alien.1979.720p.x264.MP3.mkv                │ x264 ✅│ MP3 ✅│ ✅✅ menor │
└─────────────────────────────────────────────┴────────┴───────┴──────────┘
```

**MaldoxFilm elige el 1080p x264 AAC** — no es el de mayor resolución
(hay uno 4K) pero es el más compatible y de mejor calidad real en Chrome.

> El 4K x265 se ve mejor en papel, pero si Chrome no puede
> reproducirlo sin transcode, no sirve de nada.

---

## Cuándo SÍ se usa el transcode de RD

Si solo hay versiones x265 disponibles, MaldoxFilm activa el transcode:

```
Solo hay x265 disponible
        ↓
MaldoxFilm busca el ID del archivo en /downloads de RD
        ↓
Llama a /streaming/transcode/{id}
        ↓
RD convierte x265 → H.264 en sus servidores
        ↓
Devuelve URL HLS (.m3u8) compatible con Chrome
        ↓
HLS.js reproduce el stream ✅
```

---

## Resumen en una tabla

| Situación | MaldoxFilm hace | Resultado |
|---|---|---|
| Hay x264 + AAC en RD | Reproduce directo | ✅ Instantáneo |
| Hay x264 sin AAC en RD | Reproduce directo | ✅ Puede haber problema de audio |
| Solo hay x265 en RD | Pide transcode a RD | ✅ Funciona pero tarda un poco más |
| RD no tiene nada | Pasa a Stream-Vault | ⚠️ Fuente alternativa |
| Stream-Vault falla | Pasa a UnlimPlay | ⚠️ Fuente alternativa |
| Todo falla | Pasa a VidSrc | ⚠️ Último recurso |

---

## Glosario rápido

| Término | Qué significa |
|---|---|
| **Resolución** | Tamaño de la imagen en píxeles (1080p, 4K, etc.) |
| **Codec de video** | Cómo está comprimido el video (x264, x265, AV1) |
| **Codec de audio** | Cómo está comprimido el audio (AAC, DTS, AC3) |
| **Transcode** | Convertir de un formato a otro (RD lo hace en sus servidores) |
| **Lossless** | Sin pérdida de calidad — el audio/video es idéntico al original |
| **HLS** | Formato de stream que usa RD para el transcode |
| **Bitrate** | Cantidad de datos por segundo — más alto = mejor calidad |
