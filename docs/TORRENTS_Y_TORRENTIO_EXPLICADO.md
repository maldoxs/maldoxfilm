# Torrents, Torrentio y Real-Debrid — Explicación Simple

> Qué es un torrent, cómo funciona Torrentio y cómo RD tiene los archivos listos al instante.

---

## 1. ¿Qué es un Torrent?

Imagina que quieres compartir un archivo enorme con mil personas.

**La forma normal (sin torrent):**
```
Tú tienes el archivo
        ↓
Las 1000 personas te lo piden a TI
        ↓
Tu internet colapsa ❌
```

**Con torrent:**
```
Tú tienes el archivo completo
        ↓
Persona 1 te descarga un pedacito
        ↓
Ahora Persona 1 también comparte ese pedacito
        ↓
Persona 2 descarga pedacitos de ti Y de Persona 1
        ↓
Mientras más gente descarga → más rápido va para todos ✅
```

> Un torrent no es el archivo — es un **mapa** que dice dónde están
> los pedacitos y quién los tiene. Cada torrent tiene un **hash único**
> que lo identifica (como una huella digital).

---

## 2. El problema de los torrents normales

Descargar un torrent puede tardar horas o días:

```
Tú quieres ver Alien ahora
        ↓
Inicias el torrent
        ↓
Esperas 3 horas descargando ❌
        ↓
No puedes hacer streaming mientras descarga
```

---

## 3. ¿Qué es Real-Debrid?

RD tiene servidores potentísimos que descargan torrents a máxima velocidad
y los guardan para servirlos al instante:

```
RD descarga Alien en 2 minutos (velocidad de servidor)
        ↓
Guarda el archivo en sus servidores
        ↓
Cuando TÚ lo pides → te lo sirve al instante ✅
```

### ¿Cómo se llena la BD de RD?

RD no tiene un catálogo propio — guarda todo lo que usuarios premium pidieron alguna vez:

```
Usuario A pidió Alien 4K    → RD lo descargó → lo guarda
Usuario B pidió Alien 1080p → RD lo descargó → lo guarda
Usuario C pidió El Padrino  → RD lo descargó → lo guarda
```

Cuando TÚ llegas, RD ya los tiene listos para cualquier usuario premium.

### ¿RD organiza por IMDB ID?

**No.** RD no sabe que "Alien.1979.1080p.mkv" = tt0078748.
Para RD es solo un archivo con un nombre y un hash de torrent.

```
RD tiene:  Alien.Directors.Cut.1979.1080p.BRrip.x264.mkv
           ← no sabe que es tt0078748, solo sabe el hash del torrent
```

---

## 4. ¿Qué es Torrentio?

Torrentio es un **buscador de torrents especializado en películas y series**
que además sabe cuáles están ya descargados en RD:

```
Torrentio sabe:
"La película tt0078748 (Alien) existe en estos torrents:
  ├── torrent A  →  2160p x265  →  hash: abc123
  ├── torrent B  →  1080p x264  →  hash: def456
  └── torrent C  →  720p        →  hash: ghi789"
```

### ¿Cómo sabe Torrentio qué tiene RD?

```
Torrentio toma el hash del torrent de Alien
        ↓
Le pregunta a RD: "¿Tienes este hash descargado?"
        ↓
RD responde: "Sí — aquí está el link directo"
        ↓
Torrentio devuelve ese link a MaldoxFilm ✅
```

La pregunta usa el **hash del torrent**, no el IMDB ID.
El IMDB ID solo lo usa Torrentio para saber qué torrents buscar.

---

## 5. El flujo completo junto

```
MaldoxFilm tiene IMDB ID: tt0078748
        ↓
Le pasa el ID a Torrentio
        ↓
Torrentio busca: "¿qué torrents existen de tt0078748?"
  ├── Encuentra torrent A (4K x265)  hash: abc123
  ├── Encuentra torrent B (1080p)    hash: def456
  └── Encuentra torrent C (720p)     hash: ghi789
        ↓
Torrentio pregunta a RD por cada hash:
  ├── abc123 → ✅ RD lo tiene → devuelve URL directa
  ├── def456 → ✅ RD lo tiene → devuelve URL directa
  └── ghi789 → ❌ RD no lo tiene → se descarta
        ↓
Torrentio devuelve lista de los que SÍ están en RD
        ↓
MaldoxFilm elige la mejor calidad disponible
        ↓
RD sirve el archivo al instante ✅
```

---

## 6. El mapa mental

```
TORRENT    =  mapa de un archivo repartido entre miles de PCs
              cada torrent tiene un hash único (su huella digital)

TORRENTIO  =  buscador de torrents por IMDB ID
              sabe cuáles de esos torrents ya tiene RD descargados

RD         =  servidor que descargó esos torrents y los sirve al instante
              no tiene catálogo por IMDB ID — organiza por hash de torrent

MALDOXFILM =  recibe la lista de Torrentio y elige la mejor calidad
```

---

## 7. Analogía completa

| Pieza | Analogía |
|---|---|
| **Torrent** | Mapa de dónde están los pedazos del archivo |
| **Personas compartiendo** | Los "seeders" — dan velocidad de descarga |
| **Real-Debrid** | Almacén gigante que ya descargó los torrents más populares |
| **Torrentio** | Catálogo del almacén — sabe qué hay y dónde |
| **Hash del torrent** | La huella digital que identifica cada archivo |
| **IMDB ID** | El nombre universal de la película (para que Torrentio sepa qué buscar) |
