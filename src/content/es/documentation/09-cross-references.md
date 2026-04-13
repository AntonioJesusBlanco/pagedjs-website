---
title: "Referencias cruzadas"
date: 2020-01-28T15:56:05+01:00
draft: false
weight: 10
intro: "Referencias y enlaces dentro del libro: envía al lector a otra página usando hipervínculos"
---

Muchos documentos contienen referencias internas como "ver capítulo 7" o "en la página 23". La referencia puede cambiar según tu diseño (especialmente el número de página). Por lo tanto, puedes usar contenido generado para crearla.

Puedes usar valores específicos para la propiedad content para crear automáticamente estos tipos de referencias cruzadas: [target-counter()](https://www.w3.org/TR/css-gcpm-3/#funcdef-target-counter), [target-text()](https://www.w3.org/TR/css-gcpm-3/#target-text-function). Cada uno de estos muestra información obtenida del destino de un enlace.

Puedes usar estas funciones target-counter() y target-text() para crear tablas de contenido e índices de libros, por ejemplo (ver las siguientes partes).

## Link targeted

Para las referencias cruzadas, se usan enlaces que apuntan a anclas en el documento.

Primero que todo, en tu HTML, defines un elemento relevante con un id único en tu documento. Por ejemplo, la figura 3 de tu documento:

```html
<figure id="figure-3">
  <img src="image.jpg" />
  <p class="caption">Figure 3: an image</p>
</figure>
```

En otro lugar de tu HTML, creas un ancla de enlace que hace referencia al identificador único de tu elemento relevante:

```html
<p>see the <a class="link" href="#figure-3">figure 3</a></p>
```

## Target-counter()

El contenido generado se establece en tu CSS. Para encontrar las páginas en las que aparecen los elementos relevantes dentro del documento, usa la función [target-counter()](https://www.w3.org/TR/css-gcpm-3/#target-counter) en una propiedad `content` establecida en los pseudo-elementos `::before` o `::after`. Como todas las propiedades content, puede incluir algo de texto:

```css
.link::after {
  content: ", page " target-counter(attr(href url), page);
}
```

Este código toma todos los elementos con una clase nombrada aquí `link` y busca el elemento con el identificador único especificado en el atributo `href` de cada primer elemento. Cuando tu documento se renderiza, generará el número de página de la página donde aparece el elemento con el identificador único.

Con el ejemplo de abajo, el texto generado en tu documento será "see the figure 3, page 27".

También puedes especificar un estilo de contador:

```css
.link::after {
  content: target-counter(attr(href url), page, lower-roman);
}
```

Por el momento, la función `target-counter()` funciona solo con el contador page. Eso significa que no puedes usar contadores con otro nombre dado.

## Target-text()

La función [target-text()](https://www.w3.org/TR/css-gcpm-3/#target-text) funciona como target-counter() pero recupera el valor de texto del elemento referenciado por la URL. Puedes usarla para obtener el título del capítulo, por ejemplo.

En tu HTML, crea el título de un capítulo con un identificador único:

```html
<h1 id="chapter-1">Chapter 1. The beginning</h1>
```

Más adelante en tu documento, crea un enlace a este capítulo:

```html
<p>Some text that refer to the <a class="link" href="#chapter-1">chapter</a>.</p>
<p></p>
```

En el CSS, usa target-text para generar la referencia cruzada:

```css
.link::after {
  content: "(see " target-text(attr(href url)) ")";
}
```

El texto generado en tu documento será "Some text that refer to the chapter (see Chapter 1. The beginning)."

El W3C define un segundo argumento opcional que especifica qué contenido se recupera, usando los mismos valores que la propiedad string-set (`content`, `before`, `after`, `first-letter`) pero aún no está implementado en `paged.js`.

## Debug

Estas funciones solo toman la URL de fragmento que apunta a una ubicación en el documento actual. Si no hay fragmento, si el ID referenciado no está ahí, o si la URL apunta a un documento externo, la función no devuelve nada en el caso de target-text() y `0` en el caso de target-counter().Sin embargo, si tu documento es largo, tal vez tu elemento objetivo aún no se ha cargado y la función también devuelve nada o `0`. Espera hasta que la carga se complete para que el número de página sea generado.

Si tu número de página parece incorrecto, tal vez es porque el elemento que estás apuntando está fragmentado en varias páginas. Recomendamos usar elementos cortos para apuntar (títulos de capítulos en lugar de toda la sección del capítulo).