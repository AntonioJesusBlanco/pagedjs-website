---
title: "Cómo funciona Paged.js"
date: 2019-09-03T18:23:22+02:00
draft: false
part: 4
intro: "Si alguna vez has intentado maquetar un sitio web para impresión o publicar un libro en HTML, habrás experimentado las limitaciones de los estilos pensados para mostrar texto desplazable en pantallas. Paged.js ayuda a hacer posible la producción de material paginado directamente desde el navegador."
---

<figure> <img src="/images/flux-page.png"  /></figure>

Paged.js está compuesto por tres módulos, cada uno con una tarea muy precisa:

- el **chunker** fragmenta el contenido en páginas discretas;
- el **polisher** transforma las declaraciones CSS en otras que el navegador puede entender;
- y el **previewer** crea la vista previa de tu libro en el navegador.

Para empezar a usar Paged.js, todo lo que tienes que hacer es escribir las declaraciones CSS estandarizadas y llamar al script. La biblioteca interpretará estas declaraciones ya sea convirtiéndolas en estilos CSS compatibles de forma nativa con el navegador o sustituyéndolas por implementaciones en JavaScript.

## El Chunker: fragmentar el contenido

El *chunker* toma todo tu contenido renderizado, con todas las reglas de diseño aplicadas, coloca tanto contenido como sea posible en una primera caja y busca el contenido que se desborda.

<!-- ![The chunker puts all your rendered content in a box and checks the overflow](/images/chuncker-1.png) -->

Después, el script crea una nueva caja, coloca en ella la mayor cantidad posible del contenido desbordado, busca el siguiente desbordamiento, y así sucesivamente, hasta que no queda contenido por colocar en las cajas. Paged.js repite este proceso una y otra vez hasta que el libro está completo.

<!-- ![The chunker creates a new box and puts the overflow content in it]() -->

<figure> <img src="/images/chuncker-2.png"  /></figure>

Para ello, utilizamos las potentes propiedades de las columnas CSS: cada página es una columna. Esto nos da acceso directo a algunas propiedades ya implementadas en los navegadores, como los saltos de columna, element() o las viudas y huérfanas.

PS. Esto no te impide usar la propiedad CSS column dentro de tu contenido.

Como el contenido de cada página queda entonces fijado, tendrás que recargar la página para repetir el proceso cada vez que cambie algo en tu HTML o CSS.

Veremos que es posible controlar los saltos de página y cambiar el tamaño del área de contenido modificando los márgenes de una página a otra mediante un diseño maestro de página (*page master layout*).

## El Polisher: aplicar polyfills a las declaraciones de impresión

El *Polisher* es la parte de Paged.js que traduce las reglas CSS de cada una de tus páginas. Construye nuevas cajas para crear los diseños de página y coloca tu contenido en ellas. Utilizamos la biblioteca CSS tree para analizar el CSS desde texto y sustituir las reglas `@page` por clases. El *polisher* también reemplaza llamadas como encabezados corridos, contadores de página o funciones de contenido generado por CSS por variables procedentes del DOM.

<figure> <img src="/images/div-pages.png"  /></figure>

Tomemos el siguiente CSS como ejemplo:

```css
@page {
  size: 148mm 210mm;
  margin-top: 10mm;
  margin-right: 20mm;
  margin-bottom: 25mm;
  margin-left: 15mm;

  @bottom-left {
    content: counter(page);
  }

  @bottom-center {
    content: string(title);
    text-transform: uppercase;
  }

}

h1#title {
  <!-- "Moby Dick" -->
  string-set: title content(text);
}
```

Paged.js transforma esto en un CSS que el navegador puede entender:

```css
.pagedjs_page {
  --pagedjs-string-title: "Moby Dick";
  margin-top: 10mm;
  margin-right: 20mm;
  margin-bottom: 25mm;
  margin-left: 15mm;
}

.pagedjs_page .pagedjs_margin-bottom-left::after {
  content: string(title);
}

.pagedjs_page .pagedjs_margin-bottom-center::after {
  content: var(--pagedjs-string-title);
  text-transform: uppercase;
}
```

Esto se aplicará al DOM transformado (esta es una versión simplificada de lo que genera Paged.js):

```html
<div id="page-1" class="pagedjs_page">
	<div class="pagedjs_pagebox">
    	<div class="pagedjs_margin pagedjs_margin-bottom-left hasContent">
				<div class="pagedjs_margin-content">
          <!-- ::after -->
        </div>
			</div>
      <div class="pagedjs_margin pagedjs_margin-bottom-center hasContent">
				<div class="pagedjs_margin-content">
          <!-- ::after -->
        </div>
			</div>
    </div>
		<div class="pagedjs_area">
			<!-- content of the page -->
		</div>
	</div>
</div>
```

A lo largo de esta documentación, especificaremos las propiedades CSS que implementamos y aquellas que utilizamos como alternativa, para que puedan ser interpretadas por el navegador.

## El Previewer: renderizar el documento paginado

El módulo preview de Paged.js carga los distintos módulos y utiliza el polisher y el chunker para maquetar el contenido. Construye la vista previa de tu documento en el navegador, de modo que puedas ver exactamente cómo se verá el resultado y ajustar el contenido en consecuencia.

Entre bambalinas, Paged.js modifica la estructura del DOM añadiendo algunos elementos HTML para construir y renderizar el diseño. Estas modificaciones se realizan durante el renderizado y no alteran tu documento HTML original.

También añade referencias a cada nodo (por ejemplo, clases para diferenciar páginas derechas o izquierdas). Esto nos da un control total sobre el diseño de página sin necesidad de trucos o soluciones improvisadas.

Esta documentación especificará, para cada propiedad CSS, las modificaciones del DOM necesarias para construir y renderizar tu libro. También puedes acceder directamente a estos elementos desde JavaScript a través de sus clases si quieres profundizar más en Paged.js y añadir tus propias funciones.