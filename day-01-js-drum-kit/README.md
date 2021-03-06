## Notas al día 1 - Javascript Drum Kit
![imagen del proyecto][17]

<hr>

## Referencias! *---'cause we nerds, love them :)--*

- [WHATWG sobre el elemento `<kbd>`][0]
- [MDN HTML sobre `<kbd>`][1]
- [WHATWG sobre el elemento `<audio>`][2]
- [MDN HTML sobre `<audio>`][3]
- [MDN CSS sobre el uso de background e images][4]
- [MDN CSS sobre el shorthand background][5]
- [Tutorial en MDN sobre el uso de CSS transition][10]
- [Website para encontrar el keycode de cualquier tecla][11]
- [MDN sobre CSS box-shadow][14]
- [MDN sobre evento transitionEnd][16]
- [MDN sobre objeto *this*][18]


<hr>


El proyecto consiste en una interfaz web mínima en la que el usuario puede presionar alguna de las teclas sugeridas en en la pantalla para provocar un sonido de percusión. El proyecto trae incorporado todo el HTML y CSS, y por su parte, el código de Javascript es muy cortito, por lo que el ejercicio consiste sobre todo en realizar una *lectura activa* y repasar con estas notas sobre todo lo que conozco y desconozco de su código, sacándole el máximo rendimiento posible en cuanto a conocimientos.

## HTML

La estructura básica de los elementos visibles está dada por este snippet: 

```html

<!-- Primera sección -->
<div class="keys"> <!-- Contenedor general del proyecto -->

    <!-- Esta es la unidad básica del proyecto -->
    <div data-key="65" class="key"> 
      <kbd>A</kbd>
      <span class="sound">clap</span>
    </div>
    <!-- Se repite tantas veces como es necesario -->
    <!-- para los sonidos/teclas restantes -->
    ...

</div>

<!-- Segunda sección -->
<audio data-key="65" src="sounds/clap.wav"></audio>
<audio data-key="..." src="..."></audio>
<!-- Hay un elemento para cada grupo .key -->
```

- En el código dentro de `<body>`, inmediatamente se observan dos grandes secciones: 
- La primera sección, `.keys` la conforma un container `<div class="keys"></div>` que alberga una cantidad de `<div class="key"></div>` , estos últimos forman la unidad a presentar (borde, tecla, sonido) 
- Luego, la segunda sección, consiste en una serie de elementos `<audio>`, uno para cada `.key` que invocan a través de su atributo `src="path..."` un archivo wav con el sonido asociado a cada tecla.
- Los nombres de clase del container y las secciones siguen una convención muy utilizada de utilizar identificadores en plural/singular para contenedor/items. Esta convención también se ve mucho en el uso de identificadores en objetos en Javascript. 
- Dentro de cada unidad `.key` se utiliza el elemento `<kbd></kbd>` que está pensado para representar una entrada del usuario, típicamente a través del teclado aunque puede ser cualquier otra entrada además de un elemento `<span></span>`. 
- Utiliza la convención no estandarizada de atributos del tipo `data-...` para asignar un valor de tecla específico, además de asignar la clase en singular del parent-container `class="key"`
- Algo interesante sobre este último punto, es que se utiliza el valor del atributo `data-key=""` para relacionar un grupo `.key` con un elemento específico `<audio>` como se observa al comparar el valor de `data-key=""` en el primer grupo `.key` y luego en el primer elemento `<audio>`
- El elemento audio por [defecto no es visible][6] a no ser que se declare al atributo que presenta los controles de reproducción (tiene `display: none`): ![elemento html audio en el inspector][7] 

<hr>

## CSS

```css
html {
  font-size: 10px;

  background: url('./background.jpg') bottom center;
  background-size: cover;
}

body,html {
  margin: 0;
  padding: 0;
  font-family: sans-serif;
}
```

- No me queda claro si hacer la configuración del `font-size` en el selector `html` es lo mismo que hacerlo en `:root` afectando el valor de la unidad `rem` 
  - **UPDATE:** Funciona con ambas, pruebas en Firefox y [lectura][8] .
- Se asigna un `background` al elemento `html` y luego se realiza un *micro-reset* se observa el uso del shorthand `background` 
- **No estoy seguro:** de que el uso de dos palabras clave tenga efecto alguno en la propiedad `background-position`. En los ejemplos revisados cuando hay dos palabras clave suelen ir acompañadas de un porcentaje o valor numérico, al menos 1 de las dos. Quizá implica un comportamiento por defecto pero no lo pude descubrir en una ojeada rápida.

<hr>

```css
.keys {
  display: flex;
  flex: 1;
  min-height: 100vh;
  align-items: center;
  justify-content: center;
}
```

