# Portafolio — Pedro Escobar Céspedes

Sitio personal de un ingeniero backend senior: tres casos de trabajo contados en detalle
(contexto, problema, decisión, alternativa descartada y resultado), el stack que uso y en
qué estoy trabajando hoy.

Publicado en **[pedescoces.cl](https://www.pedescoces.cl/)** mediante Cloudflare Pages.

## Estructura

```
index.html    Contenido completo del sitio (una sola página)
styles.css    Estilos
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

## Decisiones de diseño

- **Sin JavaScript.** El sitio es texto; no necesita nada más para cumplir su función.
- **Retícula de etiqueta y contenido.** Cada bloque lleva su rótulo (`Contexto`,
  `El problema`, `Qué decidí`…) en una columna lateral que colapsa sobre el contenido en
  pantallas angostas.
- **Ancho de lectura acotado.** El cuerpo se mantiene en una medida legible y en serif;
  los títulos y el stack van en sans, y los rótulos de sección en mono versalitas.
- **Paleta oscura, sin alternativa clara.** Fondo tinta con una retícula tenue y un acento
  ámbar que marca los rótulos y el filete de cada sección. `color-scheme: dark` declarado.
- **Accesibilidad.** HTML semántico, `aria-label` en las secciones, foco visible y respeto
  por `prefers-reduced-motion`.

## Contenido

Los casos describen trabajo real en un backend de plataforma de videojuegos móviles:
migración desde PlayFab CloudScript a un monolito contenerizado sobre AKS, construcción de
un manejador de middlewares y un logger por lotes para un runtime sin pipeline de request,
y el traslado fuera del request de todo lo que el jugador no necesita esperar.
