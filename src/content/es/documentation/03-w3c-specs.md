---
title: "Especificaciones del W3C para la impresión"
date: 2019-10-03T10:23:22+03:00
draft: false
part: 3
intro: "El W3C piensa en todo."
---

## Módulos CSS del W3C


Para imprimir páginas se necesitan reglas muy distintas de las que se utilizan para mostrar contenido en el navegador. Por ejemplo, querrás declarar páginas de tamaño fijo en lugar de disponer un flujo continuo de texto. Los libros también requieren muchos elementos específicos para el diseño impreso: márgenes, encabezados corridos, números de página, una tabla de contenidos, etc.

Afortunadamente, el trabajo sobre CSS en el W3C ha dado como resultado módulos especiales del estándar CSS para gestionar el diseño de un documento HTML durante la impresión. Estos módulos pueden utilizarse en una hoja de estilos de impresión mediante la media query `@media print {}` y solo se aplicarán cuando la página web se imprima desde el diálogo de impresión del navegador para crear un PDF.

- [Módulo CSS de Medios Paginados Nivel 3](https://www.w3.org/TR/css3-page/) "describe el modelo de página que divide un flujo en páginas. (…) Añade funcionalidad para la paginación, los márgenes de página, el tamaño y la orientación de la página, encabezados y pies de página, viudas y huérfanas, y la orientación de las imágenes".
- [Módulo CSS de Contenido Generado para Medios Paginados](https://www.w3.org/TR/css-gcpm-3/) define muchos requisitos especiales para la visualización del contenido de documentos impresos: encabezados y pies de página corridos, notas al pie, texto generado para referencias cruzadas o tablas de contenido, marcadores PDF, etc.
- [Módulo CSS de Fragmentación Nivel 3](https://www.w3.org/TR/css-break-3/) define cómo y dónde pueden fragmentarse las cajas CSS, incluso a través de saltos de página. (Este módulo no es específico de la impresión).
- [CSS page floats](https://www.w3.org/TR/css-page-floats-3/) define cómo un elemento se extrae del flujo normal y se coloca en una posición diferente que depende de la página. ([ver el artículo «Page Media approaches: page floats»](/journal/PagedMediaapproaches:pagefloats/))

Intentamos respetar las especificaciones tanto como sea posible, pero a veces pueden resultar poco claras o dejar cierto grado de indeterminación. Por ello, la implementación de Paged.js se basa en nuestra propia interpretación y, cuando estas reglas sean implementadas por los navegadores, pueden diferir de lo que nosotros habríamos construido.

En algún momento necesitaremos cosas que aún no han sido contempladas por el W3C. En ese caso, podremos desarrollar nuestras propias reglas, basadas en las necesidades de la comunidad de usuarios. Intentaremos adoptar las soluciones técnicamente más adecuadas para satisfacer las expectativas de tipógrafos y diseñadores. En ese caso, redactaremos las especificaciones y las compartiremos con otros miembros del W3C.

## Compatibilidad de las especificaciones del W3C en los navegadores

Los módulos estándar de CSS mencionados anteriormente fueron redactados por el World Wide Web Consortium (W3C). El W3C publica documentos que definen tecnologías web (incluido CSS) y que se consideran estándares web. Los módulos del W3C se publican públicamente a lo largo de su proceso de desarrollo hasta que finalmente se publican como una [Recomendación del W3C](https://www.w3.org/2018/Process-20180201/#rec-publication). Los módulos que necesitamos para los medios paginados se encuentran en distintas fases de este proceso, pero la mayoría aún está en la etapa de [Borrador de Trabajo](https://www.w3.org/2018/Process-20180201/#revised-wd).

Los desarrolladores de navegadores pueden comenzar a implementar estas recomendaciones en cualquier momento, sabiendo que pueden cambiar más adelante, pero no están obligados a implementar todas las especificaciones CSS hasta que se conviertan en Recomendaciones del W3C.

Afortunadamente, los desarrolladores de navegadores ya han mostrado cierto interés en implementar partes de los estándares del Borrador de Trabajo de Medios Paginados y las [reglas `@page` tienen soporte parcial](https://caniuse.com/#search=%40page) en Chrome, Firefox e IE. Sin embargo, sigue siendo difícil utilizar estos navegadores de forma eficaz para la salida de contenido paginado.

Así que, a día de hoy, cuando se trata de producir contenido paginado desde el navegador, esta es la situación: las reglas para imprimir páginas web desde un navegador están escritas e incluso estandarizadas, pero todavía no podemos utilizarlas de forma efectiva.

### ¿Qué navegador utilizar?

Queremos que Paged.js funcione perfectamente con todos los navegadores existentes, pero en el momento de escribir estas líneas, algunos son más adecuados que otros. Todo depende de las funcionalidades que quieras para tus documentos (CSS flexbox, separación de palabras, etc.). Además, puede que necesites un navegador que tenga en cuenta la propiedad size para generar PDFs. A continuación, algunas explicaciones para ayudarte a elegir.

#### Compatibilidad con @page { size }

Paged.js actúa como una especie de polyfill, pero hay algo que no podemos gestionar y que es necesario para imprimir correctamente: el soporte por parte del navegador de la propiedad `@page { size }`. Esta propiedad permite crear un PDF con el tamaño correcto cuando se genera. Esta propiedad solo es compatible con algunos navegadores:

- Chromium
- Google Chrome
- Brave
- Opera

Sabemos que muchos de vosotros estáis muy vinculados a Mozilla Firefox (y nosotros también). Aun así, es posible usar Paged.js con él, pero tendrás que cambiar manualmente el tamaño del PDF cuando lo generes (usando tamaños personalizados). Ten cuidado al calcular los sangrados y las marcas de corte si necesitas añadirlos.

#### Compatibilidad con CSS Grid

También debes utilizar una versión reciente de estos navegadores, ya que usamos algunas propiedades del módulo CSS Grid para la construcción de las páginas. CSS Grid está soportado en la mayoría de los navegadores desde mediados de 2017. Puedes comprobar aquí si tu navegador es compatible con CSS Grid: https://caniuse.com/#feat=css-grid

#### Diferencias de renderizado entre navegadores

El resultado no siempre será el mismo de un navegador a otro, ya que no utilizan el mismo motor de renderizado. Por ejemplo, la propiedad line-height no se gestiona de la misma manera en Firefox y en Chrome. El resultado tampoco será el mismo dependiendo del sistema operativo que utilices. Por ejemplo, la separación de palabras (hyphenation) en Chrome solo está disponible en Apple macOS.

Recomendamos mantener el mismo navegador y sistema operativo durante el diseño y la generación del PDF para evitar sorpresas desagradables.