- Luego, entre las líneas 13-18, esta regla CSS define el modo de display en `flex` para container `.keys`. Esto es lo más sencillo considerando el tipo de interfaz, y el hecho de que se diseño la estructura del html con la caja contenedora `.key`
- La propiedad `flex: 1` es un shorthand con una sintaxis especial -similar a la de margin- según la cantidad y tipo de valores que se indiquen, y debe ser aprendida. Las propiedades que acorta son: 
  - `flex-grow, flex-shrink` y `flex-basis`
- En este caso, indica explícitamente que el valor para `flex-grow:1` mientras que las propiedades `flex-shrink y flex-basis` las deja en 1 y auto respectivamente. [Lectura][9]
- No conozco la diferencia entre usar `width: 100vw` y `min-widht: 100vw` en un caso como este, sin otros parents que `<body>`

<hr>

```css
.key {
  /* bloque 1 */
  color: white;
  font-size: 1.5rem;
  text-align: center;
  text-shadow: 0 0 .5rem black;
  border: .4rem solid black;
  border-radius: .5rem;
  background: rgba(0,0,0,0.4);
  /* bloque 2 */
  width: 10rem;
  padding: 1rem .5rem;
  margin: 1rem;
  /* bloque 3 */
  transition: all .07s ease;
}
```

- En este snippet se le da estilo a los contenedores `.key` 
- He reordenado un poco las propiedades en 3 bloques para separar la finalidad de cada bloque:
  1. En el bloque 1 se le define color, tamaño, alineación y sombra al texto, para luego darle borde y fondo al contenedor.
  2. En el bloque 2 se definen ancho, padding y margin no responsivos en `rem`
  3. En el bloque 3 se define una rápida transición de todas las propiedades posibles en el elemento (creo, investigar bien el valor `all`) [Leer sobre su uso en MDN][10]

<hr>

```css
kbd {
  display: block;
  font-size: 4rem;
}
```
- Esta parte del código hace que los elementos `<kbd> y <span>` se coloquen uno arriba del otro en vez de en línea como es su comportamiento.
- **Algo interesante** es entender que, tal como se ve acá, no es necesario darle `display: block` a ambos elementos, pues siendo solo dos, con que el primero ocupe todo su ancho disponible, ya creará el salto de línea que logra que el segundo elemento quede debajo. 



```css
.sound {
  text-transform: uppercase;
  letter-spacing: .1rem;
  font-size: 1.2rem;
  color: #ffc600;
}
```

- En esta regla, se hace explícito el que todo el contenido de texto en el elemento `.sound` se presente en mayúsculas, además de definir espaciado de letras, tamaño y color de estas. 

<hr>

```css
.playing {
  transform: scale(1.1);
  border-color: #ffc600;
  box-shadow: 0 0 1rem #ffc600;
}
```

- Finalmente, esta regla declara un ligero escalado, además de cambiar el color del borde y la sombra del elemento al que se aplica.
- **Algo interesante** es que no es necesario declarar valores distintos de 0 en los primeros argumentos de `box-shadow` mientras tenga blur. 
- **NOTA** esto pasa porque los dos primeros valores corresponden al desplazamiento en el eje X e Y correspondientemente. Si se indican en 0 la sombra se aplicará pareja en los cuatro lados. [Leer sobre esto][14]
- Esta clase no está aplicada a ningún elemento directamente en el código, y se aplica dinámicamente como respuesta a un `eventListener` que se agregará en el script a la ventana.
- Tengo la impresión de que como estas son las propiedades que modifican al elemento `.key` que a su vez tiene la propiedad `transition: all .7s ease`, finalente esta última propiedad define de qué manera y en cuánto tiempo sucede este cambio de estado.
- **Sigo sin estar seguro** si `all` aplica solo a las propiedades explícitamente declaradas en esta última regla o aplica a otros estilos, browser-dependientes por ejemplo. 

<hr>

## Javascript

- El tutorial comienza con lo medular: 

```javascript
window.addEventListener('keydown', function(e) {
  // aca pasa algo
})
```
- Al objeto window (el elemento que contiene todo lo que pasa en nuestra página, desde lo propio del documento html hasta al entorno de la ventana del navegador) se le añade un listener que *escuchará*  el momento en que el usuario presione una tecla.
- Cuando eso suceda, se ejecutará el código dentro de la función del listener. El argumento `e` que puede llamarse de cualquier otra manera, es el argumento que contiene el `evento` mismo, el objeto con todas las propiedades capturadas cuando se disparó el evento *presionar tecla*. 
- Lo siguiente que realiza el relator del tutorial, es asignar un selector a una constante. El uso de constantes en cada momento que sea posible se considera una buena práctica: 

