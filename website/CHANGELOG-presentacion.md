# Edición presentación — registro de cambios

`index-presentacion.html` es una **versión nueva** de la edición digital, no un
reemplazo. `index.html` queda intacto como edición de lectura continua.

**El texto del libro no se tocó.** Una prueba automática compara palabra por
palabra el texto visible de las dos ediciones: de las 27 277 palabras del
original faltan 13 ocurrencias en la nueva, y las 13 son de interfaz de la
edición anterior («Saltar al contenido», el rótulo del índice lateral), no del
libro. Los 75 llamados y las 75 notas siguen emparejados, los 14 gráficos
conservan sus datos exactos y las 60 entradas de bibliografía están completas.

---

## Lo nuevo

### Dos modos sobre el mismo texto

| | Presentación | Lectura |
|---|---|---|
| Recorrido | 176 diapositivas, una por pantalla | flujo continuo, como antes |
| Avance | `→` `←`, barra espaciadora, gesto lateral, zonas de clic | scroll |
| Uso | proyectar, exponer, revisar de un vistazo | leer de corrido |

El botón **Presentación / Lectura** de la cabecera —o la tecla `M`— cambia de
modo sin recargar y **conservando la posición**: si estabas en el párrafo de los
bandidos pasajeros, ahí sigues. El modo elegido se recuerda entre visitas.

### Un visual por pasaje

Ninguna diapositiva de prosa se queda sin acompañamiento. El visual se elige por
lo que el pasaje ofrece, en este orden:

1. **30 diagramas de concepto** dibujados a mano para los argumentos que cargan
   el libro: el traje prestado, el iceberg del ruido político, los tres billetes
   de lotería, la piñata de los metales, la cadena de los cuatro booms, la
   regresión a la media, la planta Nummi, la fábrica de tortas de Baumol, las
   seis fricciones, Atlas contra Robinson Crusoe, la autopista de dos carriles,
   la paradoja del crecimiento infeliz, el bandido sedentario contra el pasajero,
   el repliegue de los partidos, el vientre de alquiler, la Pax Mafiosa, el
   monopolio fracturado, el efecto Anna Karenina, el manual del autoritario, las
   líneas de defensa, ignorancia contra incentivos, los parches, las trampas
   encadenadas y el sumo contra el leopardo, entre otros.
2. **Cifras del pasaje** — las magnitudes que el párrafo enuncia, extraídas con
   su cláusula: «6,4 %», «US$ 500 millones», «3 de cada 4», «×3,8».
3. **La voz citada** — sólo lo que el libro pone entre comillas angulares, con
   el nombre de quien habla.
4. **Quién y qué aparece aquí** — índice marginal de nombres propios e
   instituciones del pasaje, validados contra un índice de todo el libro.
5. **Lámina generativa** del capítulo, sembrada por el propio texto: la misma
   diapositiva produce siempre la misma lámina.

Un visual nunca repite una frase que ya está en el cuerpo de la diapositiva.

### Arte generativo por capítulo

Siete sistemas propios, uno por familia de trampa, corriendo sobre canvas con
semilla reproducible (ver `CALATO-filosofia-algoritmica.md`):

| Sistema | Capítulo | Qué hace |
|---|---|---|
| **Marea** | prólogo, introducción, trampa 1 | el forzante externo se retira y deja expuesto lo que sostenía |
| **Acumulación** | trampa 2 | la masa sube, el orden interno nunca baja |
| **Dos carriles** | trampa 3 | dos bandas de flujo cuya brecha se abre |
| **Pulverización** | trampa 4, epílogo | celdas que se fisionan antes de alcanzar equilibrio |
| **Monopolio roto** | trampa 5 | un atractor único se fisiona; la violencia destella en las fronteras |
| **Metástasis** | trampa 6 | agregación que parte de un nodo y coloniza los otros |
| **Tejido** | conclusión, agradecimientos | urdimbre y trama que van vistiendo la superficie |

Sólo se anima la apertura visible; el resto está detenido. Se apaga solo con
`prefers-reduced-motion` y a mano desde **Tipografía → Movimiento**.

### Gráficos

Los 14 gráficos conservan sus datos y sus fuentes. Cambia el vestido: paleta por
tokens (seis series estables en claro, sepia y oscuro), ejes discretos, sin
puntos salvo al pasar el cursor, tooltip sobre la superficie de la página y
animación de entrada. La tabla de datos accesible sigue ahí y se abre sola al
imprimir.

### Vista general e índice

`O` abre una parrilla con las 176 diapositivas, cada una con su sección, su
encabezado, un adelanto del texto y una etiqueta de qué visual lleva. `I` abre
el índice por secciones con el número de diapositivas de cada una.

### Búsqueda

Recorre las 176 diapositivas —el libro entero— y lleva al pasaje exacto, en el
modo en que estés. `⌘K` o `/`.

---

## Lo que cambia por dentro

| # | Antes | Ahora |
|---|---|---|
| 1 | Tailwind desde su CDN de desarrollo (≈400 KB, aviso en consola, exige compilar antes de publicar) | CSS propio con tokens declarados. **Desaparece el paso de compilación pendiente** del changelog anterior. |
| 2 | Chart.js desde jsDelivr | Chart.js 4.4.7 incrustado. La edición **funciona sin red**, y ningún cambio remoto puede romperla. |
| 3 | Estilos repartidos en clases de utilidad dentro del marcado | Capa de tokens única; el arte y los gráficos **leen** los tokens, por eso siguen al tema. |
| 4 | El texto vivía en el HTML | El texto vive en un modelo de contenido (`window.BOOK`) que se recorre para armar las diapositivas. Reordenar la presentación no toca el texto. |
| 5 | Tema claro / sepia / oscuro | Igual, más control de **movimiento** y de **cuerpo de letra** con ajuste automático al alto de pantalla. |

Peso: 559 KB en un solo archivo, de los cuales 205 KB son Chart.js incrustado y
188 KB el texto del libro. La única petición externa que queda son las tipografías
de Google Fonts, con familias de reserva declaradas.

---

## Atajos

| | |
|---|---|
| Avanzar / retroceder | `→` `←` `espacio` |
| Saltar de capítulo | `J` `K` |
| Vista general | `O` |
| Índice | `I` |
| Buscar | `⌘K` o `/` |
| Cambiar de modo | `M` |
| Tema | `T` |
| Inicio / final | `Inicio` `Fin` |
| Imprimir o exportar a PDF | `⌘P` |

La hoja de impresión abre todas las diapositivas en flujo, despliega las tablas
de datos, oculta la interfaz y empieza cada capítulo en página nueva.

---

## Pruebas que acompañan a esta edición

| Prueba | Qué comprueba |
|---|---|
| paridad de texto | ninguna palabra del libro se perdió |
| recorrido completo | las 176 diapositivas caben en pantalla, sin desbordes |
| auditoría de diagramas | ninguna etiqueta SVG se sale de su caja ni del lienzo |
| consola | cero errores de JavaScript en el recorrido completo |
| gráficos | los 14 se construyen y conservan todos sus puntos |
