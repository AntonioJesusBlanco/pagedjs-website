---
title: "Contenido generado en cajas de margen"
date: 2019-09-03T18:23:22+02:00
draft: false
class: documentation
intro: "Encabezados corridos, notas al pie, contenido en márgenes, etc."
part: 07
---


## Cajas de margen de una página

Una caja de página se compone de dos tipos de áreas: el área de página y las cajas de margen de la página.

El **área de página** es el área de contenido de una caja de página. Es el espacio en el que fluirá todo tu contenido HTML. Cuando este contenido se queda sin espacio, se crea automáticamente otra página. Es lo que hace la parte "chunker" de Paged.js.

Los márgenes de una página se dividen en dieciséis cajas donde puedes colocar contenido generado como números de página y encabezados corridos. Estas cajas se llaman **cajas de margen**.

Cada una tiene su propio margen, borde, padding y área de contenido. Por defecto, sus tamaños están determinados por el margen de la caja de página.

La siguiente figura representa las dieciséis cajas de margen de una página definidas por el W3C:

<figure> <img src="/images/margin-boxes.png"  /></figure>

Puedes seleccionar estas cajas de margen en las reglas `@page` con reglas como `@top-left`, `@bottom-right-corner`, `@left-middle`, etc. Puedes añadir contenido a una caja de margen usando la propiedad `content`.


El siguiente código coloca tu título en la caja de margen `@top-right` de todas las páginas derechas:

```css
@page: right {
  @top-right {
    content: "My title";
  }
} ;
```

List of the margin boxes:

```css
@top-left-corner {
}
@top-left {
}
@top-center {
}
@top-right {
}
@top-right-corner {
}
@left-top {
}
@left-middle {
}
@left-bottom {
}
@right-top {
}
@right-middle {
}
@right-bottom {
}
@bottom-left-corner {
}
@bottom-left {
}
@bottom-center {
}
@bottom-right {
}
@bottom-right-corner {
}
```

## Contador de páginas

Para definir los números de página, Paged.js utiliza un contador CSS que se incrementa con cada nueva página.

Para insertar un número de página en una página o recuperar el número total de páginas de un documento, el W3C propone un contador específico llamado `page`.
La declaración del contador debe usarse dentro de una propiedad `content` en la declaración de las cajas de margen. El siguiente ejemplo declara el número de página en la caja inferior izquierda:

```css
@page {
  @bottom-left {
    content: counter(page);
  }
}
```

También puedes añadir un poco de texto antes del número de página:

```css
@page {
  @bottom-left {
    content: "page " counter(page);
  }
}
```

Para obtener el número total de páginas del documento, puedes escribir lo siguiente:

```css
@page {
  @bottom-left {
    content: "Page " counter(page) " of " counter(pages);
  }
}
```

**Reiniciar el contador de páginas**

