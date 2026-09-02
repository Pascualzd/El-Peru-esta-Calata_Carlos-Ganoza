# Sistema de diseño — *El Perú está calato*, edición presentación

Referencia del sistema que gobierna `index-presentacion.html`. Todo vive en un
solo archivo, pero está organizado en capas: **tokens → reset → tipografía →
chrome → modos → componentes → visuales → impresión**.

El sistema **evoluciona** la identidad de la edición anterior (rojo Perú,
Playfair + Crimson) en lugar de sustituirla. Lo que cambia es que ahora está
declarado: no quedan valores sueltos en el marcado.

---

## 1. Tokens

### Color

Tres temas. Sólo se redefinen los **semánticos**; las rampas crudas son fijas.

| Token | Claro | Sepia | Oscuro | Uso |
|---|---|---|---|---|
| `--bg` | `#fbfaf8` | `#f3ead6` | `#0d0e11` | fondo de página |
| `--bg-raised` | `#ffffff` | `#faf3e3` | `#16181d` | tarjetas, gráficos, diagramas |
| `--bg-sunken` | `#f4f2ee` | `#eadfc6` | `#0a0b0d` | grupos de control, superposiciones |
| `--ink` | `#1b1a16` | `#2f2611` | `#eceae5` | texto principal |
| `--ink-2` | `#5a564b` | `#6b5a3a` | `#a5a29a` | texto secundario |
| `--ink-3` | `#7c7768` | `#8a7852` | `#7d7a72` | metadatos, leyendas |
| `--rule` | `#e7e3dc` | `#ddcfae` | `#26282e` | separadores de 1 px |
| `--rule-strong` | `#d3cec4` | `#c9b78e` | `#383b43` | bordes de diagrama |
| `--accent` | `#b91c1c` | `#9c3a17` | `#f0574a` | rojo Perú: capitulares, cifras, énfasis |
| `--accent-soft` | `#fae5e1` | `#f0dcc6` | `#3a1614` | fondo de resalte |
| `--focus` | `#1d6fd0` | — | — | anillo de foco, nunca es el acento |

**Paleta de datos** — seis series estables en los tres temas. Nunca se usa el
acento como color de serie salvo cuando el Perú *es* la serie destacada.

| Token | Claro / sepia | Oscuro | Papel |
|---|---|---|---|
| `--d1` | `#b91c1c` | `#f0574a` | serie destacada (Perú, «después») |
| `--d2` | `#2f6fa8` | `#5aa6e0` | serie de contraste (mundo, «antes») |
| `--d3` | `#c2872a` | `#e0a943` | commodities, tercer eje |
| `--d4` | `#12786f` | `#3fb5a6` | reserva |
| `--d5` | `#5a564b` | `#9a978e` | serie neutra |
| `--d6` | `#8b5cb8` | `#b98ae0` | reserva |

> **Regla:** ningún color se escribe literal fuera de la capa de tokens.
> El motor generativo y Chart.js **leen** los tokens con `getComputedStyle`,
> por eso el arte y los gráficos cambian de tema junto con la página.

### Tipografía

| Rol | Familia | Dónde |
|---|---|---|
| `--f-display` | Playfair Display | títulos, capitulares, cifras grandes |
| `--f-serif` | Crimson Pro | cuerpo del libro, epígrafes |
| `--f-sans` | Plus Jakarta Sans | interfaz, tarjetas, notas, bibliografía |
| `--f-mono` | JetBrains Mono | antetítulos, numeración, etiquetas de dato |

Escala: `--t--2` .6875rem · `--t--1` .78 · `--t-0` .875 · `--t-1` 1 · `--t-2` 1.1875
· `--t-3` 1.4375 · `--t-4` 1.75 · `--t-5` 2.25 · `--t-6` 3 · `--t-7` 4rem.

Dos multiplicadores se aplican encima:

- `--reader-scale` (0,85–1,35) — lo mueve el lector desde **Tipografía**.
- `--fit` (0,82–1) — lo mueve el motor para que cada diapositiva entre en
  pantalla. Ver §5.

### Espaciado, forma, elevación, movimiento

