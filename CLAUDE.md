# MaldoxFilm — Contexto del proyecto

## ¿Qué es?
Aplicación web tipo streaming (SPA de un solo archivo HTML) desplegada en Netlify.
Replica la experiencia de Netflix/Disney+ con catálogo de TMDB y canales IPTV.

**URL producción:** https://maldoxfilm.netlify.app  
**Repo GitHub:** https://github.com/maldoxs/maldoxfilm (rama `main`)  
**Archivo principal:** `/Users/angelomaldonado/Desktop/Streamix-local/assets/index.html` (~370KB, todo en un solo archivo: CSS + HTML + JS)

---

## Stack técnico
- HTML/CSS/JS vanilla — sin frameworks
- TMDB API (key: `d95e6a7e2ead40e949fcdb81f1f26f0b`) para catálogo de películas y series
- `hls.js` para reproducción de canales IPTV en formato M3U8
- Netlify para hosting estático
- Reproductores embebidos: UnlimPlay, VidSrc (iframes)

---

## Detección de dispositivo (en `<head>`, antes del render)
El script del `<head>` agrega una clase a `<html>` según el dispositivo:
- `html.tv-mode` → Smart TV (WebOS, Tizen, detectado por user-agent o resolución)
- `html.desktop-mode` → PC/laptop con mouse
- `html.mobile-mode` → móvil/tablet touch

Esto permite CSS instantáneo sin esperar JS.

---

## Navegación por modo

### Desktop (`html.desktop-mode`)
- Nav superior fijo (`nav#mainNav .desktop-nav`)
- Items: Inicio, Películas, Series, Anime, Mi Lista, Buscar
- **Canales TV oculto** (`#nlCanales { display:none }`)

### Mobile (`html.mobile-mode`)
- Bottom nav (`nav.bottom-nav`) con: Inicio, Buscar, Series, Películas, Más
- Panel de búsqueda móvil (`#mobileSearchPanel`) — overlay que se abre/cierra
- **Canales TV oculto** del menú "Más" (`#bmCanales { display:none }`)

### TV (`html.tv-mode`)
- Topnav horizontal fijo (`nav.tv-topnav`) — NO usar sidebar (tiene bugs históricos)
- Items: Inicio, Películas, Series, Anime, Mi Lista + botón pantalla completa + botón buscar
- **Canales TV oculto** (`#tnCanales { display:none }`)
- Botón pantalla completa (`#tnFullscreen`) en topnav — persiste aunque se entre al reproductor

---

## Reproductor de video
- Elemento: `div#playerPage .player-page`
- Topbar (`div.player-topbar`): título, selector de fuente, fullscreen — se auto-oculta con `controls-hidden`
- Flecha ← volver: `button.player-back` — **fuera del topbar**, posición absoluta `top:72px; left:16px`
  - Siempre visible (no se oculta con los controles)
  - Hover: se ilumina (fondo blanco semitransparente)
  - TV: más grande (`52x52px`), se pone azul acento al hacer focus
- Selector de fuentes (`div.source-selector`): dropdown con UnlimPlay / VidSrc
- **Icono de casa eliminado** via CSS: `.player-home-btn { display:none !important }`
- **"Enviar a TV" eliminado** del reproductor

---

## Pantalla completa (TV)
```javascript
var _appFSActive = false;
// toggleAppFullscreen() alterna fullscreen
// document.addEventListener('fullscreenchange') → si se pierde el fullscreen y _appFSActive=true,
// lo vuelve a activar automáticamente (ej: al entrar/salir del reproductor)
```
El flag `_appFSActive` persiste toda la sesión — si el usuario activó fullscreen, el app lo mantiene aunque navegue entre páginas.

---

