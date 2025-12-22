---
title: Diseño web para impresión
date: 2019-09-03T18:23:22+02:00
part: 5
intro: "¡Si quieres empezar a diseñar tu propio libro, ahora es el momento perfecto!"
draft: false
---

## La consulta `@media print`

El diseño responsivo es posible gracias al uso de media queries: un conjunto de propiedades CSS que puedes usar para definir estilos cuando tu página web se renderiza en una tableta, un teléfono, una pantalla de televisión, etc. Una de estas media queries, `@media print`, fue creada específicamente para imprimir una página web. Por ejemplo, puedes eliminar menús, iconos, cambiar la forma en que se muestran los enlaces, etc. Como polyfill, Paged.js utilizará las reglas CSS incluidas en esta media query para definir los estilos de tu libro:


```css 
@media print {
  /* All your print styles go here */
}
```

Si no usas Paged.js, los estilos declarados en esta media query solo se aplicarán cuando la página web se imprima (o se guarde como PDF) desde el diálogo de impresión del navegador. Por ejemplo, el tamaño de la fuente puede variar entre la pantalla y la impresora, o las imágenes pueden eliminarse (con `display: none`) para ahorrar tinta. Si usas Paged.js, podrás ver en tu navegador una vista previa de cómo se verán tus estilos al imprimir.
## Regla @page

La regla @page te permite especificar distintos aspectos del modelo de página, como dimensiones, orientación, fondo, márgenes, recortes, marcas de registro, etc. Todas las propiedades CSS que afectan al diseño de la página deben declararse dentro de ella.

### Propiedad de tamaño de página

La propiedad `size` especifica el tamaño de la página (excluyendo los sangrados). Este tamaño fijo puede declararse usando unidades de longitud como centímetros (`cm`), milímetros (`mm`) o pulgadas (`in`). El primer valor es el ancho del documento y el segundo es la altura. Por defecto, Paged.js utiliza el tamaño letter (8.5in × 11in).
También es posible especificar el tamaño de página usando palabras clave que pueden combinarse con la orientación (`portrait` o `landscape`). Por defecto, la página siempre se imprime en orientación vertical (portrait).


```css 

/* Define a custom page size */
@page {
    size: 140mm 200mm;
}

/* Use A5 paper */
@page {
  size: A5;
}

/* Use A4 paper in landscape orientation */
@page {
  size: A4 landscape;
}
```


| Page size keyword     | Size          |
| :--                   |           --: |
| A0                    | 841 × 1189 mm |
| A1                    | 594 × 841 mm  |
| A2                    | 420 × 594 mm  |
| A3                    | 297 × 420 mm  |
| A4                    | 210 × 297 mm  |
| A5                    | 148 × 210 mm  |
| A6                    | 105 × 148 mm  |
| A7                    | 74 × 105 mm   |
| A10                   | 26 × 37 mm    |
| B4                    | 250 × 353 mm  |
| B5                    | 176 × 250 mm  |
| letter                | 8.5 × 11 in   |
| legal                 | 8.5 × 14 in   |
| ledger                | 11 × 17 in    |


### Variables CSS

No se pueden usar variables CSS para definir el tamaño de la página, ya que los navegadores no lo soportan.

<!-- Unfortunately, the `@page { size }` property is the only thing we can't polyfill that we need to print (to generate the PDF). -->

Sin embargo, Paged.js crea un conjunto de propiedades personalizadas a partir de tu declaración y las utiliza para el diseño. Por lo tanto, puedes reutilizarlas en tu documento si las necesitas para tus cálculos:

- `var(--pagedjs-pagebox-width)` para el ancho de la página
- `var(--pagedjs-pagebox-height)` para la altura de la página



```md
TODO: Esto debe actualizarse, ¡ya que no es cierto!
Advertencia: el navegador solo puede entender un único tamaño de página para tu documento. Si necesitas crear un documento con distintos tamaños de página, tendrás que crear dos archivos HTML separados y generar dos PDFs.
```




## Propiedad de tamaño de márgenes

Los márgenes de tus páginas deben declararse en la regla `@page` usando la misma sintaxis habitual de CSS. Puedes usar unidades como centímetros (`cm`), milímetros (`mm`), pulgadas (`in`) o píxeles (`px`).

```css 
@page {
  margin: 20mm 30mm;
}
```


Por defecto, los márgenes están configurados en 1 pulgada.

Otros ejemplos con diferentes sintaxis:

```css 
/* All margins are 30mm */
@page {
  margin: 30mm;
}

/* Top and bottom margins are 3in,
left and right margins are 2in */
@page {
  margin: 3in 4in;
}

/* All margins are different */
@page {
  margin-top: 20mm;
  margin-bottom: 25mm;
  margin-left: 10mm;
  margin-right: 35mm;
}
```


