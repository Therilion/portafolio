# Portafolio — Pedro Escobar Céspedes

Sitio personal de un ingeniero backend senior: tres casos de trabajo contados en detalle
(contexto, problema, decisión, alternativa descartada y resultado), el stack que uso y en
qué estoy trabajando hoy.

Publicado en **[pedescoces.cl](https://pedescoces.cl/)** mediante Cloudflare Pages.

## Estructura

```
index.html            Contenido completo del sitio (una sola página)
styles.css            Estilos
favicon.svg           Ícono principal, fuente de los dos siguientes
favicon.ico           Fallback multi-tamaño (16, 32 y 48 px)
apple-touch-icon.png  180×180 para la pantalla de inicio en iOS
og.svg                Fuente editable de la imagen anterior
og.png                1200×630 para la previsualización al compartir el enlace
```

No hay framework, bundler ni dependencias: HTML y CSS servidos tal cual. La única
dependencia externa es la tipografía IBM Plex (Sans, Serif y Mono) desde Google Fonts.

## Desarrollo local

Basta con abrir `index.html` en el navegador. Para servirlo por HTTP:

```bash
python3 -m http.server 8000
# http://localhost:8000
```

## Despliegue

Cloudflare Pages conectado al repositorio: cada push a `main` publica automáticamente.

| Opción | Valor |
| --- | --- |
| Framework preset | None |
| Build command | *(vacío)* |
| Build output directory | `/` |

Al no haber paso de build, Cloudflare sirve los archivos directamente desde la raíz del
repositorio.

### Regenerar `og.png`

El `og.svg` se dibuja sobre un lienzo cuadrado de 1200×1200 y la tarjeta ocupa la banda
central; `qlmanage` escala mal los SVG apaisados, así que se rasteriza cuadrado y se
recorta después:

```bash
qlmanage -t -s 1200 -o . og.svg
sips -c 630 1200 og.svg.png --out og.png && rm og.svg.png
```

Requiere las tres tipografías instaladas localmente:
`brew install --cask font-ibm-plex-sans font-ibm-plex-serif font-ibm-plex-mono`.

## Decisiones de diseño

- **Sin JavaScript.** El sitio es texto; no necesita nada más para cumplir su función.
- **Retícula de etiqueta y contenido.** Cada bloque lleva su rótulo (`Contexto`,
  `El problema`, `Qué decidí`…) en una columna lateral que colapsa sobre el contenido en
  pantallas angostas.
- **Ancho de lectura acotado.** El cuerpo se mantiene en una medida legible y en serif;
  los títulos y el stack van en sans, y los rótulos de sección en mono versalitas.
- **Paleta oscura, sin alternativa clara.** Fondo tinta con una retícula tenue y un acento
  ámbar que marca los rótulos y el filete de cada sección. `color-scheme: dark` declarado.
- **Ícono sobre grilla par.** El `favicon.svg` es una ventana de terminal con las iniciales
  dibujadas como rectángulos, no como texto, para no depender de tipografías del sistema.
  Todas las coordenadas del `viewBox` de 32 son pares: así, al rasterizar a 16 px, cada
  unidad cae en un píxel entero y los trazos no se emborronan.
- **Previsualización al compartir.** `og.png` reusa la paleta, el ícono y el mismo reparto
  tipográfico del sitio: Sans en el nombre, Serif en la bajada y Mono en el dominio. Las
  etiquetas `og:image` apuntan a una URL absoluta, porque el scraper que la lee no tiene
  la página como contexto.
- **Íconos como sprite.** Los tres íconos de contacto viven en un `<svg>` oculto al inicio
  del `body` y se referencian con `<use>`, así no se repite el marcado. Heredan
  `currentColor`, de modo que toman solos el ámbar del hover. El subrayado del enlace va
  en un `span` interno y no en el `<a>`, para que no cruce por debajo del ícono.
- **Accesibilidad.** HTML semántico, `aria-label` en las secciones, foco visible y respeto
  por `prefers-reduced-motion`.

## Contenido

Los casos describen trabajo real en un backend de plataforma de videojuegos móviles:
migración desde PlayFab CloudScript a un monolito contenerizado sobre AKS, construcción de
un manejador de middlewares y un logger por lotes para un runtime sin pipeline de request,
y el traslado fuera del request de todo lo que el jugador no necesita esperar.