- Espaciado base 4: `--s1` .25rem … `--s10` 6rem.
- Radios: `--r-sm` 5 · `--r-md` 10 · `--r-lg` 16 · `--r-xl` 24 · `--r-pill` 999px.
- Elevación en tres pasos (`--e1` a `--e3`), redefinida por tema para que la
  sombra no se vea sucia sobre papel sepia ni invisible sobre negro.
- Movimiento: `--dur-1` 120ms · `--dur-2` 240 · `--dur-3` 480 · `--dur-4` 900,
  con `--ease` y `--ease-out`. Todo se anula bajo `prefers-reduced-motion`.

---

## 2. Componentes

### `.slide`

| Propiedad | Valores | Notas |
|---|---|---|
| `data-kind` | cover · traps · flap · legal · chapter · prose · chart · feature · notes · biblio | decide el constructor |
| `data-sec` | id de la sección del libro | fija el sistema generativo y el color de capítulo |
| `--fit` | 0,82–1 | ajuste automático al alto |
| `.is-current` | — | única visible en presentación; dispara animaciones |
| `.has-more` | — | no cupo ni al 82 %: aparece el degradado de continuidad |

Estructura interna fija:

```
.slide > .slide__scroll > [.slide__eyebrow] + .slide__grid[data-layout]
                                              ├─ .slide__text
                                              └─ .slide__visual
```

`data-layout` toma `split` (1,02fr / 0,98fr), `wide-visual` (0,82 / 1,18) o
`full`. Por debajo de 900 px todo colapsa a una columna y el visual sube primero.

### `.chartcard`

| Parte | Token | Nota |
|---|---|---|
| antetítulo | `--f-mono`, `--accent` | «Gráfico 7 (p. 54)» |
| título | `--f-display` 700 | el del libro, sin reescribir |
| lienzo | alto `min(46vh, 20rem)` | 19rem fijo en lectura |
| fuente | `--t--2`, `--ink-3` | separada por regla de 1 px |
| tabla | `<details>` | se genera desde los datos del gráfico, sin duplicar cifras |

Estados: se construye al entrar en pantalla (no al cargar); se destruye y
reconstruye al cambiar de tema; se abre en tabla al imprimir.

### `.diagram`

Diagrama SVG de concepto. Gramática compartida:

| Clase | Papel |
|---|---|
| `.dg-box` / `.dg-box--a` / `.dg-box--ghost` | caja neutra / acentuada / ausente |
| `.dg-arrow` / `--a` / `--soft` | flecha neutra / acentuada / de fondo |
| `.dg-t` / `.dg-t--sm` / `.dg-t--k` | etiqueta / secundaria / antetítulo mono |
| `.dg-t--big` | cifra protagonista |
| `.dg-line` / `.dg-line--soft` / `.dg-dash` | trazo estructural |

**Ajuste tipográfico**: el SVG no reajusta texto, así que `epcDiagram()` mide
cada etiqueta contra su caja en un contenedor oculto y reduce el cuerpo hasta
que entra (mínimo 6,6 px); si aún se sale del lienzo, la desplaza hacia dentro.
Hay una prueba (`audit-diagrams.js`) que falla si algún texto desborda.

### `.figure-chip`, `.chip`, `.pullquote`, `.plate`

Los cuatro visuales automáticos. Ver §4.

### `.card`

Tarjeta de escenario o pilar. Variantes `card--good` / `card--warn` / `card--bad`
cambian sólo el punto y el color del título, nunca el fondo.

### Controles

| Componente | Estados |
|---|---|
| `.modes` | `aria-pressed` marca el modo activo |
| `.iconbtn` | reposo · hover (`--bg-sunken`) · `.iconbtn--on` |
| `.btn` / `.btn--solid` | reposo · hover (borde acento) · activo (`--solid`) |
| `.thumb` | reposo · hover (elevación) · `.is-current` (anillo acento) |

---

## 3. Los dos modos

Un solo DOM, dos disposiciones. `body[data-mode]` conmuta entre ellas y no se
vuelve a construir nada, por eso el cambio es instantáneo y conserva la posición.

| | Presentación | Lectura |
|---|---|---|
| `#stage` | `height:100dvh; overflow:hidden` | flujo normal, `max-width:78rem` |
| `.slide` | `position:absolute`, sólo la actual visible | `position:relative`, todas en flujo |
| Rejilla | dos columnas siempre | una columna; dos desde 1180 px |
| Cabeceras de sección | ocultas | `.sec-head` con regla acentuada |
| Elementos `.deckonly` | visibles | ocultos |
| Fondo generativo | corre sólo en la diapositiva actual | corre al entrar en pantalla |
| Navegación | flechas, espacio, gestos, zonas de clic | scroll |

