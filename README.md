# Portafolio — Pedro Escobar Céspedes

Sitio personal de un ingeniero backend senior: tres casos de trabajo contados en dos
niveles —un resumen de problema, decisión y resultado, y el caso completo detrás de un
desplegable—, el stack que uso y en qué estoy trabajando hoy.

Publicado en **[pedescoces.cl](https://pedescoces.cl/)** sobre Cloudflare Workers.

## Estructura

```
index.html                   Contenido completo del sitio (una sola página)
styles.css                   Estilos
assets/favicon.svg           Ícono principal, fuente de los dos siguientes
assets/favicon.ico           Fallback multi-tamaño (16, 32 y 48 px)
assets/apple-touch-icon.png  180×180 para la pantalla de inicio en iOS
assets/og.svg                Fuente editable de la tarjeta social
assets/og.png                1200×630 para la previsualización al compartir el enlace
```

No hay framework, bundler ni dependencias: HTML y CSS servidos tal cual. La única
dependencia externa es la tipografía IBM Plex (Sans, Serif y Mono) desde Google Fonts.

Los íconos y la tarjeta social viven en `assets/`. Como las rutas se declaran una por una
en el `<head>`, ningún navegador depende de encontrarlos en la raíz.

## Desarrollo local

Basta con abrir `index.html` en el navegador. Para servirlo por HTTP:

```bash
python3 -m http.server 8000
# http://localhost:8000
```

## Despliegue

El sitio corre como un Worker de Cloudflare con assets estáticos, bajo **Workers & Pages**
en el panel. El repositorio está conectado, así que cada push a `main` publica
automáticamente. No hay paso de build: los archivos se sirven tal cual desde la raíz.

La configuración del despliegue vive en el panel de Cloudflare, no en este repositorio: no
hay `wrangler.toml` ni script de Worker que versionar.

### Dominios

`pedescoces.cl` es la URL canónica, y así lo declaran el `<link rel="canonical">` y las
etiquetas `og:` del HTML.

`www.pedescoces.cl` redirige al apex con un **301** mediante una Redirect Rule
(*Rules → Redirect Rules*), con patrón comodín `https://www.pedescoces.cl/*` hacia
`https://pedescoces.cl/${1}`, preservando ruta y query string. Sin esa regla los dos
hostnames servirían el mismo contenido, que es lo que había antes de configurarla.

Para comprobar que sigue en pie:

```bash
curl -sI https://www.pedescoces.cl/ | grep -iE '^HTTP|^location'
# HTTP/2 301
# location: https://pedescoces.cl/
```

### Regenerar `og.png`

El `og.svg` se dibuja sobre un lienzo cuadrado de 1200×1200 y la tarjeta ocupa la banda
central; `qlmanage` escala mal los SVG apaisados, así que se rasteriza cuadrado y se
recorta después:

```bash
qlmanage -t -s 1200 -o assets assets/og.svg
sips -c 630 1200 assets/og.svg.png --out assets/og.png && rm assets/og.svg.png
```

Requiere las tres tipografías instaladas localmente:
`brew install --cask font-ibm-plex-sans font-ibm-plex-serif font-ibm-plex-mono`.

## Decisiones de diseño

- **Sin JavaScript.** El sitio es texto; no necesita nada más para cumplir su función. El
  plegado de cada caso usa `<details>` y `<summary>` nativos, así que el contenido está
  siempre en el HTML: se indexa, se busca con ⌘F y se imprime aunque esté cerrado.
- **Dos niveles de lectura.** Quien revisa cien portafolios no lee tres mil palabras. Cada
  caso abre con un titular en lenguaje llano y un resumen de tres líneas —problema,
  decisión, resultado— y guarda el relato completo tras *El caso completo*. La decisión se
  compone más grande que el resto del resumen: es lo único que hay que recordar del caso.
- **Cifras antes que párrafos.** Los números que estaban enterrados en la prosa (once años,
  tres meses de migración, un equipo de dos) suben a una banda al inicio.
- **Diagramas en HTML, no en imagen.** Los tres de los casos —el antes y después de la
  migración, el despacho de logs por lote y lo que el jugador espera dentro del request—
  están hechos con CSS. Un SVG con texto dentro se escala con su `viewBox` y en un teléfono
  deja las etiquetas en siete píxeles; así el texto reflows, se busca, se copia y se
  imprime. Las dos barras del tercero están a la misma escala: la parte sólida es la espera.
- **Tres áreas como índice.** Las tarjetas bajo las cifras nombran las áreas —arquitectura
  y migración, observabilidad, rendimiento— y son, a la vez, la única navegación del sitio:
  cada una ancla a su caso.
- **Retícula de etiqueta y contenido.** Cada bloque lleva su rótulo (`Contexto`,
  `El problema`, `Qué decidí`…) en una columna lateral que colapsa sobre el contenido en
  pantallas angostas.
- **Ancho de lectura acotado.** El cuerpo se mantiene en una medida legible y en serif;
  los títulos y el stack van en sans, y los rótulos de sección en mono versalitas.
- **Paleta oscura, sin alternativa clara.** Fondo tinta con una retícula tenue y un acento
  ámbar que marca los rótulos y el filete de cada sección. `color-scheme: dark` declarado.
- **Hoja de impresión.** En papel el sitio se vuelve documento: fondo blanco, tinta negra,
  sin la navegación por áreas, con los casos desplegados y con la URL impresa junto a cada
  enlace. Forzar el despliegue necesita dos reglas, `::details-content` para los
  navegadores actuales y `display` sobre los hijos para los anteriores. Las tramas y los
  rellenos de los diagramas llevan `print-color-adjust: exact` para no desaparecer en papel.
- **Ícono sobre grilla par.** El `favicon.svg` es una ventana de terminal con las iniciales
  dibujadas como rectángulos, no como texto, para no depender de tipografías del sistema.
  Todas las coordenadas del `viewBox` de 32 son pares: así, al rasterizar a 16 px, cada
  unidad cae en un píxel entero y los trazos no se emborronan.
- **Previsualización al compartir.** `og.png` reusa la paleta, el ícono y el mismo reparto
  tipográfico del sitio: Sans en el nombre, Serif en la bajada y Mono en el dominio. Las
  etiquetas `og:image` apuntan a una URL absoluta, porque el scraper que la lee no tiene
  la página como contexto.
- **Íconos como sprite.** Los cuatro íconos —correo, LinkedIn, GitHub y el galón de los
  desplegables— viven en un `<svg>` oculto al inicio del `body` y se referencian con
  `<use>`, así no se repite el marcado. Heredan `currentColor`, de modo que toman solos el
  ámbar del hover. El subrayado del enlace va en un `span` interno y no en el `<a>`, para
  que no cruce por debajo del ícono.
- **Accesibilidad.** HTML semántico, `aria-label` en las secciones, foco visible y respeto
  por `prefers-reduced-motion`.

## Contenido

Los casos describen trabajo real en un backend de plataforma de videojuegos móviles:
migración desde PlayFab CloudScript a un monolito contenerizado sobre AKS, construcción de
un manejador de middlewares y un logger por lotes para un runtime sin pipeline de request,
y el traslado fuera del request de todo lo que el jugador no necesita esperar.
