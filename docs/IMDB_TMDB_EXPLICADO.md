# IMDB y TMDB — Explicación Simple

> Qué son, cómo funcionan sus IDs y por qué MaldoxFilm los necesita a ambos.

---

## ¿Qué significan las siglas?

| Sigla | Nombre completo | En español |
|---|---|---|
| **IMDB** | Internet Movie Database | Base de Datos de Películas en Internet |
| **TMDB** | The Movie Database | La Base de Datos de Películas |

---

## IMDB — El estándar mundial

### ¿Quién lo creó?
IMDB nació en **1990** — creado por fans de cine en un foro llamado Usenet. Empezaron a listar películas manualmente y les asignaron IDs con formato `tt` + números.

**Amazon lo compró en 1998** y desde entonces es la base de datos de entretenimiento más grande del mundo.

### ¿Cómo funciona el ID?
Los IDs se asignan en orden cronológico de registro:

```
tt0000001  →  Carmencita (1894) — la primera registrada
tt0078748  →  Alien (1979)
tt0068646  →  El Padrino (1972)
tt9999999  →  películas muy recientes
```

El prefijo `tt` significa simplemente **"title"** (título). Es solo una convención que IMDB adoptó desde el inicio.

### ¿Es una regla oficial?
**No por ley — pero sí por adopción masiva.**

Nadie obligó a nadie a usarlo. Pero como IMDB era la base más grande y confiable, todos empezaron a referenciarlo:

```
Netflix    →  usa IMDB ID internamente
TMDB       →  guarda el IMDB ID de cada título
Torrentio  →  busca streams por IMDB ID
Kodi       →  usa IMDB ID
Plex       →  usa IMDB ID
MaldoxFilm →  usa IMDB ID para consultar Torrentio
```

Es como el inglés — nadie lo declaró idioma oficial del mundo, pero todos lo usan para comunicarse.

### ¿Puedo cambiar o inventar un ID?
**No.** IMDB es una base de datos **centralizada y controlada por Amazon.**

```
IMDB = una sola base de datos central
         ↓
Amazon controla quién escribe en ella
         ↓
Los IDs son inmutables — nadie los puede cambiar
```

Puedes proponer agregar una película nueva o corregir información, pero sujeto a revisión del equipo de IMDB. El ID lo asigna IMDB, no tú.

---

## TMDB — El alternativo abierto

### ¿Quién lo creó?
TMDB fue creado en **2008** como alternativa **open source y gratuita** a IMDB. Cualquier persona puede contribuir y corregir información. Tiene su **propia API gratuita** — por eso la usan los desarrolladores.

### Sus IDs son distintos
TMDB tiene sus propios IDs numéricos, independientes de IMDB:

```
Alien tiene DOS IDs distintos:
  IMDB ID  →  tt0078748   (Amazon, cerrado)
  TMDB ID  →  27205       (comunidad, abierto con API gratis)
```

### ¿Por qué MaldoxFilm usa TMDB y no IMDB directamente?

```
IMDB  →  no tiene API gratuita para desarrolladores ❌
TMDB  →  API gratuita, rápida, con portadas e info completa ✅
```

Entonces MaldoxFilm usa TMDB para el catálogo visual (portadas, descripciones, búsqueda), pero necesita el IMDB ID para hablar con Torrentio.

---

## El puente entre los dos

Por eso existe este paso en MaldoxFilm:

```
Usuario hace clic en "Alien"
        ↓
MaldoxFilm tiene el TMDB ID: 27205
        ↓
Pregunta a TMDB: "¿Cuál es el IMDB ID de 27205?"
        ↓
TMDB responde: "tt0078748"
        ↓
MaldoxFilm le pregunta a Torrentio: "¿Tienes tt0078748 en RD?"
        ↓
Torrentio devuelve la URL del stream ✅
```

Sin ese puente TMDB→IMDB, Torrentio no sabría de qué película estás hablando.

---

## Comparativa final

| | IMDB | TMDB |
|---|---|---|
| **Creado por** | Fans, comprado por Amazon | Comunidad open source |
| **Año** | 1990 | 2008 |
| **Control** | Amazon (cerrado) | Comunidad (abierto) |
| **Formato ID** | `tt` + números (`tt0078748`) | Solo números (`27205`) |
| **API gratuita** | ❌ No | ✅ Sí |
| **¿Quién lo usa?** | Toda la industria | Desarrolladores y apps |
| **¿IDs modificables?** | No | No |
| **En MaldoxFilm** | Para buscar en Torrentio | Para catálogo y portadas |

---

## Resumen en una línea

> **IMDB** es el DNI universal de cada película — lo asigna Amazon y nadie lo cambia.
> **TMDB** es la versión abierta para desarrolladores — misma info, API gratis.
> MaldoxFilm usa los dos: TMDB para mostrar y IMDB para buscar el stream.
