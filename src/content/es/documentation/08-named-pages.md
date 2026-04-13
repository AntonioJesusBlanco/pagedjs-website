---
title: "Paginas nombradas"
date: 2020-01-28T15:54:01+01:00
draft: false
intro: "Cómo configurar el page master basándose en el contenido de tu HTML: la named page"
part: 8
---


Pueden haber páginas en tu libro que necesiten un diseño más específico: diferente fondo, diferentes márgenes, e incluso estilos diferentes de la regla principal.
Puedes usar lo que se llama "named pages" para definir esto. Basándote en tu HTML, puedes vincular un diseño específico a cualquier contenido.

![Cómo funcionan las named pages](/images/named-pages.png)

Imagina que quieres un diseño específico para las páginas de tu frontmatter. 

La propiedad `page` se usa para especificar un tipo particular de página. Con esta propiedad, defines que todas las secciones con la clase `frontmatter` tendrán una plantilla de página nombrada `frontmatterLayout`. Ten en cuenta que los nombres de tipo de página distinguen entre mayúsculas y minúsculas.

También creas una regla @page específica con el mismo nombre de tipo de página donde creas nuevas propiedades de la página. 

Así es como lo harías con el código:
```css
.frontmatter {
	page: frontmatterLayout;
}

@page frontmatterLayout {
	/* reglas específicas para el frontmatter*/
}
```

Con esta regla, cada elemento con la clase `frontmatter` tendrá un salto de página por defecto. Un **page group** se crea en cada instancia de elementos con la clase `frontmatter`.

Es posible vincular diferentes secciones a la misma regla @page.

```css
#half-title,
#copyright,
#table-of-content,
#introduction {
	page: frontmatterLayout;
}
```


## Mezclar selectores de página y named pages

Puedes mezclar selectores de página y named pages.

Por ejemplo, la primera página de cada capítulo a menudo requiere un tratamiento especial. Puedes definir un diseño para todos tus capítulos y seleccionar la primera página de cada capítulo (de cada page group).

En el código de abajo, se selecciona cada primera página de elementos con la clase `chapter`:

```css
.chapter {
	page: chapter;
}

@page chapter:first {
    /* reglas específicas para la primera página de cada capítulo */
}
```

También puedes seleccionar las páginas `:left` y `:right` de named pages y page groups.

Los selectores `:nth()` y `:blank` no funcionan con named pages por ahora. Pero puedes usar este truco para seleccionar las páginas en blanco de named pages:

```css
.pagedjs_chapter_page + .pagedjs_blank_page {
/* reglas específicas para páginas en blanco de named page llamada "chapter" */
}
```



## Prioridad de las reglas @page

En paged.js, las reglas de página no se aplican exactamente en cascada. Las reglas se definen por el orden de prioridad a continuación (ordenadas de menor a mayor prioridad):

- `@page { }`
- `@page :left { }` o `@page :right { }`
- `@page <namedPage> { }`
- `@page <namedPage> :left { }` o `@page <namedPage> :right { }`
- `@page :blank { }`
- `@page :first { }`



Si una propiedad CSS está definida en una página de menor prioridad que otra pero no en la página de prioridad, entonces la página de prioridad hereda esta propiedad y su valor. De lo contrario, la propiedad tomará el nuevo valor definido en la página de prioridad.