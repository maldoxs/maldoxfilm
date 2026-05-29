# x265 / H.265 — Explicación Simple

> Por qué Chrome no reproduce 4K y cómo Real-Debrid lo soluciona.

---

## El problema básico: idiomas de video

Imagina que los videos hablan idiomas distintos. Chrome solo entiende algunos:

```
H.264 (x264)  →  Chrome lo entiende ✅  (el idioma "común")
H.265 (x265)  →  Chrome NO lo entiende ❌  (idioma "moderno")
AV1           →  Chrome lo entiende ✅  (muy nuevo)
```

---

## ¿Por qué existe x265 si Chrome no lo entiende?

Porque x265 es **más eficiente**. Misma calidad, la mitad del tamaño:

```
Película 4K en x264  →  80 GB
Película 4K en x265  →  35 GB   ← misma calidad, menos espacio
```

Por eso los torrents de 4K casi todos son x265 — ocupan menos y se comparten más rápido.

---

## ¿Por qué Chrome no lo soporta?

Por dinero. Decodificar x265 requiere pagar una **licencia a una empresa**. Los fabricantes de celulares y TVs la pagan (por eso en un FireTV sí funciona). Google decidió no pagarla en Chrome y desarrolló su propio formato (AV1) que es gratis.

```
FireTV / Android TV  → pagó licencia → reproduce x265 ✅
Chrome               → no pagó       → no reproduce x265 ❌
Safari (Mac/iPhone)  → sí pagó       → reproduce x265 ✅
```

---

## ¿Qué hace Real-Debrid para solucionarlo?

RD tiene servidores potentes que **traducen** el video antes de enviártelo:

```
Archivo x265 en RD
       ↓
RD lo convierte a H.264 en su servidor  (transcode)
       ↓
Te manda el stream ya convertido (HLS .m3u8)
       ↓
Chrome lo recibe en H.264 → lo entiende ✅
```

Eso es el **transcode** — RD hace la traducción por ti en sus servidores.

---

## Cómo lo resuelve MaldoxFilm

```
Torrentio devuelve stream x265 4K
       ↓
rdGetStream busca el ID del archivo en /downloads de RD
       ↓
Llama a /streaming/transcode/{id}
       ↓
RD responde con URL HLS (H.264 compatible con Chrome)
       ↓
HLS.js reproduce el stream ✅
```

Si el archivo no está en downloads o el transcode falla:
```
Intenta reproducción directa (funciona si es H.264)
       ↓ falla
Cambia a 🌐 Stream-Vault automáticamente
```

---

## Resumen comparativo de reproductores

| Reproductor | x265 4K | ¿Por qué? |
|---|---|---|
| **Chrome / Edge** | ❌ | No pagó licencia HEVC |
| **Safari (Mac/iPhone)** | ✅ | Apple pagó licencia |
| **FireTV / Android TV** | ✅ | Hardware con licencia |
| **VLC (escritorio)** | ✅ | Decodifica por software |
| **ExoPlayer (Android)** | ✅ | Usa hardware del dispositivo |
| **MaldoxFilm en Chrome** | ✅* | *Via transcode de RD |

---

## Glosario rápido

| Término | Qué significa |
|---|---|
| **x264 / H.264** | Formato de video estándar, compatible con todo |
| **x265 / H.265 / HEVC** | Formato moderno, mitad de tamaño, Chrome no lo lee |
| **AV1** | Formato nuevo de Google, libre de licencias, Chrome sí lo lee |
| **Transcode** | Convertir un video de un formato a otro (RD lo hace en sus servidores) |
| **HLS** | HTTP Live Streaming — formato de stream que usa Apple y RD para el transcode |
| **HLS.js** | Librería JavaScript que permite reproducir HLS en Chrome |
