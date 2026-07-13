# Auditoría — La Tradición (Panadería) — 2026-07-12

**Stack:** HTML5 + Tailwind CSS (CDN, `cdn.tailwindcss.com`) + Font Awesome 6.4.0 (CDN) + Google Fonts (Great Vibes, Dancing Script, Inter) + Leaflet 1.9.4 (CDN, mapa interactivo) + JS vanilla inline. Sin build step, sin dependencias npm.

**Categoría:** (a) Estático puro — sin persistencia local, el formulario solo arma un link `wa.me` (redirección, no guarda datos).

**Cómo se sirve:** archivos estáticos; verificado con `python -m http.server 4131 --directory .`

## Hallazgos

| Sev | Área | Hallazgo | Evidencia | Acción |
|-----|------|----------|-----------|--------|
| Media | Assets | Hero (`img` sección #inicio) y favicon usan hotlink externo a `images.unsplash.com` en vez de asset local | `src="https://images.unsplash.com/photo-1555507036-..."`, favicon `href` también Unsplash | Funciona hoy (200 OK, naturalWidth=800 verificado). Riesgo: dependencia externa frágil (puede caducar/404 con el tiempo o fallar sin internet). Recomendado migrar a asset local en iteración futura — no se tocó (no está roto, fuera de mandato de Fase B). |
| Baja | Consola | Advertencia `cdn.tailwindcss.com should not be used in production` | Consola del navegador (2 warnings) | Esperado en demo con CDN; no bloquea funcionalidad. Documentado, no requiere acción para esta fase. |
| Info | Negocio | Número de WhatsApp `525582841488` usado en 3 CTAs + generador dinámico del formulario | `wa.me/525582841488` | El propio footer ya declara "teléfonos... deberán ser validados directamente con los administradores del negocio". Pendiente de dato real del negocio. |
| Info | CDNs | Tailwind, Font Awesome, Google Fonts, Leaflet CSS/JS | Todas responden 200 (verificado con fetch); mapa Leaflet renderiza tiles de OpenStreetMap correctamente | Sin caídos. |
| OK | Imágenes locales | 7 imágenes en `images/` (menú, galería, destacado) | Todas 200 OK, `naturalWidth>0` para las 7 | Sin roturas. |
| OK | Meta | `<title>`, `<meta description>`, `<meta viewport>`, favicon — todos presentes | Líneas 5-10 del `<head>` | Completo. |
| OK | JS | Funciones inline (menú filtrable, formulario→WhatsApp, lightbox, mapa Leaflet, scroll fade-in) | Sin errores en consola al cargar/interactuar | Sin errores de sintaxis. |
| OK | Anclas | `#inicio #esencia #menu-interactivo #destacados #galeria #ubicacion #contacto-pedido` | Revisión manual de `id=` | Sin duplicados. |
| OK | Secretos | Ningún hardcodeo de keys/tokens | Revisión visual del archivo | N/A. |

## Verificación (servidor de prueba, puerto 4131)

- Consola: 0 errores, solo advisory de Tailwind CDN (no es error).
- Network: 0 requests fallidas. Imágenes locales (7/7) y externas (hero Unsplash, favicon, tiles OSM) devuelven 200.
- `naturalWidth` de todas las `<img>` visibles > 0 (la única en 0 es `#lightbox-img`, que es un placeholder oculto con `src=""` por diseño, se llena al abrir la galería).

## Pendientes de negocio (no técnicos)

- Confirmar teléfono/WhatsApp real.
- Confirmar dirección exacta y horario (el propio sitio ya los marca como "por confirmar").
- Reemplazar fotografías de stock (Unsplash) por fotos reales del negocio cuando estén disponibles (el disclaimer superior ya lo declara).
