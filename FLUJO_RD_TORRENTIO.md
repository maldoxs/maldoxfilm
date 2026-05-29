# Flujo Real-Debrid + Torrentio — Explicación Simple

> Cómo funciona la reproducción con ⚡ RD en MaldoxFilm, paso a paso.

---

## El problema: quieres ver "Alien (1979)"

---

### PASO 1 — Tu app sabe el ID de TMDB

**TMDB** es como el "diccionario oficial" de películas.
Tu app guarda el número `27205` que significa "Alien (1979)" en ese diccionario.

Pero hay **otro diccionario más viejo** llamado **IMDB** (el de las estrellas ⭐), y Alien ahí es `tt0078748`.

---

### PASO 2 — Preguntarle a TMDB: "¿cuál es el ID de IMDB?"

```
Tu app → pregunta a TMDB:
"Oye, el 27205... ¿cómo se llama en IMDB?"

TMDB → responde:
"Ese es tt0078748"
```

---

### PASO 3 — Preguntarle a Torrentio

**¿Qué es Torrentio?**
Imagínate un bibliotecario que conoce **todos los torrents del mundo** y sabe cuáles están guardados en Real-Debrid listos para ver.

```
Tu app → pregunta a Torrentio:
"¿Tienes la película tt0078748 en Real-Debrid?"

Torrentio → responde con una lista:
- Alien.1979.2160p.x265.mkv  → https://cdn99.real-debrid.com/dl/ABC123/alien.mkv
- Alien.1979.1080p.x264.mkv  → https://cdn99.real-debrid.com/dl/XYZ789/alien.mkv
```

Torrentio ya usó **tu token de RD** para revisar cuáles están en el caché, listas para ver instantáneamente.

---

### PASO 4 — El problema: x265 no lo entiende Chrome

La primera URL apunta a un archivo `.mkv` con **x265** (formato ultra HD moderno).

Chrome dice: ❌ "No sé reproducir eso"

---

### PASO 5 — El transcoding: RD lo traduce

Le decimos a Real-Debrid:

```
"Oye, el archivo ABC123... ¿me lo puedes convertir a algo
que Chrome sí entienda?"

RD → responde:
"Claro, aquí tienes el mismo video pero en HLS (H.264):
https://cdn99.real-debrid.com/stream/ABC123/index.m3u8"
```

RD **convierte el video en sus servidores** y te da un link nuevo que sí funciona en el navegador.

---

### PASO 6 — Tu app reproduce ese link con HLS.js

```javascript
video.src = "https://cdn99.real-debrid.com/stream/ABC123/index.m3u8" // ✅
```

---

## Resumen visual

```
Tu app
  │
  ├─► TMDB: "27205 → ¿IMDB?"           →  tt0078748
  │
  ├─► Torrentio: "¿tt0078748 en RD?"   →  URL directa del archivo (.mkv)
  │
  ├─► RD API: "transcódeme ese archivo" →  URL HLS compatible con Chrome
  │
  └─► HLS.js reproduce el video ✅
```

---

## Por qué cada pieza es necesaria

| Pieza | Qué hace |
|---|---|
| **TMDB** | Diccionario de películas, da el ID de IMDB |
| **IMDB ID** | Identificador universal que Torrentio entiende |
| **Torrentio** | Bibliotecario: sabe qué torrents están cacheados en tu cuenta RD |
| **Real-Debrid** | Descargó el torrent previamente, te sirve el archivo directo |
| **Transcoding RD** | Convierte x265/HEVC → H.264 HLS para que Chrome lo pueda reproducir |
| **HLS.js** | Reproduce el stream HLS en el navegador |

---

## El token de RD

Tu token `RD_TOKEN` le prueba a Torrentio y a Real-Debrid que eres tú:
- Torrentio lo usa para saber qué archivos tienes cacheados en **tu cuenta**
- La app lo usa para llamar al API de transcoding de **tu cuenta**

Sin el token, Torrentio solo mostraría torrents que habría que descargar (lento). Con el token, sirve los archivos al instante desde el caché de RD.

---

## Flujo de fallback en la app

```
⚡ RD (transcoded HLS)
   ↓ si falla
⚡ RD (video directo — funciona para H.264)
   ↓ si falla
🌐 SV (Stream-Vault)
   ↓ si falla
▶ UnlimPlay
   ↓ si falla
▶ VidSrc
```

---

## Errores conocidos y resueltos

### ❌ `_safePlay is not defined` — Mayo 2026

**Síntoma:** El reproductor RD se quedaba colgado en pantalla negra. El video nunca arrancaba. En consola aparecía:
```
Uncaught ReferenceError: _safePlay is not defined
    at video.addEventListener.once (índice):6456
```

**Causa:** En el bloque de reproducción RD se usaba `_safePlay(video)` para iniciar el video, pero esa función **nunca existió** en el código. Se introdujo como nombre de función en una sesión de desarrollo sin llegar a definirla.

**Fix:** Reemplazar todas las llamadas `_safePlay(video)` por `video.play().catch(()=>{})` directamente.

```javascript
// ❌ Mal — función inexistente
_safePlay(video);

// ✅ Bien — nativo del browser
video.play().catch(()=>{});
```

**Afectaba a:** Reproducción directa H.264, HLS.js (manifest parsed), Apple HLS nativo, y Shaka Player (SV).

---

### ℹ️ Nota: URL proxy de Torrentio

Torrentio no siempre devuelve una URL CDN directa. A veces devuelve una URL proxy propia:
```
https://torrentio.strem.fun/resolve/realdebrid/TOKEN/HASH/filename.mp4
```
El navegador **sí puede seguir ese redirect** automáticamente al hacer `video.src = url`. No hace falta resolverla manualmente para reproducir H.264.

Para x265 (que necesita transcode), sí es necesario resolver la URL para obtener el ID real del download en `/rest/1.0/downloads`.
