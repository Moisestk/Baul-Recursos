@AGENTS.md

# BaulResources

## Visión del proyecto

BaulResources es una aplicación web para **scrapear diariamente** cuentas de creadores de contenido en X (Twitter) y **organizar los recursos** que publican (enlaces a páginas, texto/descripciones y URLs de imágenes públicas) en una base de datos propia, con el fin de reutilizarlos en futuros proyectos. Es un "cofre" personal de recursos indexados y buscables.

Flujo central: **scrapear → extraer (link, texto, imagen) → guardar en BD → organizar (tags/categorías) → buscar y filtrar → reutilizar**.

No es un clon de X ni una herramienta de publicación: es un curador/archivador de recursos de terceros para uso propio.

## Identidad de marca

Fuente completa: [`Informacion de diseño/Manual_Marca_BaulResources.pdf`](../Informacion%20de%20diseño/Manual_Marca_BaulResources.pdf) (ruta relativa al repo `baul-resources`, un nivel arriba). Logos disponibles en esa misma carpeta: `baul-logo.png`, `logo-baulpng.png`, `logo-baulpng-transparente.png`. **Antes de tocar el favicon o el logo en la app, copiarlos a `public/`** (aún no están ahí).

**Concepto:** homenaje sutil a la estética de "baúl/cofre" de Minecraft, reinterpretado con una identidad profesional, minimalista y de Dev UI (inspirada en VS Code / GitHub). Ludismo funcional sin sacrificar seriedad.

### Paleta de colores

| Nombre | Hex | Uso |
|---|---|---|
| Void Dark | `#12141c` | Fondo base (dark mode nativo) |
| Panel Surface | `#1b1f2e` | Superficies, cards, paneles |
| Chest Wood | `#b8860b` | Acento cálido primario (madera del cofre) |
| Amber Scrap | `#d97706` | Acento secundario / hover / estados activos |
| Dev Cyan | `#38bdf8` | Acento técnico (links, datos, consola) |

Bordes sutiles en cards: `1px solid #252b42`. Hover con destello ámbar o cian.

### Tipografía

| Rol | Familia | Uso |
|---|---|---|
| Títulos y branding | Inter / Plus Jakarta Sans (sans geométrica) | Navegación, títulos de sección, cards de recursos |
| Datos, código y logs | JetBrains Mono / Fira Code (monoespaciada) | Consola de scraping, URLs, selectores, JSON/metadatos técnicos |
| Retro / opcional | Press Start 2P | Solo easter eggs o insignias pequeñas (8–10pt), uso muy medido |

Se debe reemplazar la fuente Geist del scaffold por Inter/Plus Jakarta Sans + JetBrains Mono/Fira Code vía `next/font`.

### Guías de UI

- **Dark mode nativo** como modo por defecto (no solo `prefers-color-scheme`, sino la identidad visual real de la app).
- **Cards de recursos en Bento Grid**: estructura modular, bordes sutiles (`#252b42`), hover con destello ámbar/cian, cuadrícula tipo "inventario".
- **Consola de scraping**: fondo negro profundo, tipografía monoespaciada, texto verde terminal o cian, barras de progreso/estado en tiempo real durante el scrapeo.

## Stack técnico

- **Next.js** (App Router) — ya inicializado con `create-next-app`, sobre Next 16 / React 19.
- **Tailwind CSS v4** — ya instalado (`@tailwindcss/postcss`, `@tailwindcss/vite`), config vía `@theme` en `globals.css`. Los tokens de la paleta de marca deben definirse ahí como CSS vars (`--color-void-dark`, `--color-chest-wood`, etc.), no hardcodeados en componentes.
- **shadcn/ui** — aún no instalado; es la librería de componentes elegida para inputs, cards, dialogs, filtros, etc.
- **Base de datos** — aún no decidida/implementada. Necesaria para persistir recursos scrapeados de forma duradera (no solo en memoria). Al elegirla, priorizar algo con buen soporte de filtros/búsqueda de texto (Postgres es la opción natural dado el volumen de texto + tags).
- **Scraper** — aún no implementado. Debe correr con cadencia diaria (cron/scheduled job) contra una lista configurable de creadores/handles de X.

## Modelo de datos (conceptual)

- **Creator**: handle, nombre, avatar/foto de perfil, url de perfil, activo/inactivo para scrapeo.
- **Resource** (el recurso guardado por cada post relevante):
  - `source_post_url` (el tweet/post origen)
  - `link_url` (enlace externo encontrado en el post, si hay)
  - `image_url` (imagen pública del post, si hay)
  - `text` (texto/descripción del post)
  - `creator_id`
  - `tags` / `category` (organización manual o semi-automática)
  - `scraped_at`, `saved_at`
  - `status` (nuevo / revisado / usado)
  - `used_in` (proyectos donde se reutilizó, opcional)

Este modelo es punto de partida — ajustar cuando se defina el ORM/BD.

## Funcionalidades clave

1. **Scrapeo diario** de una lista de creadores en X, extrayendo posts nuevos desde el último scrapeo.
2. **Extracción de recursos**: link externo, texto/descripción, URL de imagen pública del post.
3. **Persistencia organizada** en base de datos (evitar duplicados por post ya scrapeado).
4. **Organización**: tags/categorías, favoritos, estado de uso.
5. **Filtros de búsqueda variados**: por creador, rango de fechas, tag/categoría, tipo de recurso (con imagen / con link / solo texto), texto libre.
6. **UI de cards** (bento grid) para visualizar cada recurso con su preview (imagen, texto truncado, creador, fecha, link).
7. **Reutilización futura**: marcar/filtrar recursos ya usados en otros proyectos.

## Notas sobre el scraping de X

X tiene medidas anti-scraping y términos de servicio restrictivos; este es un uso personal de bajo volumen (lectura de posts públicos de una lista propia de creadores, cadencia diaria, no republicación masiva ni redistribución). Al implementar el scraper: cuidar rate limiting, backoff ante bloqueos, y robustez ante cambios de estructura del sitio (los selectores se rompen seguido). Si en algún punto aparece la API oficial de X como alternativa viable, evaluarla frente al scraping directo.

## Estado actual

El proyecto está en scaffold inicial (`create-next-app` sin modificar: sin shadcn/ui, sin tokens de marca aplicados, sin modelo de datos, sin scraper). Todo lo descrito arriba es el objetivo a construir, no el estado actual del código.
