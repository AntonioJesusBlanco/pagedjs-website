---
title: "Primeros pasos con Paged.js"
date: 2019-09-03T18:23:22+02:00
draft: false
part: 2
intro: "Los conceptos básicos para ejecutar Paged.js"
cover: "/images/chuncker-1.png"
class: documentation
symbolContent: "𝄢"
symbolText: "El bloque de Símbolos Musicales cubre los caracteres utilizados por la notación musical occidental básica y sus antecedentes (notación mensural y canto llano —o gregoriano—). [Leer más](https://decodeunicode.org/en/scripts/musical_symbols#musical_symbols)"
---

## Iniciar Paged.js

Paged.js se presenta en tres modalidades: un polyfill que se ejecuta automáticamente al iniciar el navegador, un módulo npm que puedes ejecutar, y una versión de línea de comandos que utiliza un navegador sin interfaz gráfica. Todas pueden adaptarse fácilmente a tus necesidades, así que siéntete libre de usar la que te resulte más conveniente.

### Usar Paged.js como polyfill en navegadores web

Para poder ejecutar Paged.js en tu documento, necesitarás lo siguiente:

- Los archivos HTML y CSS que quieras transformar en un libro;
- El script de Paged.js (ya sea de forma local o usando nuestro enlace CDN);
- Un servidor web que permita al polyfill acceder a tu archivo CSS;
- Un navegador web para ver la magia en pantalla.

#### Obtener el script

Para descargar el polyfill, ve a https://unpkg.com/pagedjs/dist/. Desde ahí, puedes elegir la versión más reciente o versiones anteriores de `paged.polyfill.js`.

Copia el script en un archivo y llámalo desde el `<head>` de tu archivo HTML:

```html
<script src="js/paged.polyfill.js"></script>
```


Si lo prefieres, puedes usar la versión alojada del script en [unpkg.com/pagedjs] copiando la siguiente línea de código en el `head` de tu documento. Si necesitas una versión anterior de Paged.js, puedes consultar los lanzamientos en [unpkg.com/browse/pagedjs](https://unpkg.com/browse/pagedjs/). Fíjate en el botón de la esquina superior derecha para acceder a versiones más antiguas.

```html
<script src="https://unpkg.com/pagedjs/dist/paged.polyfill.js"></script>
```
Tan pronto como el navegador haya cargado todo lo necesario para mostrar tu HTML en pantalla (incluidas imágenes, archivos de fuentes, etc.), el script comenzará a paginar el contenido y las páginas aparecerán en la pantalla.

#### Previsualiza tu trabajo (interfaz)

Paged.js manipula el DOM para que el navegador pueda entender las reglas CSS que has escrito. En pantalla, las páginas se mostrarán de arriba abajo en el lado izquierdo de la página. Para facilitar la comprensión de lo que ocurre, hemos creado un pequeño archivo CSS llamado interface que define el diseño para mostrar tu libro en pantalla. Como utilizamos `@media screen`, los bordes de página y las sombras no aparecerán en papel cuando se imprima.Para descargar el archivo de la interfaz, ve al [repositorio en GitLab](https://gitlab.coko.foundation/pagedjs/interface-polyfill), descarga el archivo `interface.css` y enlázalo a tu documento en el `<head>`. Te recomendamos revisar el archivo, ya que ofrece opciones para mostrar páginas enfrentadas, recto/verso, línea base, etc.

```html
<link href="path/to/file/interface.css" rel="stylesheet" type="text/css" />
```

#### Genera tu primer PDF desde el navegador

Una vez que Paged.js haya terminado su trabajo, puedes generar el PDF usando la función Guardar como PDF de tu navegador.

1. Haz clic en el botón "Imprimir" de tu navegador (normalmente en Archivo > Imprimir o con el atajo CTRL/CMD + P).

2. Cambia el Destino a "Guardar como PDF".

3. En la configuración avanzada, como Paged.js no utiliza ninguna de esas opciones, asegúrate de que los siguientes ajustes sean correctos:

   - Los Márgenes estén configurados en "ninguno",
   - "Encabezados y pies de página" esté desactivado o en ninguno,
   - "Gráficos de fondo" esté activado.

Después, puedes abrir tu PDF con tu lector de PDF favorito.

### Versión de línea de comandos

La versión de línea de comandos de Paged.js utiliza un navegador sin interfaz gráfica para generar un PDF. Puede ejecutarse en el servidor para lanzar un Chromium sin interfaz en flujos de trabajo totalmente automatizados. Con esta versión, no necesitas incluir el script de Paged.js en tu documento: se hará automáticamente.

Primero, descarga e instala `pagedjs-cli` usando tu terminal (necesitas tener `git`, `node` y `npm` instalados):

```bash
$ npm install -g pagedjs-cli pagedjs
```


Luego, en una nueva ventana de terminal, ve a la carpeta donde se encuentra el código de tu documento (usando el comando `cd`) y genera tu PDF con el siguiente comando:
```bash
$ pagedjs-cli index.html -o result.pdf
```

Algunas opciones para generar el PDF:

```bash
-h, --help                   muestra la información de uso
-V, --version                muestra el número de versión
-i, --inputs [inputs]       Entradas
-o, --output [output]       Salida
-d, --debug                 Muestra la ventana de Electron para depuración
-l, --landscape             Impresión en horizontal
-s, --page-size [size]      Imprimir con tamaño de página [size]
-w, --width [size]          Imprimir con ancho de página [width]
-h --height [size]          Imprimir con alto de página [height]
-m, --page-margin [margin]  Imprimir con margen [margin]
-n, --hyphenate [lang]      Separar palabras por idioma [language], por defecto "en-us"
-hi, --hypher_ignore [str]  Ignorar selectores de elementos, como ".class_to_ignore, h1"
-ho, --hypher_only [str]    Separar solo los elementos indicados, como ".hyphenate, aside"
-e, --encoding [type]       Establecer la codificación del HTML de entrada, por defecto "utf-8"
-t, --timeout [ms]          Establecer un tiempo máximo de espera [ms]
```