## Colores actuales
```css
--accent:      #3d5afe;   /* Índigo/azul principal */
--accent2:     #2a46e0;   /* Azul más oscuro (hover) */
--accent-glow: rgba(61,90,254,0.25);
--bg:          #0e0e0e;
--text:        #f0f0f0;
```
**Importante:** en WebOS antiguo (LG 50") las CSS variables no funcionan. Se aplican fallbacks hardcodeados en body, topnav y páginas:
```css
body { background: #0e0e0e; background: var(--bg); }
```

---

## Búsqueda
- Desktop/mobile: `#searchInput` en `#mobileSearchPanel`
- TV: `#tvSearchBarInput` en `#tvSearchBar`
- Búsqueda en tiempo real via `oninput → handleSearch()`
- Resultados: solo películas y series TMDB (canales TV excluidos del buscador)
- El panel NO se cierra automáticamente — solo con Escape o botón ✕
- En TV: Enter cierra la barra de búsqueda (control remoto)

---

## Filtros por género
- Cada sección (Inicio, Películas, Series, Anime) tiene su propio `genre-pill bar`
- Sin íconos/emojis en los pills — solo texto
- **Bug corregido:** al cambiar filtro, se cancela cualquier lazy loader pendiente antes de cargar nuevos datos:
  ```javascript
  if(el._lazyLoader){ el._lazyLoader = null; _lazyObserver.unobserve(el); }
  ```
  Esto evitaba que el primer click mostrara datos de la sección anterior.

---

## Ver Todo (all-results overlay)
- `div#allResultsPage` — `position:fixed; z-index:610`
- Al hacer click en un resultado → llama `closeAllResults()` antes de `showDetailPage()` para evitar que el overlay tape la página de detalle
- Colores: botón Volver usa acento índigo (no rojo)

---

## Mi Lista
- Almacenamiento: `localStorage` via `safeStorage`, clave `sx_mylist`
- Formato: array de objetos `{id, type, title, poster}` (compatible con formato antiguo de strings)
- **Página propia** (`div#miListaPage`, clase `.mi-lista-page`) — igual que Películas/Series
- Aparece en el menú principal de todos los modos (desktop nav, TV topnav)
- Cada card tiene un botón ✕ para eliminar:
  - Desktop/mobile: aparece al hover sobre la card (opacity 0 → 1)
  - TV: siempre levemente visible, rojo al hacer focus
  - Llama `mlRemoveItem(id)` → elimina, guarda, re-renderiza, muestra toast

### Funciones clave de Mi Lista
```javascript
function _mlId(item)       // extrae el id string del item (objeto o string)
function _mlInList(id)     // boolean — está en la lista?
function _mlIdx(id)        // índice en dpMyList
function dpToggleList(id, title, type, poster)  // agrega/quita de la lista
function showMiListaPage() // muestra la página, cierra TODAS las demás (incluyendo detailPage)
function closeMiLista()    // oculta la página
function mlRemoveItem(id)  // elimina item, re-renderiza
```

### Bug corregido — Mi Lista aparecía en otras secciones
`showMoviesPage`, `showSeriesPage`, `showAnimePage`, `showChannelsPage` no llamaban `miListaPage.classList.remove('active')`. Corregido: ahora todas las funciones de navegación ocultan `miListaPage`.

### Bug corregido — Al ir a Mi Lista desde detalle, se quedaba pegado
`showMiListaPage` no cerraba `detailPage`. Corregido: ahora incluye `detailPage.classList.remove('active')` y `body.style.overflow=''`.

---

## Páginas y su patrón show/hide
```
homePage      → classList.add('hidden') para ocultar
moviesPage    → classList.add/remove('active') — default display:none
seriesPage    → classList.add/remove('active')
animePage     → classList.add/remove('active')
channelsPage  → classList.add/remove('active')
miListaPage   → classList.add/remove('active')
detailPage    → classList.add/remove('active')
allResultsPage → classList.add/remove('open') — position:fixed overlay
playerPage    → classList.add/remove('active')
```
**Regla:** cualquier función que muestre una página debe ocultar TODAS las demás explícitamente.

---

## Lazy loading (carruseles infinitos)
- `_lazyObserver` — IntersectionObserver global
- Cada carousel `div` tiene `el._lazyLoader` para cargar más al hacer scroll
- Al llamar `loadCarouselInfinite(id, endpoint, type)` siempre se cancela el loader anterior:
  ```javascript
  if(el._lazyLoader){ el._lazyLoader = null; _lazyObserver.unobserve(el); }
  ```

---

## Reglas críticas al editar
1. **NUNCA tocar** las reglas CSS de `html.tv-mode` que controlan `display` del topnav/sidebar
2. **NUNCA cambiar** `padding` del `.player-topbar` — afecta el área de click y rompe los controles
3. **NUNCA eliminar** elementos del HTML si hay JS que los referencia — usar `display:none` en CSS
4. Cualquier función de navegación que muestre una página **debe ocultar todas las demás** (incluyendo `miListaPage` y `detailPage`)
5. Hacer **1 cambio a la vez**, deploy, esperar confirmación del usuario antes de continuar

---

## Deploy
```bash
cd /Users/angelomaldonado/Desktop/Streamix-local
netlify deploy --prod --dir assets
```

## Git (solo cuando el usuario confirma que todo funciona)
```bash
git add assets/index.html
git commit -m "descripción clara de los cambios"
git push origin main
```

---

## Cambios completados (historial)
- [x] Hero dots ocultos
- [x] Buscador: panel no se cierra solo, Enter funciona en TV
- [x] Título MALDOXFILM en todos los logos
- [x] Eliminar "Enviar a TV" del reproductor
- [x] Botón pantalla completa en TV topnav (con flag `_appFSActive` persistente)
- [x] Flecha ← del reproductor: siempre visible, fuera del topbar, efecto hover
- [x] Filtros sin emojis/íconos en los pills
- [x] Bug lazy loader: filtros cargaban datos incorrectos en el primer click
- [x] Ver Todo: colores índigo, click directo al detalle sin quedar tapado
- [x] Botón "Volver" junto a "Mi Lista" en el detalle
- [x] Mi Lista como página propia (no overlay flotante)
- [x] Mi Lista en menú principal (desktop, TV, mobile)
- [x] Orden home: filtros de género ANTES de "Continuar Viendo"
- [x] Fallbacks CSS para WebOS antiguo (LG 50" blanco y negro)
- [x] Botón ✕ para eliminar items de Mi Lista
- [x] Bug: Mi Lista aparecía al fondo de otras secciones
- [x] Bug: navegar a Mi Lista desde detalle se quedaba pegado

## Pendiente
- [ ] Git commit y push con todos los cambios acumulados