Por ahora, reiniciar el conteo de páginas a 1 es la única opción posible. Consulta el [Issue #31](https://gitlab.coko.foundation/pagedjs/pagedjs/issues/91) para seguir el estado de esta funcionalidad.

## Named String: encabezados y pies clásicos

La forma más rápida de crear encabezados o pies de página corridos es usar contenido que ya existe.
Las **named strings** se utilizan para crear encabezados y pies corridos: copian texto para reutilizarlo en las cajas de margen.

Primero, el contenido de texto del elemento seleccionado se clona en una named string usando `string-set` con un identificador personalizado (en el ejemplo se llama “title”, pero puedes nombrarlo como quieras). En el siguiente ejemplo, cada vez que aparece un nuevo `<h2>` en el HTML, el contenido de la named string se actualiza con el texto de ese `<h2>` (también puede seleccionarse con una clase si lo prefieres).

```css
h2 {
  string-set: title content(text);
}
```

Luego, la función `string()` copia el valor de la named string en las cajas de margen mediante la propiedad `content`.

```css
@page {
  @bottom-center {
    content: string(title);
  }
}
```

La propiedad string actúa como una variable. Lee el DOM y cada vez que se encuentra un nuevo título de nivel 2, cambia el valor desde la página donde aparece ese título.
Este valor se pasa a las cajas de margen de esa página y a todas las siguientes hasta que aparece un nuevo título.

<figure> <img src="/images/string-set.png"  /></figure>

### Seleccionar contenido en string-set

Esta parte no funciona correctamente en Paged.js por ahora. Issues[#45](https://gitlab.coko.foundation/pagedjs/pagedjs/issues/45), [#42](https://gitlab.coko.foundation/pagedjs/pagedjs/issues/42).

La documentación necesita completarse.

Puedes especificar qué parte del elemento deseas seleccionar para construir el valor de la named string. Este argumento es obligatorio.

- `string-set: <identifier> content(text)`: <br> Cadena de texto del elemento (valor por defecto)
- `string-set: <identifier> content(first-letter)`: <br> Primera letra del elemento (según `::first-letter`)
- `string-set: <identifier> content(before)`: <br> Valor del pseudo-elemento `::before`
- `string-set: <identifier> content(after)`:<br> Valor del pseudo-elemento `::after`
- `string-set: <identifier> attr(<identifier-attr>)`: <br> Devuelve el valor del atributo definido por un identificador del atributo (`data-attribute`, `href`, `title`, etc.)

Es posible definir múltiples valores en el mismo `string-set`.

Ejemplo:

```css
h2::before {
  content: "Chapter " counter(countChapter, upper-roman);
}

h2 {
  string-set: titleBefore content(before), title content(text);
}

@page {
  @bottom-center {
    content: string(titleBefore) ". " string(title, first);
  }
}
```

### Estilizar una named string

El contenido se copia, por lo que para estilizarlo debes aplicar los estilos directamente en la caja de margen.

Por ejemplo, para poner el título en mayúsculas y con un tamaño de 11px:

```css
@page {
  @bottom-center {
    content: string(title);
    text-transform: uppercasse;
    font-size: 11px;
  }
}
```

Este es el DOM creado cuando una named string se inserta en una caja de margen:

```html
<div class="pagedjs_margin pagedjs_margin-bottom-center hasContent">
  <div class="pagedjs_margin-content">::after</div>
</div>
```

El contenido de la caja de margen se inserta en la propiedad `content` del pseudo-elemento `::after` del `div` con la clase "pagedjs_margin-content".

También puedes usar esta clase y el pseudo-elemento `::after` para estilizar.

## Elementos corridos: encabezados/pies con contenido complejo

Hay casos en los que string-set no es adecuado para encabezados o pies corridos complejos. Por ejemplo, cuando necesitas:

- conservar etiquetas HTML (`<em>`, `<span>`, `<br>`, etc.)
-insertar imágenes o pictogramas (`<img>`, `<svg>`)
- acortar un título que es demasiado largo y hacerlo de forma semántica
  (no usar la propiedad `text-overflow: ellipsis;`, sino reemplazar el título por una parte significativa)
- repetir elementos complejos (direcciones, datos de contacto, etc.)

Para esto puedes usar **elementos corridos** (running elements) con la propiedad `position` y la función `element().` La propiedad `position` elimina el elemento del flujo normal (en lugar de copiarlo, como hace la propiedad `string-set`) y lo mueve a las cajas de margen mediante la función `element().`

Esta técnica permite conservar toda la estructura HTML del elemento, pero requiere añadir elementos dedicados en el HTML.

En el siguiente ejemplo, queremos conservar las cursivas contenidas en el título.

```html
<section id="chapitre-4">
  <h1>The protagonist of <em>Macbeth<em></h1>
  <p><em>Macbeth</em> is a tragedy by William Shakespeare; it is thought to have been first performed in 1606. It dramatises the damaging physical and psychological effects of political ambition on those who seek power for its own sake...</p>
</section>
```

Primero, añade un elemento dedicado para el título corriente (running title) en tu HTML (justo después del título) y copia ahí tu título. Aquí es un párrafo con la clase `.title`.

```html
<section id="chapitre-4">
  <h1>The protagonist of <em>Macbeth<em></h1>
  <p class="title">The protagonist of <em>Macbeth<em></p>
  <p><em>Macbeth</em> is a tragedy by William Shakespeare; it is thought to have been first performed in 1606. It dramatises the damaging physical and psychological effects of political ambition on those who seek power for its own sake...</p>
</section>
```

Después, establece la propiedad `position` del elemento en running. Aquí, “titleRunning” es un identificador personalizado; puedes llamarlo como tenga más sentido para ti.

```css
.title {
  position: running(titleRunning);
}
```

Luego, coloca el elemento dentro de una caja de margen usando la función `element()` a través de la propiedad `content`:

```css
@page {
  @top-center {
    content: element(titleRunning);
  }
}
```

El elemento `.title` se elimina del flujo normal y se repite en las cajas de margen superiores. Funciona como una named string: cuando se encuentra un nuevo `.title`, se actualiza a partir de esa página.

Nota: `element()` no puede combinarse con otros valores de `content`.

### Estilizar elementos corridos

Como el elemento se copia, todos los estilos se copian con él. Es decir, si has estilizado tu elemento `.title`, los estilos se conservarán en los márgenes.

Con el siguiente código, tu encabezado corrido aparecerá en mayúsculas y con un tamaño de 11px:

```css
.title {
  position: running(titleRunning);
  text-transform: uppercasse;
  font-size: 11px;
}

@page {
  @top-center {
    content: element(titleRunning);
  }
}
```

Este es el DOM creado cuando mueves un elemento corrido a un margen:

```html
<div class="pagedjs_margin pagedjs_margin-top-center hasContent">
  <div class="pagedjs_margin-content">
    <p class="title">The protagonist of <em>Macbeth<em></p>
  </div>
</div>
```

Puedes ver que el párrafo se mantiene en el margen junto con todo su contenido. Puedes aplicar estilos al párrafo o a los márgenes gracias al cascading.

## Seleccionar elementos de la página para encabezados/títulos corridos

El valor de una named string o de un elemento corrido puede cambiar varias veces en una página (por ejemplo, si hay múltiples títulos del mismo nivel en la misma página).
Puedes añadir un segundo argumento opcional en la función string() o element() para indicar qué elemento de la página se debe usar si hay más de uno.
Este argumento especifica el valor de la named string y puede combinarse con otros posibles valores de la propiedad content.

- `string(<identifier>, first)`: <br> Usa el valor de la primera asignación en la página (por defecto)
- `string(<identifier>, start)`: <br> Usa el valor asignado al inicio de la página. Si el elemento es el primero en la página, se usa ese. Si no, se usa el elemento de la página anterior.
- `string(<identifier>, last)`: <br> Usa el valor del último elemento en la página.
- `string(<identifier>, first-except)`: <br> Si el valor se asigna en la página, el elemento corrido no aparece en esta página, pero sí en las siguientes.

Los tres primeros argumentos son útiles para diccionarios o glosarios. La figura muestra qué valor aparece según el argumento:

<figure> <img src="/images/string-argument.png"  /></figure>

## Eliminar contenido generado en páginas en blanco

Los saltos de página forzados pueden crear páginas en blanco, por ejemplo, para asegurar que un nuevo capítulo comience en la página izquierda o derecha deseada.
El selector de pseudo-clase `:blank` selecciona páginas sin contenido.
Para eliminar el contenido generado en páginas en blanco, simplemente usa `content: none` en las cajas de margen de esas páginas.
```css
@page: blank {
  @top-left {
    content: none;
  }
} ;
```

## Estilizar cajas de margen y contenido generado

Puedes estilizar las cajas de margen aplicando estilos directamente en las at-rules de las cajas de margen.

```css
@page {
  @top-left {
    content: "My title";
    padding-left: 15mm;
    color: #ff5733;
  }
}
```

### Alineación por defecto del contenido generado

Cada caja de margen tiene alineaciones por defecto (mostradas en la siguiente tabla).
Puedes cambiarlas fácilmente usando las propiedades text-align y vertical-align dentro de las at-rules de las cajas de margen.

#### Tabla de alineación por defecto

| Margin box           | `text-align` | `vertical-align` |
| -------------------- | ------------ | ---------------- |
| @top-left-corner     | right        | middle           |
| @top-left            | left         | middle           |
| @top-center          | center       | middle           |
| @top-right           | right        | middle           |
| @top-right-corner    | left         | middle           |
| @left-top            | center       | top              |
| @left-middle         | center       | middle           |
| @left-bottom         | center       | bottom           |
| @right-top           | center       | top              |
| @right-middle        | center       | middle           |
| @right-bottom        | center       | bottom           |
| @bottom-left-corner  | right        | middle           |
| @bottom-left         | left         | middle           |
| @bottom-center       | center       | middle           |
| @bottom-right        | right        | middle           |
| @bottom-right-corner | left         | middle           |


### Aplicar estilos al contenido generado

Puedes especificar que algunas reglas CSS solo apliquen a la caja de margen, mientras que otras solo apliquen al contenido generado. Depende de cómo hayas creado el contenido generado.
**Con position: running()**

Si usaste `position: running`, los estilos del contenido generado deben declararse en el elemento corrido, y los estilos de la caja de margen en las at-rules de las cajas de margen.

```css
.running {
  position: running(chapTitle);
  font-size: 12px;
  text-transform: uppercase;
}

@page: left {
  @top-left {
    content: element(chapTitle);
    vertical-align: top;
    padding-top: 24px;
  }
} ;
```

**Con string-set**

Si usaste `string-set`, todos los estilos se declaran en la caja de margen y se aplican a ella. Si quieres que algunas reglas afecten solo al texto y no a toda la caja, debes usar las clases creadas por Paged.js para dirigirte al contenido.

Por ejemplo, si aplicas background-color y padding en las at-rules, se aplican a toda la caja:

```css
@page: left {
  @top-left {
    background-color: #ffd2b5;
    color: #fe4017;
    padding: 2mm 5mm;
  }
} ;
```

Resultado:

<figure> <img src="/images/marginbox-style-01.png"  /></figure>

Si quieres que el background-color y el padding solo se apliquen al contenido generado, debes usar el div especial creado por Paged.js: `pagedjs_margin-content`.

```css
.pagedjs_left_page .pagedjs_margin-top-left .pagedjs_margin-content {
  width: auto;
  background-color: #ffd2b5;
  color: #fe4017;
  padding: 2mm 5mm;
}
```

Resultado:

<figure> <img src="/images/marginbox-style-02.png"  /></figure>

### Definir ancho y alto de las cajas de margen

La altura y el ancho de las cajas de margen se calculan automáticamente por Paged.js, pero puedes definir fácilmente el tamaño usando valores relativos (`%`) o absolutos (`mm`, `in`, `px`).

```css
@page {
  @left-top {
    width: 28mm;
    height: 10mm;
  }
}
```

### Rotar cajas de margen

Usando la propiedad `transform()` puedes rotar fácilmente las cajas de margen de tu documento:

```css
@page {
  @left-top {
    width: 28mm;
    height: 10mm;
    transform: rotate(-90deg);
    transform-origin: top left;
    position: relative;
    top: 28mm;
  }
}
```

Resultado:

<figure> <img src="/images/marginbox-style-03.png"  /></figure>

## Renderizado de cajas de margen con Paged.js

Paged.js utiliza CSS Grid y Flexbox para crear las cajas de margen de la página.
Las figuras siguientes muestran cómo se colocan las cajas de margen usando las clases div.

### Cajas de margen en la página

<figure> <img src="/images/margin-boxes_grid_01.png" /></figure>

La página consiste en cuatro márgenes de esquina y cuatro grupos de márgenes colocados en una grid de tres columnas y tres filas.
La grid usa variables de margen creadas por Paged.js, basadas en tus declaraciones de márgenes y tamaño de página, para definir el tamaño de los elementos.

**Plantilla de la grid**

```css
.pagedjs_pagebox {
  grid-template-columns:
    [left] var(--pagedjs-margin-left)
    [center] calc(
      var(--pagedjs-pagebox-width) - var(--pagedjs-margin-left) - var(--pagedjs-margin-right)
    )
    [right] var(--pagedjs-margin-right);
  grid-template-rows:
    [header] var(--pagedjs-margin-top)
    [page] calc(
      var(--pagedjs-pagebox-height) - var(--pagedjs-margin-top) - var(--pagedjs-margin-bottom)
    )
    [footer] var(--pagedjs-margin-bottom);
}
```

**Clases de los márgenes de esquina (y posición en la grid)**

- `div.pagedjs_margin-top-left-corner-holder` (grid-column: `left` / grid-row: `header`)

- `div.pagedjs_margin-top-right-corner-holder` (grid-column: `right` / grid-row: `header`)

- `div.pagedjs_margin-bottom-left-corner-holder` (grid-column: `left` / grid-row: `footer`)

- `div.pagedjs_margin-bottom-right-corner-holder` (grid-column: `right` / grid-row: `header`)

**Clases de los grupos de márgenes (y posición en la grid)**

- top page margins: `div.pagedjs_margin-top` (grid-column: `center` / grid-row: `header`)
- bottom page margins: `div.pagedjs_margin-bottom` (grid-column: `center` / grid-row: `bottom`)
- left page margins: `div.pagedjs_margin-left` (grid-column: `left` / grid-row: `page`)
- right page margins: `div.pagedjs_margin-right` (grid-column: `right` / grid-row: `page`)

### Grupos de cajas de margen

Cada grupo contiene tres cajas de margen en una grid de dirección única (horizontal para top/bottom, vertical para left/right).

<figure> <img src="/images/margin-boxes_grid_02.png"  /></figure>

**Márgenes superiores**

- `div.pagedjs_margin-top-left` (A)
- `div.pagedjs_margin-top-center` (B)
- `div.pagedjs_margin-top-right` (C)

**Márgenes inferiores**

- `div.pagedjs_margin-bottom-left` (A)
- `div.pagedjs_margin-bottom-center` (B)
- `div.pagedjs_margin-bottom-right` (C)

**Márgenes izquierdos**

- `div.pagedjs_margin-left-top` (A)
- `div.pagedjs_margin-left-middle` (B)
- `div.pagedjs_margin-left-bottom` (C)

**Márgenes derechos**

- `div.pagedjs_margin-right-top` (A)
- `div.pagedjs_margin-right-middle` (B)
- `div.pagedjs_margin-right-bottom` (C)

### Contenido generado en las cajas de margen

Cada caja de margen se muestra con `flex` y contiene un `div` con la clase `pagedjs_margin-content` donde se inserta el contenido generado.
Para más detalles, ver las secciones: “Estilizar named string” y “Estilizar elementos corridos”.

### Reglas de cálculo para grupos de cajas de margen

Si no se define tamaño para las cajas de margen en tu hoja de estilos, Paged.js calcula automáticamente los tamaños según estas reglas:

- Para márgenes top y bottom, la altura es el 100% del grupo de margen.
- Para márgenes left y right, el ancho es el 100% del grupo de margen.

Con respecto al ancho de los márgenes superior e inferior de la página y a la altura de los márgenes izquierdo y derecho, las reglas de cálculo siguen los mismos patrones. Estos patrones dependen de cuántos márgenes se generan (o se llenan) en el grupo, es decir, si se ha definido `content` en las at-rules para las cajas de márgenes de la página.

Aquí, para explicar estos patrones de cálculo, usamos letras que corresponden a las tres cajas de cada grupo. “Size” corresponde a la “anchura” (width) para los márgenes superior e inferior de la página y a la “altura” (height) para los márgenes izquierdo y derecho.

#### Si solo se genera una caja de margen

Si solo se genera una de las cajas, ocupa todo el ancho/alto del grupo.

<figure> <img src="/images/margin-boxes_size_sample-01.png"  /></figure>

#### Si se generan dos cajas de margen

**Si se generan A y C**

Sin tamaño definido: el tamaño de B es `0`, y A y C se extienden a lo largo del grupo de márgenes. Su tamaño es relativo a la longitud del contenido generado.

<figure> <img src="/images/margin-boxes_size_sample-02.png"  /></figure>

Con un tamaño definido (A o C): el tamaño de B sigue siendo `0`. A y C se extienden sobre el grupo de márgenes, y la caja de margen cuyo tamaño no está definido ocupa el espacio restante del grupo.

<figure> <img src="/images/margin-boxes_size_sample-03.png"  /></figure>

Con los dos tamaños definidos (A y C): A se alinea a la izquierda del grupo de márgenes, C se alinea a la derecha, y B ocupa el espacio restante, aunque no tiene contenido.

<figure> <img src="/images/margin-boxes_size_sample-04.png"  /></figure>

**Si se generan A y B o B y C**

Sin tamaño definido: el tamaño de las cajas de margen será relativo a la longitud del contenido generado. La caja central siempre estará centrada (“regla del centro”), es decir, el tamaño de A será siempre igual al de C.

<figure> <img src="/images/margin-boxes_size_sample-05.png"  /></figure>

Con un tamaño definido (A, B o C): las otras dos cajas de margen (sin tamaño definido) ocuparán el espacio restante. La “regla del centro” sigue siendo válida, por lo que los tamaños automáticos de las dos cajas de margen se distribuyen según esta regla.

<figure> <img src="/images/margin-boxes_size_sample-06.png"  /></figure>

Con dos tamaños definidos: las dos cajas de margen con tamaño definido tendrán el tamaño declarado. La tercera caja de margen (sin contenido) ocupará el espacio restante del grupo.

<figure> <img src="/images/margin-boxes_size_sample-07.png"  /></figure>

#### If all margin box are generated

Sin tamaños definidos: el tamaño de las cajas de margen será relativo al contenido generado. La caja central siempre estará en el medio (“regla del centro”), es decir, el tamaño de A siempre será igual al de C.

<figure> <img src="/images/margin-boxes_size_sample-08.png"  /></figure>

Con un tamaño definido (A, B o C): las otras dos cajas (sin tamaño definido) ocuparán el espacio restante. La “regla del centro” sigue siendo válida, por lo que los tamaños automáticos de las dos cajas se distribuirán según esta regla.

<figure> <img src="/images/margin-boxes_size_sample-09.png"  /></figure>

Con dos tamaños definidos: las dos cajas con tamaño declarado mantendrán ese tamaño. La tercera caja (sin contenido) ocupará el espacio restante del grupo.

<figure> <img src="/images/margin-boxes_size_sample-10.png"  /></figure>

Con todos los tamaños definidos: todas las cajas de margen tendrán el tamaño declarado. Se alinearán a la izquierda para los márgenes top/bottom y arriba para los márgenes left/right.

<figure> <img src="/images/margin-boxes_size_sample-11.png"  /></figure>