```javascript
const audio = document.querySelector(`audio[data-key="${e.keyCode}"])
```

- Acá se observa que el selector *solicita* que se seleccione un elemento de tipo `<audio>` a través de la condición siguiente: 
  - "busca y asigna a la constante `audio` el elemento audio que tiene un atributo `data-key` y cuyo valor es `${e.keyCode}`" ...WTF!
- Entonces, hago un pequeño `console.log(e)` y otro `console.log(e.keyCode)` para observar de qué trata esto: 

![inspector muestra event y event.keyCode][12]

- El primer console log nos muestra todo el objeto que está guardado en el argumento `e` de la función que se ejecuta cuando se dispara el `eventListener('keydown')` del primer snippet. Estos objetos *event* son particulares para cada tipo de evento: uno de mouse puede traer las coordenadas dentro de la pantalla en que ocurrió un click y otros como este, traen una serie de datos sobre la tecla presionada, entre ellos, si se observa bajando alfabéticamente, uno llamado `keyCode` que contiene el valor de la tecla presionada. En `console.log(e.keyCode)` siguiente solicita directamente el valor de esta propiedad, por lo que retomando con el selector podemos decir que: 

```javascript
window.addEventListener('keydown', function(e) {
  const audio = document.querySelector(`audio[data-key="${e.keyCode}"])
})
```

- dice: "Busca UN elemento de tipo `audio`, que tiene un atributo `data-key` cuyo valor es el valor de la propiedad `keyCode` del evento que se dispara cuando un usuario presiona una tecla, y asígnalo a la constante `const audio`".
- ahora es fácil entender mirando la captura anterior al inspector de elementos, además del codigo html y este snippet, que el valor que está asignado en el atributo `data-key` tanto de `<audio>` como de cada grupo `.key` es el valor del keyCode asociado a la letra que se espera que el usuario presione para obtener un evento y es el valor con el que se busca el elemento `<audio>` que contiene el sonido correspondiente. 
- entonces, acto seguido, se solicita que se reproduzca el sonido del archivo indicado en el atributo `src` del elemento que fue obtenido con la línea `audio.play()`
- Sin embargo, esto trae un problema inmediato: ¿qué pasa si el usuario presiona una tecla que no está en el listado de teclas asociadas a un sonido?. Esto: 

![captura del inspector de elemento con error por audio null][13]

- pasa que se envía un error a la consola que indica que el evento buscó tal elemento audio pero no encontró nada, y luego asignó ese valor `null` (nada) a la constante `audio` para luego solicitarle que invocara la función de reproducir su audio con `audio.play()` y acá la consola nos dice "hey! no puedo hacer lo que solicitas porque el elemento es `null`!". Por eso se agrega la línea `if (!audio) return` para que la función deje de ejecutarse si el valor de esa función es null. Queda así: 

```javascript
window.addEventListener('keydown', function(e) {
  const audio = document.querySelector(`audio[data-key="${e.keyCode}"]);
  if (!audio) return;
  audio.play()
})
```
- Ahora, pasa que si uno presiona la tecla más rápido que lo que dura el sonido, este no vuelve a reproducirse. Esto tiene sentido, ya que si se está reproduciendo el elemento solicitado, hacer audio.play() no tiene sentido sino hasta que el audio termine. Pero acá se acude a otra propiedad de los elementos de tipo audio -también video- que es `currentTime`. Esta contiene en todo momento un valor que represente el punto actual en el tiempo que se está reproduciendo. Y como queremos que el audio se reproduzca al ritmo que presionamos las teclas, la usaremos para *rebobinar* el audio al punto 0 antes de reproducirlo, para que nos permita ejecutar audio.play() cada vez que presionemos una tecla: 

```javascript
window.addEventListener('keydown', function(e){
  const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);
  if (!audio) return;
  audio.currentTime = 0;
  audio.play();
})
```
- hasta acá, la parte del proyecto donde nuestras teclas invocan un sonido funciona bien, ahora se necesita que la objeto en pantalla que representa la tecla tenga una pequeña animación. Nos hacemos una idea de la animación que se quiere lograr con este snippet de CSS:

```css
.key {
  border: .4rem solid black;
  box-shadow: none; /* implícito */
  transition: all .07s ease;
}

