---
title: "Contenido generado"
date: 2019-09-03T18:23:22+02:00
draft: false
intro: "La propiedad content en CSS es una forma muy potente de añadir estructura a tu contenido sin fijarla en el HTML. Veamos cómo la web gestiona estas propiedades creadas automáticamente antes de entrar en lo que Paged.js puede generar de forma automática."
weight: 6
part: 6
---


Una de las ventajas más importantes de CSS es que ayuda a separar el estilo de un documento de su contenido. La separación entre HTML y CSS facilita el mantenimiento de los sitios, el uso compartido de hojas de estilo entre páginas y la visualización de documentos en distintos entornos. En algunos casos, ciertos elementos no forman parte del contenido en sí, sino que ayudan a su lectura en diferentes entornos. Puede tratarse de información auxiliar, como insertar la palabra "Figura" antes del pie de una imagen, o "Capítulo 7" antes del título del séptimo capítulo.

Esto suele hacerse mediante las propiedades de _contenido generado_ de CSS. De esta forma se evitan problemas al numerar figuras cuando se añade una nueva en medio del contenido, o si se quiere reutilizar un capítulo en otro libro y aplicar un nuevo sistema de numeración.

En términos técnicos, el contenido generado solo existe en el diseño del documento web: no forma parte del árbol DOM.

## La propiedad `content`

La propiedad `content` se utiliza dentro de los pseudo-elementos `::before` y `::after`. En la declaración, se especifica como valor de `content` aquello que se quiere generar automáticamente. Por ejemplo, la siguiente regla inserta la cadena "Note: " antes de cada elemento cuya clase contiene la palabra "note":

```css
.note::before {
  content: "Note: ";
}
```

Puedes aplicar estilos al elemento directamente donde se define, por ejemplo:

```css
.note::before {
  content: "Note: ";
  color: red;
  font-weight: bold;
}
```

## Texto generado

Puedes declarar directamente el texto en CSS (como en los ejemplos anteriores), pero también puedes utilizar texto especificado en un atributo personalizado `data-`.
En tu HTML:

```html
<p class="ref" data-ref-id="0215">Some blabla as a reference</p>
```

En tu CSS:

```css
p.ref::before {
  content: attr(data-ref-id);
}
```

También es posible combinar elementos dentro de la propiedad content:

```css
p.ref::before {
  content: "Reference " attr(data-ref-id) ": ";
}
```

Una vez mostrado, obtendrás el siguiente texto:

> `Reference 0215: Some blabla as a reference`

## Contadores generados

`css-counter` es una propiedad CSS que permite contar elementos dentro del contenido. Por ejemplo, puede que quieras añadir un número delante del pie de cada figura. Para hacerlo, debes reiniciar el contador en el selector `<body>`, incrementarlo cada vez que aparezca un pie de figura y mostrar ese número en un pseudo-elemento `::before`.

```css
body {
  counter-reset: figureNumber;
}

figcaption {
  counter-increment: figureNumber;
}

figcaption::before {
  content: counter(figureNumber);
}
```

## Imágenes generadas

Si necesitas incluir una imagen dentro del contenido generado, puedes hacerlo de la siguiente manera:

```css
.glossary::after {
  content: " " url("/images/glossary-icon.png");
}
```

## Enlaces generados

Puede resultar útil mostrar las URL completas de los enlaces cuando se imprime una página web. El siguiente ejemplo inserta el valor del atributo href entre paréntesis después de cada elemento `<a>`:

```css
a::after {
  content: " (" attr(href) ")";
}
```

## Contenido generado para medios paginados

El uso de contenido generado es posible sin Paged.js; `content` es una propiedad CSS implementada en todos los navegadores.

Pero para diseñar un libro (o contenido paginado) se necesitan algunos elementos adicionales que ayuden a los lectores a navegar entre páginas: encabezados y pies de página corridos, números de página, tabla de contenidos, índice, referencias cruzadas, etc.

Estos elementos no existen en el HTML como contenido, por lo que deben crearse automáticamente. Para ello, se puede usar una combinación de sintaxis y propiedades que Paged.js implementa bajo el nombre de Generated Content for Paged Media.
