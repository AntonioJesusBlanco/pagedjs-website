---
title: "La visión general"
date: 2019-09-03T18:23:22+02:00
draft: false
part: 1
symbolContent: "𖠅"
symbolText: "El rey Njoya creó la escritura bamum a los 25 años, en 1896, para la lengua bamum, que hoy se habla en el actual Camerún. [Leer más](https://decodeunicode.org/en/scripts/bamum#bamum)"
intro: "¿Has oído hablar de Paged.js pero no sabes por dónde empezar?"
cover: "/images/chuncker-1.png"
class: documentation
---

## ¿Qué es Paged.js?

Paged.js es una biblioteca libre y de código abierto que pagina cualquier contenido HTML para producir hermosos PDF listos para impresión.  
La biblioteca fragmenta el contenido, lee tus declaraciones CSS para impresión y presenta una vista previa paginada en el navegador que puedes guardar como PDF.

Al paginar el contenido en el navegador, Paged.js muestra una vista previa del resultado final en PDF directamente en los navegadores web. Esto permite a diseñadores y diseñadoras usar las herramientas de desarrollo del navegador (por ejemplo, la consola de inspección integrada en la mayoría de los navegadores) para realizar cambios al instante y controlar el renderizado de la composición tipográfica.

También es posible usar Paged.js dentro de otras herramientas y ampliar el renderizado mediante la creación de plugins.

Como Paged.js sigue los estándares del W3C, puede integrarse fácilmente en flujos de trabajo automatizados gracias a la versión de línea de comandos (que utiliza un navegador sin interfaz gráfica) capaz de generar un PDF a partir de comandos automatizados mediante scripts.

## Especificaciones del W3C

<p>Paged.js se basa en los estándares CSS escritos por el World Wide Web Consortium (W3C). Paged.js es un <span class="dt">polyfill<span class="dd">Un <a href="https://en.wikipedia.org/wiki/Polyfill_(programming))">polyfill</a> es un fragmento de código que implementa una funcionalidad en navegadores que no la soportan de forma nativa</span></span> para algunas propiedades CSS destinadas a la impresión de HTML desde el navegador. Puede analizar hojas de estilo CSS y traducir las declaraciones en HTML y CSS a un formato que el navegador pueda entender. Las declaraciones de impresión (actualizándolas con estilos compatibles o sustituyéndolas por implementaciones en JavaScript) permiten presentar un renderizado paginado del documento HTML utilizando la fragmentación proporcionada por las columnas CSS.</p>

Los módulos CSS del W3C que Paged.js pretende implementar son los siguientes:

- [Módulo CSS de Medios Paginados Nivel 3](https://www.w3.org/TR/css3-page/)
- [Módulo CSS de Contenido Generado para Medios Paginados](https://www.w3.org/TR/css-gcpm-3/)
- [Módulo CSS de Fragmentación Nivel 3](https://www.w3.org/TR/css-break-3/)

## Una comunidad

El código de Paged.js es de código abierto, con licencia MIT, y su desarrollo está impulsado por la comunidad. ¡Todo el mundo está invitado a participar! Puedes encontrar el código fuente de Paged.js en el repositorio de nuestro GitLab autoalojado:  
https://gitlab.coko.foundation/pagedjs/pagedjs

Contamos con diseñadores y desarrolladores que quieran debatir nuevas funcionalidades, ideas y correcciones de errores. Si te gustaría participar en la conversación, puedes añadir incidencias en el repositorio. Pero la forma más sencilla es entrar en [nuestro chat autoalojado](https://mattermost.coko.foundation/) y unirte a la conversación.