## Facing pages or recto/verso

Desde Gutenberg, los libros se diseñan pensando en páginas enfrentadas: las páginas izquierda y derecha suelen ser simétricas entre sí, utilizando el pliegue como eje.

Para lograrlo, puedes usar los pseudo-selectores `:left`y `:right` en las reglas `@page` y aplicar estilos distintos a cada página.


Veamos un ejemplo con márgenes diferentes: los márgenes exteriores son más grandes que los interiores.

```css 
@page:left {
  margin-left: 25mm;
  margin-right: 10mm;
}

@page:right {
  margin-left: 10mm;
  margin-right: 25mm;
}
```


Si tu documento es un libro recto/verso (es decir, no necesita páginas enfrentadas), puedes usar los selectores de página `:recto` y `:verso` de la misma manera.

## Saltos de página

Paged.js crea automáticamente un salto de página cuando el contenido no cabe en una página. Sin embargo, puede que necesites controlar esta fragmentación. Por ejemplo, en un libro, suele ser necesario que todos los capítulos comiencen en una página derecha. Existen varias propiedades que te ayudan a hacerlo.

<!-- In this part, we only talk about breaks from the point of view of global layout. Controlling the breaks inside paragraphs (widows or orphans) or avoiding fragmenting specific elements will be discussed in another part. -->

La propiedad `break-before` indica que un elemento debe comenzar en una nueva página, que puede ser:

- con `break-before: page`, el elemento puede comenzar en cualquier página nueva;

* con `break-before: right` o `break-before: left`, el elemento comienza en la siguiente página derecha o izquierda (se creará automáticamente una página en blanco si es necesario);

* con `break-before: recto` o `break-before: verso`, como alternativa a `break-before: right` o `break-before: left`.

Imaginemos que todos los capítulos de tu libro están dentro de elementos `<section>` con la clase `chapter` y quieres que cada capítulo comience siempre en una página derecha. Lo escribirías así:

```css 
.chapter {
  break-before: right;
}
```


También puedes usar saltos de página en elementos en línea. Por ejemplo, el siguiente código fuerza a que los títulos de nivel 2 comiencen siempre en una nueva página:

```css 
h2 {
  break-before: page;
}
```


Si lo prefieres, puedes usar `break-after` de la misma forma:

- `break-after: page` empuja el contenido posterior al elemento a la siguiente página;

* `break-after: right` o `break-after: left` empuja el contenido posterior a la siguiente página derecha o izquierda;

* `break-after: recto` o `break-after: verso` empuja el contenido posterior a la siguiente página recto o verso.



## Selectores de pseudoclase para páginas

El W3C ha definido selectores de pseudoclase para páginas específicas. Ya hemos visto los selectores `:left` y `:right`, pero hay otros muy útiles:

- `:first`, selecciona la primera página del documento;
- `:nth()`, permite especificar la página que deseas seleccionar indicando su número (por ejemplo, `@page:nth(3)` selecciona la tercera página);
- `:blank`, selecciona todas las páginas en blanco del documento (las páginas en blanco son el resultado de un salto forzado a página derecha o izquierda).

Una página seleccionada por una pseudoclase también puede coincidir con otras pseudoclases. Las reglas que se aplican se determinan siguiendo los principios de la cascada CSS.


## Sangrados

Para asegurarnos de que no quede ningún borde blanco visible al imprimir, utilizamos la propiedad `bleed`. Esta especifica el tamaño del área de sangrado fuera de la caja de página y no afecta al espacio disponible para el contenido.

```css 
@page  {
  bleed: 6mm;
}
```


## Marcas de corte y de registro

Puedes añadir marcas de corte fuera de la caja de página para facilitar el recorte. En impresión profesional, también es posible añadir marcas de registro, que se utilizan para alinear las hojas durante el proceso de impresión.

Ambos tipos de marcas deben añadirse en la misma propiedad `marks`, pudiendo usar una o ambas.

```css 
/* To set crop and cross marks */
@page {
  marks: crop cross;
}

/* To set only crop marks */
@page {
  marks: crop;
}
```


## Code Review for a chapter

```css 
@media print{
    @page {
        size: 140mm 200mm;
        margin: 10mm 15mm;
        bleed: 6mm;
        marks: crop cross;
    }
    @page:left {
        margin-left: 35mm;
        margin-right: 15mm;
    }
    @page:right {
        margin-left: 15mm;
        margin-right: 35mm;
    }
    .chapter{
        break-before: right;
    }
}
```

