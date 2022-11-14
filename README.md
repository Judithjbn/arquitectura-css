### 👷  ¿Qué queremos solucionar con arquitectura CSS

Cuando planteamos una aplicación en cualquier lenguaje tenemos que tomar una serie de decisiones sobre su arquitectura que afectaran a la escalabilidad y mantenibilidad de la misma, y el CSS no es una excepción.

Acordar con nuestro equipo un estándar de nomenclaturas de clases que comuniquen claramente la intención de las clases va a jugar un papel muy importante en la mantenibilidad de nuestro código. Incluso cuando usamos soluciones como CSS-in-JS o Tailwind, vamos a tener que crear componentes reutilizables, y estos necesitaran tener un nombre y recibir variables que permitan modificar ciertas propiedades, que no serán muy distintas del nombre que pondríamos a la clase base y sus modificadores si usamos CSS vainilla.

Además, un buen sistema de nomenclatura también nos ayudará a tener selectores menos específicos y no tener que recurrir a usar _!important_ para solucionar problemas relacionados con especificidad.

Modularizar nuestros estilos con la ayuda de un preprocesador como **SASS** también nos ayudará a tener archivos pequeños fáciles de localizar, entender y mantener.

Por último, tenemos herramientas disponibles online para detectar dead code en CSS como [Unused CSS](https://unused-css.com/), y también herramientas que podemos incorporar en nuestro proyecto como [PurifyCSS](https://github.com/purifycss/purifycss).

### 🎯  Visualiza la calidad de tu CSS: Especificidad de los selectores

Sabemos que, en un selector, un ID es lo más específico. En segundo lugar, las clases, pseudo-clases (:hover, :active) y los selectores de atributo, como [type=text]. Por último, los elementos como div, article, a, etc. Esto es fácil de recordar, pero cuando nos encontramos selectores complejos es difícil determinar a simple vista su especificidad a simple vista. En estos casos, podemos recurrir al método de calcular la especificidad contando cuantos selectores de cada tipo hay en nuestro selector compuesto. Por ejemplo, en el caso de #header .nav a:hover, tenemos 1 id, 1 clase, 1 pseudo-clase y 1 elemento. Esto nos daría una especificidad de 1-2-1.

Esto nos va ser útil cuando nos encontremos con CSS a refactorizar, pero si estamos desarrollando nuestros estilos, intentaremos siempre usar selectores simples: mayoritariamente clases y anidar lo menos posible, para facilitar la lectura y comprensión de nuestros estilos. De la misma forma, lo mejor es poner los selectores menos específicos al inicio de nuestra hoja de estilos, y los más específicos al final.

Para analizar la especificidad de nuestros selectores, podemos usar herramientas como el [Specificity Graph](https://jonassebastianohlsson.com/specificity-graph/). Lo ideal será un resultado lo más plano posible, y si hay subidas que se encuentren al final de nuestra hoja de estilos.

### 🍅  Single Responsibility Principle en CSS

En CSS, como en cualquier otro lenguaje, nos interesará seguir patrones como el de responsabilidad única. Lo complicado será determinar cuál es la responsabilidad de cada clase.

Si estamos tratando con clases muy genéricas, como es el caso de ui-list, esa responsabilidad va a ser muy concreta y tenemos que comunicar bien cuál es esa responsabilidad con el nombre de la clase. En este caso suele ser mala idea mezclar atributos que hagan referencia a layout con otros que hagan referencia a theming, como colores de background, texto o demás decoraciones.

En clases para elementos más concretos, como podria ser header, nos podemos permitir añadir atributos de posición, tamaño y theming en una misma clase ya que, como el nombre de la clase indica, la responsabilidad de esa clase es estilar el header principal de nuestra aplicación.

### 👨‍👧‍👦 Herencia vs Composición en CSS

A la hora de compartir partes de código CSS nos encontramos también con paralelismos con otros lenguajes cuando hablamos de herencia y composición.

Hay casos, como en el ejemplo de un botón con distintos themes, en los que podría tener sentido usar la herramienta @extend de Sass, que podríamos considerar herencia de clases.

```
%btn {
  display: inline-block;
  padding: 0.5rem;
}

.btn-primary {
  @extend %btn;
  background: #289f5f;
  color: #fff;
}

.btn-secondary {
  @extend %btn;
  background: #333;
  color: #fff;
}

```

Usando el operador %, conseguimos que la clase base no sea incluida en el CSS generado, ya que no necesitamos usarla en ningún caso. De esta forma, en el CSS final nos quedan los selectores con los atributos comunes agrupados:

```
.btn-secondary, .btn-primary {
  display: inline-block;
  padding: 0.5rem;
}

.btn-primary {
  background: #289f5f;
  color: #fff;
}

.btn-secondary {
  background: #333;
  color: #fff;
}

```

Si usamos el mismo método con clases que tienen atributos en común pero son independientes, como el caso del ejemplo de header, footer y section, estamos uniendo clases que en realidad no tienen nada que ver y a la larga podemos encontrarnos con problemas. Otra opción es usar mixins, que permiten pasar parámetros y así tener algo más de flexibilidad:

```
@mixin justify($vertical-align: center) {
  display: flex;
  justify-content: space-between;
  align-items: $vertical-align;
}

.hero {
  @include justify;
  background: #289f5f;
  color: #fff;
}

.footer {
  @include justify($vertical-align: flex-start);
  background: #333;
  color: #fff;
}

.section {
  @include justify;
  background: #fff;
  color: #333;
}

```

Los mixins pueden sernos útiles, pero hay que usarlos con cuidado, para tareas muy concretas y sin abusar de demasiados parámetros.

Realmente, la mejor opción para compartir atributos entre clases y la que debería ser nuestra opción por defecto, es componer con clases separadas:

```
.btn {
  display: inline-block;
  padding: 0.5rem;
}

.btn-primary {
  background: #289f5f;
  color: #fff;
}

.btn-secondary {
  background: #333;
  color: #fff;
}

```

Así, podemos usar dos clases en el mismo elemento html:

```
<a
  class="btn btn-primary"
  href="/buy">
  Comprar
</a>

```

En este caso, debemos comunicar muy bien con el nombre de las clases la relación que existe entre ambas, y que no tendría sentido usar btn-primary por separado. Esto lo veremos en detalle en el siguiente vídeo.

### 🧩 Mejora tu CSS con BEM: ventajas e inconventes

BEM (Block, Element, Modifier) es un sistema de nomenclatura de clases que resuelve los principales problemas de escoger nombres de clases: minimiza el riesgo de colisión, reduce la necesidad de anidar selectores, y expresa de manera clara la relación entre clases.

Lo vemos claro en este ejemplo:

```
<article class="card">
  <img class="card__img" src="/arquitectura-css.png" alt="" />
  <h3 class="card__title">Arquitectura CSS</h3>
  <strong class="card__meta">Núria y Rafa</strong>
  <p>Aprende a crear layouts…</p>
  <button class="button button--primary">Empezar curso</button>
</article>

```

```
.card {
  &__img { }
  &__title { }
  &__meta { }
}
.button {
  &--primary { }
}

```

Tanto si miramos el HTML como el CSS, vemos claramente que card y button son independientes, que card__img, card__title y card__meta son elementos de card (ya que llevan 2 underscore), y que button--primary es un modifier de btn (ya que lleva 2 guiones).

BEM suele recibir críticas porque las clases son muy largas y deja el html difícil de leer. Es cierto que tienden a ser más largas que si no usáramos este sistema, pero podemos minimizar esto evitando los errores más comunes.

Un error habitual es replicar la estructura que tenemos en HTML y anidar elementos dentro de otros elementos, terminando con clases como card__info__title:

```
<article class="card">
  <img class="card__img" src="/cursos/arquitectura-css.png" alt="" />
  <div class="card__info">
    <h3 class="card__info__title">Arquitectura CSS</h3>
    <strong class="card__info__meta">Núria y Rafa</strong>
  </div>
  <button class="button button--primary">Empezar curso</button>
</article>

```

Esto provoca que las clases sean muy largas, pero el mayor problema es que acopla demasiado el CSS a la estructura HTML. En BEM, todos los elements deben ser elementos directos del bloque:

```
.card {
  &__info { }
  &__title { }
  &__meta { }
  &__img { }
}

```

Otro error habitual es evitar anidar hasta el punto de crear modifiers por elementos cuando tendría más sentido usar un modifier de bloque:

```
<article class="card card--dark">
  <img class="card__img" src="/cursos/arquitectura-css.png" alt="" />
  <h3 class="card__title card__title--dark">Arquitectura CSS</h3>
  <strong class="card__meta card__meta--dark">Núria y Rafa</strong>
</article>

```

```
.card {
  &--dark { }
  &__img { }
  &__title {
    &--dark { }
  }
  &__meta {
    &--dark { }
  }
}

```

En este caso estamos repartiendo la responsabilidad de adaptar card a un theme oscuro entre el modifier card--dark, y los modifiers de elementos card__title--dark y card__meta--dark.

En este caso está plenamente justificado anidar clases y hacer un solo modifier de card que se encargue también de modificar los elementos:

```
<article class="card card--dark">
  <img class="card__img" src="/cursos/arquitectura-css.png" alt="" />
  <h3 class="card__title">Arquitectura CSS</h3>
  <strong class="card__meta">Núria y Rafa</strong>
</article>

```

```
.card {
  &__img { }
  &__title { }
  &__meta { }

  &--dark {
    .card__title {}
    .card__meta {}
  }
}

```

Así nos queda el HTML más limpio, y en el CSS quedan las responsabilidades claramente más agrupadas y es más fácil de entender.

### ⚡ Mejorar lo que no nos gusta de BEM con utility classes

Si aplicamos BEM de forma purista, cada vez que tuviéramos que modificar un bloque o elemento tendríamos que crear un modifier específico. Pero tendremos algunos casos que se van a repetir en muchas partes de nuestra aplicación, por ejemplo centrar un texto, poner negrita, usar un color rojo para comunicar un error… Tener que crear múltiples modifiers que hagan lo mismo es tedioso y difícil de mantener, ya que si queremos cambiar algo tendremos que hacerlo en múltiples puntos de nuestra app.

Para estos casos, tiene sentido combinar BEM con utility classes:

```
<article class="card">
  <img class="card__img" src="/arquitectura-css.png" alt="" />
  <h3 class="card__title is-centered">Arquitectura CSS</h3>
  <strong class="card__meta is-centered">Núria y Rafa</strong>
</article>

```

```
.card {
  &__img { }
  &__title { }
  &__meta { }
}

.is-centered {
  text-align: center;
}

```

Es importante comunicar la intención de la utility class. Usando un prefijo como is-, nos queda claro que esta clase es una utilidad y va a modificar el elemento al que se aplique. Es importante que este tipo de clases sean muy atómicas, en la mayoría de casos no deberían contener más de una propiedad. Como siempre que añadimos una utility class vamos a querer que se aplique esa propiedad, incluso tendría sentido añadir !important.

### ⚛️  Atomic Design: compartir lenguaje entre diseño y desarrollo

Atomic Design es un método para crear sistemas de diseño ideado por Brad Frost. Pese a estar orientado a diseño, podemos coger ideas que nos van a facilitar la arquitectura de nuestro CSS.

Atomic Design consiste en varios elementos:

- Átomos
_Son las piezas más pequeñas de nuestro sistema de diseño. Son partes reconocibles pero indivisibles y siempre necesitarán de más contexto para tener sentido en nuestra aplicación. Ejemplos claros son botones, labels, títulos (elementos individuales)…_

- Moléculas
_Cuando juntamos dos o más átomos creamos una molécula. Son elementos que ya tienen un sentido por si solos, aunque no son totalmente independientes: cards, una label con un input, cpt diseño individual…_

- Organismos
_Un organismo es una composición de varias moléculas. Aquí si que ya estamos tratando con elementos independientes, que si sacamos de una página y los ponemos en otra seguirán teniendo sentido: un header, el grid/listings de cursos con sus filtros…_

Atomic Design también habla de templates y páginas, pero esto ya no nos afecta en el desarrollo de nuestros estilos, ya que raramente necesitaremos definir clases a nivel de página y las definiremos juntando varios organismos en nuestro HTML.

Con Atomic Design conseguimos un lenguaje ubicuo entre el equipo de diseño y desarrollo front-end. Eso facilitará mucho la comunicación entre ambos, aunque, siendo un sistema orientado a diseño, se nos queda cojo cuando necesitamos definir donde poner estilos como reset o normalize, box-sizing, o añadir clases funcionales o de estructura.

La estructura de carpetas usando atomic design quedaría así:

```
styles
├── atoms
│       ├── _button.scss
│       ├── _image.scss
│       └── _pill.scss
├── molecules
│       ├── _card.scss
│       └── _form.scss
├── organisms
│       ├── _gallery.scss
│       └── _header.scss
└── index.scss

```
Más información: [Atomic Design](https://atomicdesign.bradfrost.com/table-of-contents/)

---

### 🔻 ITCSS: arquitectura para aplicaciones escalables

ITCSS es un sistema de arquitectura CSS ideado por Harry Roberts. IT son las iniciales de Inverted Triangle, que es lo que usamos para visualizar como vamos a organizar nuestros estilos: de lo más general a lo más concreto, con selectores de menos específicos a más.

### Organizaremos nuestro CSS en 7 capas:

- **Settings y Tools**
Los dos primeros niveles están orientados a preprocesadores como Sass: definir variables de colores, tipografía… así como mixins y funciones. Estos dos niveles no producen ningún output en nuestro CSS.

- **Generic**
_En esta capa es donde pondremos estilos genéricos como reset o normalize, y definición de box-sizing. Aquí ya empezamos a generar output en nuestro CSS._

- **Elements**
_Definimos los estilos de elementos HTML como h1, img, a… es donde empezamos a aplicar estilos propios que cambiarán el aspecto de nuestra aplicación.


- **Objects**
_En esta capa empezamos a crear clases, hasta ahora solo hemos trabajado con selectores de elemento (que tienen menos especificidad). Los objetos son clases muy reutilizables que añaden patrones de estructura pero sin añadir decoración: clases como .container o .ui-list._


- **Components**
_Los componentes son el grueso de nuestra app. Aquí tendremos todas las clases que añaden estilos más allá de estructura. Al ser clases más concretas, van a ser menos reutilizables: una clase main-header la vamos a aplicar menos veces que container.

- **Utilities**
_Clases de utilidad como is-error, is-centered, etc, que siempre van a sobreescribir los estilos anteriores cuando las apliquemos. Recordemos que es el caso donde podemos usar !important sin que esto haga nuestro CSS menos mantenibles._

Como vemos, con ITCSS tenemos una estructura muy clara de donde va cada tipo de estilo, aunque la carpeta components tenderá a crecer mucho. ITCSS pretende ser un sistema muy flexible y por eso deja a nuestra elección como ordenar los componentes.

La estructura de carpetas usando ITCSS quedaría así:

```
styles
├── settings
│       ├── _colors.scss
│       └── _typography.scss
├── tools
│       └── _mixins.scss
├── generic
│       ├── _normalize.scss
│       └── _box-sizing.scss
├── elements
│       ├── _headings.scss
│       ├── _images.scss
│       └── _links.scss
├── objects
│       ├── _container.scss
│       ├── _grid.scss
│       └── _ui-list.scss
├── components
│       ├── _button.scss 
│       ├── _card.scss 
│       ├── _forms.scss
│       ├── _header.scss
│       └── ...
├── utilities
│       ├── _typography.scss 
│       └── _error.scss
└── index.scss

```
Más información: [ITCSS: Scalable and Maintainable CSS Architecture](https://www.xfive.co/blog/itcss-scalable-maintainable-css-architecture/)

**🙌  Lo mejor de los dos mundos**

Podemos solucionar las carencias de cada sistema si los combinamos. Podemos usar la división de Atomic Design de átomos, moléculas y organismos para redefinir la carpeta components de ITCSS, consiguiendo así una guía clara de como organizar los componentes. Los objetos, al no tener estilos decorativos, los dejamos como nivel anterior a átomos.

La estructura combinando ITCSS con Atomic Design quedará así:

```
styles
├── settings
│       ├── _colors.scss
│       └── _typography.scss
├── tools
│       └── _mixins.scss
├── generic
│       ├── _normalize.scss
│       └── _box-sizing.scss
├── elements
│       ├── _headings.scss
│       └── _links.scss
├── objects
│       ├── _container.scss
│       └── _ui-list.scss
├── atoms
│       ├── _button.scss
│       ├── _image.scss
│       └── _pill.scss
├── molecules
│       ├── _card.scss
│       └── _form.scss
├── organisms
│       ├── _gallery.scss
│       └── _header.scss
├── utilities
│       ├── _typography.scss 
│       └── _error.scss
└── index.scss

```

Si nuestra aplicación no es demasiado grande, puede ser un poco overkill tener una estructura así, tenemos que valorar las necesidades de nuestros estilos y si lo vemos adecuado podemos juntar varios niveles: settings y tools, generic y elements, objetos y átomos… También podemos buscar inspiración en otros sistemas de arquitectura como [OOCSS](https://www.smashingmagazine.com/2011/12/an-introduction-to-object-oriented-css-oocss/) o [SMACSS](http://smacss.com/).

### 🛠️  Cómo utilizar variables, mixins y funciones- [Ejemplo en GitHub](https://github.com/CodelyTV/css-architecture-course/tree/main/5-1-settings-and-tools)

En las capas de Settings y Tools vamos a definir las variables de nuestro preprocesador, en este caso Sass, así como mixins y funciones.

Con las variables, es interesante usar variables privadas para definir valores, y variables públicas para definir sus aplicaciones. Por ejemplo, en _colors.scss tenemos varios colores definidos en variables privadas (si no sóis imaginativos con los nombres de colores, podéis usar herramientas como [Name that color](https://chir.ag/projects/name-that-color/)):

```
$_eucalyptus: #289f5f;
$_green-pea: #19623b;
$_baltic-sea: #282729;
$_cod-gray: #181818;
$_dove-gray: #666;
$_boulder: #777;
$_white: #fff;
$_alabaster: #fafafa;
$_gallery: #ececec;

```

Estas variables privadas las usamos para definir las variables públicas:

```
$primary-color: $_eucalyptus;
$primary-dark-color: $_green-pea;

$background-dark-color: $_baltic-sea;
$background-darker-color: $_cod-gray;
$background-light-color: $_alabaster;
$background-lighter-color: $_white;

$text-color: $_cod-gray;
$text-muted-color: $_dove-gray;
$text-inverted-color: $_white;

$border-color: $_dove-gray;
$border-inverted-color: $_gallery;

```

Esto nos permite ver fácilmente qué colores se repiten y en qué aplicaciones, y así simplificar colores si vemos que hay tonos muy parecidos aplicados a lo mismo (siempre consultando con el departamento de diseño).

Es importante que las variables públicas sean por aplicación. Por ejemplo, tenemos $background-darker-color y $text-color con el mismo valor, pero podría ser que desde diseño nos cambiaran el color de fondo pero no el del texto. Al ser variables independientes, será mucho más fácil hacer el cambio.

Los mixins y funciones de Sass es importante que se definan cuando vemos que sea necesario, y no definir demasiados de entrada, ya que estaríamos creando abstracciones prematuras.

**⛱️  La base del CSS**: 
estilos globales - [Ejemplo en GitHub](https://github.com/CodelyTV/css-architecture-course/tree/main/5-2-generic).

Cada navegador tiene sus estilos por defecto a la hora de mostrar como se muestra cada elemento. Aunque cada vez los navegadores son más consistentes entre si, utilizar un reset o un normalize nos puede facilitar la creación de estilos globales para conseguir más consistencia.

Un [reset](https://meyerweb.com/eric/tools/css/reset/) va a quitar los estilos de los elementos, unificándolos de tal forma que un h1 y un small van a ser del mismo tamaño, y un strong no será en negrita. Esto puede ser útil para asegurarnos que controlamos los estilos de todos los elementos, pero añade una carga de trabajo importante y puede dejar al usuario sin contexto si nos olvidamos de estilar algún elemento.

El [normalize](https://necolas.github.io/normalize.css/), en cambio, lo que hace es normalizar los estilos entre los navegadores, pero dejando las diferencias entre elementos. Así, si nos olvidamos de estilar algun elemento, los estilos por defecto seguirán actuando y darán pistas al usuario sobre lo que estan viendo. En general es más recomendable usar un normalize que un reset, ya que nos libra de una carga de trabajo importante y seguiremos teniendo unos estilos base coherentes en cualquier navegador.

En esta capa también pondremos otros estilos genéricos como definir el box-sizing.

### 💄 Empezando a aplicar estilos propios

En esta capa empezamos a definir nuestros estilos. Aún no escribiremos ninguna clase, sino que definimos como se mostrarán los elementos. Lo más habitual será definir estilos de tipografía y enlaces.

En este vídeo también vemos la diferencia entre usar  @import  y  @use a la hora de importar archivos Sass. Cuando trabajamos con variables y mixins, es recomendable usar @use para que las variables solo sean accesibles en ese módulo de Sass, y no de forma global. Así siempre vemos claramente de donde salen las variables que estamos usando, y nos queda el código más modular y fácil de leer y mantener. Sass esta desfavoreciendo el uso de @import y en los próximos años lo eliminará del lenguaje, por lo que es mejor acostumbrarnos a usar @use cuanto antes.

[Documentación sobre @import](https://sass-lang.com/documentation/at-rules/use#configuration)

[Documentación sobre @use](https://sass-lang.com/documentation/at-rules/use)

### 🦠 Crear objetos y Átomos 
- [Ejemplo en GitHub](https://github.com/CodelyTV/css-architecture-course/tree/main/6-1-objects-and-atoms).

Como hemos visto anteriormente, los objects son clases que no tienen propiedades de theme como colores, background, etc. Son clases que creamos para ayudarnos a dar estructura a nuestra aplicación, pero seguramente no corresponden directamente a algo que nos haya pasado el departamento de diseño. Los átomos, en cambio, sí que tienen propiedades visuales. Usando BEM, en estas clases podemos tener modifiers, pero no deberíamos encontrarnos con un objeto o átomo que tenga un element, ya que estamos definiendo los bloques más pequeños de nuestra aplicación.

Cuando vemos clases de átomo y objeto en nuestro html, no tenemos forma de saber de qué tipo de clase se trata. Eso puede dificultar encontrar el archivo para ver o modificar la clase si es necesario, y también nos dificulta la comprensión de qué hace cada clase. En aplicaciones grandes, nos puede interesar prefijar las clases según su tipo, para así ver rápidamente que tipo de clase es:

```
objects:    o-
atoms:      a-
molecules:  m-
organisms:  g-
utilities:  u-
            is-
            has-

```

Esto puede crear clases largas y difíciles de leer, especialmente combinado con BEM, así que hay que valorar si vale la pena o no en función del tipo de aplicación y el equipo que tengamos.

### 🧬 Crea clases con significado: 
Moléculas - [Ejemplo en GitHub](https://github.com/CodelyTV/css-architecture-course/tree/main/6-2-molecules)

Las moléculas son un conjunto de elementos simples, para formar un componente con sentido, pero no totalmente independiente.

Puede darse el caso que ciertos elementos de la molécula no se usen en ningún otro sitio, por lo que podemos definirlos como partes de ella, en lugar de tener una clase de átomo (por ejemplo, el botón de play dentro de la molécula video-thumbnail.

A este nivel, como ya nos estamos acercando al final del triángulo invertido de ITCSS, podemos empezar a anidar selectores (recordemos que los selectores deben ir de menos específicos a más).

En general debe evitarse definir propiedades como tamaños y márgenes en los estilos de una molécula, ya que siempre se va a utilizar dentro de un organismo, que es el que deberá dar la estructura que defina los tamaños. Si ponemos por ejemplo un max-width en una molécula, la estamos haciendo menos flexible y podría darse el caso que necesitemos ponerla dentro de un contexto en el que necesita ser un poco más grande, forzándonos a editar los estilos.

### 🦑 Crea componentes independientes: 
Organismos - [Ejemplo en GitHub](https://github.com/CodelyTV/css-architecture-course/tree/main/6-3-organisms).

Los organismos son las clases más concretas y menos reutilizables de nuestra aplicación. Van a formar elementos que son independientes y los podemos usar en diferentes páginas, pero raramente se van a repetir en la misma página. Por este motivo, en esta última capa de nuestros componentes es muy importante evitar crear abstracciones prematuras. Al ser bloques prácticamente únicos, será más fácil de mantener parte de código duplicado que mantener una abstracción incorrecta.

También se nos presentan preguntas interesantes si anidar selectores o bien crear clases específicas para ello. Hay casos, como en el organismo courses, que nos va a interesar no acoplar demasiado al contenido: ahora tenemos un grid de cards, pero se podría dar el caso que en un futuro, cambiáramos la molécula card por otra. Esto, combinado a que lo queremos estilar no tiene relación directa con card, hace que la mejor opción sea crear una nueva clase, courses__item. En cambio, si realmente quisiéramos sobreescribir alguna propiedad de card (quitar el border-radius, por ejemplo), tendría sentido usar el selector .courses .card.

Por último, tenemos la capa final de clases de utilidad. Como hemos visto en vídeos anteriores, las ponemos al final pese que a su selector es relativamente poco específico (una clase), queremos que sobreescriban cualquier clase anterior, y por esto es la única capa donde veremos el uso de !important.
