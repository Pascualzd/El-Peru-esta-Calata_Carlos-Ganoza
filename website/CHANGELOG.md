# Edición digital — registro de cambios

Revisión del andamiaje técnico de `index.html`. **El texto del libro no se tocó:**
un diff del contenido visible contra la versión anterior devuelve cero diferencias,
salvo la llamada a la nota 4 que faltaba y el enlace «Saltar al contenido».

## Errores corregidos

| # | Problema | Detalle |
|---|----------|---------|
| 1 | La búsqueda se caía | Se construía `new RegExp("(" + consulta + ")")` con el texto crudo del usuario. Escribir un paréntesis —«PBI (»— lanzaba `SyntaxError` y la búsqueda dejaba de responder. Ahora no se usa expresión regular: el resaltado se hace por índice de carácter. |
| 2 | Inyección de HTML en los resultados | El fragmento y la consulta se insertaban con `innerHTML` sin escapar; `<img src=x onerror=...>` llegaba al DOM. Todo pasa ahora por una función de escape. |
| 3 | Enlace roto `#ref4` | La nota 4 tenía enlace de vuelta pero su llamada nunca se insertó en el cuerpo. Restituida sobre «…naturaleza económica e institucional». 75 llamadas ↔ 75 notas. |
| 4 | Búsqueda superficial | Devolvía una sola coincidencia por sección (15 como máximo en todo el libro) y dejaba al lector al inicio de un capítulo de 20 000 palabras. Ahora indexa 495 bloques: «productividad» pasa de 8 a 30 resultados y cada uno lleva a la frase exacta, que parpadea al llegar. |
| 5 | Las preferencias se perdían | Tema, tipografía y cuerpo de letra se reiniciaban en cada recarga. Persisten en `localStorage` y se aplican en el `<head>`, antes del primer pintado. |
| 6 | El índice móvil no se podía cerrar | Ocupaba la pantalla completa sin fondo ni botón de cierre, y los enlaces lo dejaban abierto. Es un cajón lateral con fondo oscurecido, bloqueo de desplazamiento y cierre por enlace, fondo o `Esc`. |
| 7 | Cálculo de posición desviado | El resaltado de capítulo usaba `offsetTop`, relativo al ancestro posicionado: 65 px de desfase. Se usa la posición absoluta en el documento. |
| 8 | CDN sin versión fijada | `cdn.tailwindcss.com` y `chart.js` apuntaban a «la última»: una versión mayor rompería la página sin avisar. Fijadas en Tailwind 3.4.16 y Chart.js 4.4.7. |

## Accesibilidad

- Los 14 gráficos eran invisibles para un lector de pantalla. Cada `<canvas>` lleva `role="img"` y un `aria-label` con título y series, **y una tabla de datos desplegable** generada desde el propio gráfico (sin duplicar cifras).
- Nombres accesibles en los botones de icono, el selector de capítulo y el campo de búsqueda.
- Enlace «Saltar al contenido», foco visible propio, región `aria-live` para anunciar cambios.
- El modal de búsqueda es un `dialog` real: `aria-modal`, trampa de foco, bloqueo de fondo y devolución del foco al cerrar.
- Se respeta `prefers-reduced-motion` y, si nunca se eligió tema, `prefers-color-scheme`.

## Lectura

- **Capítulo actual resaltado** en el índice y sincronizado con el selector de la cabecera. La clase `.sidebar-link` existía en el marcado pero no tenía estilo ni lógica: era código muerto.
- **Notas al pie emergentes** al pasar el cursor o al enfocar con teclado, sin perder el salto clásico.
- Botón «volver arriba» a partir de 700 px de desplazamiento.
- Los controles de tamaño de letra ya no se ocultan en móvil, donde más falta hacen.
- Atajos: `Ctrl/⌘+K` o `/` para buscar, `t` para cambiar de tema, `j`/`k` para saltar de capítulo.
- Hoja de estilos de impresión: oculta la interfaz, abre las tablas de datos, evita cortes dentro de gráficos y citas, y empieza cada capítulo en página nueva.

## Rendimiento

- Los 14 gráficos se construían al cargar. Ahora se dibujan al acercarse a la pantalla (`IntersectionObserver`, margen de 400 px) y todos de golpe antes de imprimir.
- El manejador de desplazamiento pasa por `requestAnimationFrame` y escucha en modo `passive`.
- El tema sepia ya no depende de sobreescribir clases de Tailwind con `!important` desde `body`: se resuelve con `html[data-theme]`, lo que además elimina el parpadeo al cargar. Los gráficos ahora también siguen al tema sepia, no solo a claro y oscuro.

## Metadatos

`meta description`, Open Graph, Twitter Card, `theme-color`, `color-scheme` y favicon SVG incrustado (sin peticiones externas).

## Nota

Tailwind se sirve desde su CDN de desarrollo, que imprime un aviso en consola y pesa unos 400 KB.
Para publicar conviene compilar el CSS con la CLI de Tailwind y servir solo las clases usadas.
Es el único cambio de fondo que queda pendiente y exige un paso de compilación.
