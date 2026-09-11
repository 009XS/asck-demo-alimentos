# Auditoría — La Tradición (Panadería) — 2026-07-12

**Stack:** HTML5 + Tailwind CSS (CDN, `cdn.tailwindcss.com`) + Font Awesome 6.4.0 (local, `vendor/`) + Google Fonts (Great Vibes, Dancing Script, Inter) + Leaflet 1.9.4 (CSS local en `vendor/`, JS desde unpkg) + JS vanilla inline. Sin build step, sin dependencias npm.

**Categoría:** (a) Estático puro — sin persistencia local, el formulario solo arma un link `wa.me` (redirección, no guarda datos).

**Cómo se sirve:** archivos estáticos; verificado con `python -m http.server 4131 --directory .`

## Hallazgos

| Sev | Área | Hallazgo | Evidencia | Acción |
|-----|------|----------|-----------|--------|
| OK | Assets | Hero (`img` sección #inicio) usa hotlink externo a `images.unsplash.com` en vez de asset local | `src="https://images.unsplash.com/photo-1555507036-..."` | Riesgo: dependencia externa frágil. **Favicon resuelto 2026-07-24**: ahora es SVG local propio (`images/favicon.svg`, hogaza con paleta del sitio) — el anterior era hotlink Unsplash bloqueado por la CSP del VPS. **Hero resuelto 2026-09-11**: la imagen se descargo y ahora se sirve local como `images/hero-pan.webp` (1600 px, 187 KB, WebP). **El sitio ya no depende de ningun hotlink externo de imagenes.** |
| Baja | Consola | Advertencia `cdn.tailwindcss.com should not be used in production` | Consola del navegador (2 warnings) | Esperado en demo con CDN; no bloquea funcionalidad. Documentado, no requiere acción para esta fase. |
| OK | Privacidad | Los CTAs de WhatsApp apuntan al número de ASCK, no al del negocio | `wa.me/525525268475` | **Resuelto 2026-09-11, al hacer público el repositorio**: se retiraron del sitio el teléfono real, la dirección exacta y el Instagram de La Tradición, que es un **prospecto y no un cliente**, y sus datos no deben publicarse sin su acuerdo. La dirección quedó a nivel de zona, el mapa dejó de señalar un local concreto y el teléfono dice "por confirmar". Los datos reales siguen en el historial de git: ver "Pendientes". |
| Info | CDNs | Tailwind, Font Awesome, Google Fonts, Leaflet CSS/JS | Todas responden 200 (verificado con fetch); mapa Leaflet renderiza tiles de OpenStreetMap correctamente | Sin caídos. |
| OK | Imágenes locales | 7 imágenes en `images/` (menú, galería, destacado) | Todas 200 OK, `naturalWidth>0` para las 7 | Sin roturas. |
| OK | Meta | `<title>`, `<meta description>`, `<meta viewport>`, favicon — todos presentes | Líneas 5-10 del `<head>` | Completo. |
| OK | JS | Funciones inline (menú filtrable, formulario→WhatsApp, lightbox, mapa Leaflet, scroll fade-in) | Sin errores en consola al cargar/interactuar | Sin errores de sintaxis. |
| OK | Anclas | `#inicio #esencia #menu-interactivo #destacados #galeria #ubicacion #contacto-pedido` | Revisión manual de `id=` | Sin duplicados. |
| OK | Secretos | Ningún hardcodeo de keys/tokens | Revisión visual del archivo | N/A. |

## Verificación (servidor de prueba, puerto 4131)

- Consola: 0 errores, solo advisory de Tailwind CDN (no es error).
- Network: 0 requests fallidas. Todas las imágenes del sitio son locales desde 2026-09-11; las únicas peticiones de imagen externas que quedan son los tiles de OpenStreetMap, propios del mapa interactivo.
- `naturalWidth` de todas las `<img>` visibles > 0 (la única en 0 era `#lightbox-img`, placeholder oculto que se llena al abrir la galería; el 2026-07-24 se le retiró el `src=""` para que el navegador no resuelva una petición a la raíz).

## Pendientes

**Historial de git (decisión abierta).** El repositorio es público desde 2026-09-11. El teléfono
y la dirección reales se retiraron del sitio ese mismo día, pero **siguen presentes en commits
anteriores**, que son igualmente públicos. Quitarlos del todo exige reescribir el historial y un
`push --force`; mientras no se haga, el dato es recuperable por cualquiera que clone el repo.

**De negocio (no técnicos), para cuando el negocio sea cliente y facilite sus datos:**

- Teléfono y WhatsApp propios del negocio.
- Dirección exacta y horario (hoy el sitio los declara "por confirmar" a propósito).
- Fotografías reales en lugar de las de stock (el aviso superior ya lo declara).

Mientras siga siendo una demo conceptual, estos tres campos **deben permanecer genéricos**: son
datos de un tercero que no ha dado su acuerdo para publicarlos.