---

## 4. Patrón: un visual por pasaje

Cada diapositiva de prosa recibe un visual. La elección es una cascada, de más
informativo a más atmosférico:

| Orden | Tipo | Condición | Qué muestra |
|---|---|---|---|
| 1 | `diagram` | el pasaje activa la expresión regular de uno de los 30 diagramas | el mecanismo del argumento |
| 2 | `figures` | hay ≥ 2 magnitudes reconocidas | las cifras del pasaje con su cláusula |
| 3 | `quote` | hay una cita entre « » de 28–190 caracteres | la voz citada, con su autor |
| 4 | `entities` | hay ≥ 3 nombres propios verificados | quién y qué aparece aquí |
| 5 | `plate` | resto | lámina generativa del capítulo, sembrada por el texto |

**Regla que evita el ruido:** un visual nunca repite una frase que ya está en el
cuerpo de la misma diapositiva. Por eso `quote` sólo toma comillas angulares
—una voz ajena— y `entities` valida cada nombre contra un índice global de
palabras que aparecen en mayúscula **fuera** del inicio de oración, para que
«Sabiendo» o «Quizá» no se cuelen como si fueran personas.

Distribución resultante sobre 176 diapositivas: 30 diagramas, 20 paneles de
cifras, 10 citas, 40 índices de nombres, 36 láminas, 11 gráficos, 11 aperturas.

---

## 5. Patrón: ajuste al alto

En presentación una diapositiva no debería tener barra de desplazamiento. Al
entrar, `fitSlide()` mide y reduce `--fit` en pasos de 3,5 % hasta que el
contenido cabe, con suelo en 0,82. Si aún no cabe —un párrafo de mil caracteres
en una pantalla baja— se marca `.has-more` y se permite desplazar con un
degradado de aviso, que es más honesto que un cuerpo ilegible.

`--fit` afecta a párrafos, encabezados de diapositiva, notas y bibliografía; no
toca los antetítulos ni la interfaz, que deben mantener su tamaño mínimo legible.

---

## 6. Accesibilidad

- Los 14 gráficos llevan `role="img"` + `aria-label` y una tabla desplegable
  generada desde sus propios datos.
- Región `aria-live` que anuncia sección y posición al cambiar de diapositiva.
- Diálogos reales con `aria-modal`, foco devuelto al cerrar y `Esc`.
- `aria-hidden` en las diapositivas no visibles, sólo en presentación.
- Enlace «Saltar al contenido», anillo de foco propio con `--focus`.
- `prefers-reduced-motion` detiene el arte generativo y las transiciones; el
  lector también puede apagarlo a mano desde **Tipografía → Movimiento**.
- Contraste: `--ink-2` sobre `--bg` supera 7:1 en los tres temas; `--ink-3`
  supera 4,5:1 y sólo se usa en texto de apoyo.

---

## 7. Qué hacer y qué no

| ✅ | ❌ |
|---|---|
| Añadir un diagrama al catálogo con su `match` y su `cap` | Escribir un `#hex` fuera de la capa de tokens |
| Usar `--d1…--d6` para series de datos | Usar `--accent` para una serie que no es la protagonista |
| Dejar que `fitSlide` resuelva el alto | Recortar texto del libro para que quepa |
| Medir el SVG con `audit-diagrams.js` antes de publicar | Confiar en que la etiqueta entra en la caja |
| Poner en el visual algo que el texto no dice ya | Repetir en el margen la frase que está al lado |

---

## 8. Pendientes

- Los diagramas no tienen aún descripción larga (`aria-describedby`); hoy se
  apoyan en el pie de figura, que sí lee un lector de pantalla.
- El índice de nombres propios es heurístico: acierta en casi todo, pero puede
  dejar pasar un topónimo raro que sólo aparece al inicio de una oración.
- Chart.js va incrustado (205 KB). Si alguna vez sólo se usaran gráficos de
  líneas y barras, un motor propio sobre canvas ahorraría ese peso.