.playing {
  transform: scale(1.1);
  border-color: #ffc600;
  box-shadow: 0 0 1rem #ffc600;
}
```


- se entiende por las dos reglas que lo que se busca es que el grupo `.key` creza un 10% mientras aparece un border de amarillo muy contrastante con el background y los elementos `.key` 
- Todo lo anterior pasa en una fracción de segundo como indica el segundo valor en la propiedad `transition` de la primera regla.

![screenshot del efecto][15]

- Para lograrlo, se han realizado dos acciones: primero, justo debajo del primer `querySelector` se ha creado otro, que busca un grupo `.key` bajo la misma condición que con el elemento `<audio>`: que su atributo `data-key` sea el mismo del `e.keyCode` del evento disparado al presionar una tecla y se le asigna a una constante `key`. Luego, con ese elemento seleccionado, se utiliza esta propiedad de la DOM API: `key.classList.add('playing)` que dice algo así: una vez que encuentres y selecciones ese elemento asígnalo a la constante `key` y entonces, a su listado de clases `classList` agrégale otra nueva `add` de nombre `playing`: 

```javascript
window.addEventListener('keydown', function(e){
  const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);
  const key = document.querySelector(`.key[data-key="${e.keyCode}"]`);
  if (!audio) return;
  audio.currentTime = 0;
  audio.play();
  key.classList.add('playing');
})
```

- OK, hasta acá genial, pero algo pasa: no se quita el efecto. Este aparece en el tiempo especificado, pero luego no se va. Y claro, el código indica claramente que cuando se presione una tecla se agregue esa clase que provoca el adorno, pero nunca se indica que se le quite.
- Para corregirlo se utiliza un *eventListener* llamado `transitionEnd`. Este evento se dispara cuando en el elemento apuntado, ha concluido cualquier transición que se le haya agregado. En este caso la transicíón es la que indica la línea de CSS `transition: all .07s ease`
- Para lograrlo, se debe crear un listener en cada grupo `.key` usando un *selectorAll* y un *forEach*: 

```javascript
const keys = document.querySelectorAll('.key');
keys.forEach(key => key.addEventListener('transitionEnd', function(e){
  if (e.propertyName !== 'transform') return; 
  this.classList.remove('playing');
}))
```

- Todo funcionando bien aca. Este último párrafo puede ser un poco *tricky* así que vamos por línea: 
- a la constante `keys` se le asigna un arreglo con todos los elementos que tengan la clase `.key`
- ese arreglo se recorre mediante el método `forEach` que toma 1 elemento en cada iteración `key` para agregarle el eventListener `key.addEventListener` que estará atento al momento en que termine cualquier transición que ocurra en este `('transitionEnd', function(e){ ...})` [Leer acá sobre transitionEnd][16]
- solo queremos escuchar cuando termine la transición 'transform' porque es la más importante, por lo tanto si la propiedad que ha concluido no es `transform` termina la ejecución de la función `if (e.propertyName !== 'transform') return; 
- Si es transform, continuar y entonces, quitar la clase que crea el adorno: `this.classList.remove('playing'); 
- El operador *`this`* puede ser un poco confuso  pero sin complicarlo mucho, es similar al argumento `e` en que "trae" valores consigo por defecto, que en el caso de `this` es el objeto que invoca a la función. Y como el objeto que ha invocado la función de `removeTransition` es un grupo `key`, este al ser utilizado es como indicar `key` que en el contexto del bloque de la función ya está fuera de alcance. (creo) **REVISAR** [Leer sobre this en MDN][18]
- **And... that's it!** 
- Solo queda observar que la última línea que llama al elemento del contexto *this* también se puede realizar con la propiedad interna del evento `target`, que registra el elemento sobre el que se ha disparado el evento en cuestión: 
  - `e.target.classList.remove('playing');

- Finalmente, se hace una pequeña refactorización del código para que quede más ordenado y modular y se lleva toda la lógica de los eventListeners a funciones separadas y queda así: 

```javascript
function removeTransition(e) {
  if (e.propertyName !== 'transform') return;
  e.target.classList.remove('playing');
}

function playSound(e) {
  const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);
  const key = document.querySelector(`div[data-key="${e.keyCode}"]`);
  if (!audio) return;

  key.classList.add('playing');
  audio.currentTime = 0;
  audio.play();
}

const keys = Array.from(document.querySelectorAll('.key'));
keys.forEach(key => key.addEventListener('transitionend', removeTransition));
window.addEventListener('keydown', playSound);
```


<hr>

[0]:https://html.spec.whatwg.org/multipage/text-level-semantics.html#the-samp-element
[1]:https://developer.mozilla.org/en-US/docs/Web/HTML/Element/kbd
[2]:https://html.spec.whatwg.org/multipage/media.html#the-audio-element
[3]:https://developer.mozilla.org/en-US/docs/Web/HTML/Element/audio
[4]:https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Backgrounds_and_borders
[5]:https://developer.mozilla.org/en-US/docs/Web/CSS/background
[6]:https://developer.mozilla.org/en-US/docs/Web/HTML/Element/audio#styling_with_css
[7]:https://assets.codepen.io/6378904/html-audio-browser-default.png
[8]:https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units#lengths
[9]:https://developer.mozilla.org/en-US/docs/Web/CSS/flex
[10]:https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions
[11]:https://keycode.info
[12]:./img/inspector-event-keyCode.png
[13]:./img/inspector-error-audio-null.png
[14]:https://developer.mozilla.org/en-US/docs/Web/CSS/box-shadow
[15]:./img/screenshot-efecto-transition.png
[16]:https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/transitionend_event
[17]:./img/JS-Drum-Kit.png
[18]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this