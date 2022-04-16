## Referencias! *---'cause we nerds, love them :)--*

- [WHATWG sobre el elemento `<kbd>`][0]
- [MDN HTML sobre `<kbd>`][1]
- [WHATWG sobre el elemento `<audio>`][2]
- [MDN HTML sobre `<audio>`][3]
- [MDN CSS sobre el uso de background e images][4]
- [MDN CSS sobre el shorthand background][5]
- [Tutorial en MDN sobre el uso de CSS transition][10]


<hr>

## Notas al día 1 - Javascript Drum Kit

El proyecto trae incorporado todo el HTML y CSS, y los scripts de Javascript son muy cortitos, por lo que mi verdadero ejercicio es hacer una *lectura activa* y repaso en mis notas de todo lo que conozco y desconozco de cada proyecto, sacándole el máximo rendimiento posible en cuanto a conocimientos a cada uno.

## HTML

La estructura básica de los elementos visibles está dada por este snippet: 

```html
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
```

- Todo el cuerpo de elementos está envuelto en un `<div class="keys"></keys>` Sigue una convención muy utilizada de utilizar identificadores en plural/singular para identificar contenedor/items. Esta convención también se ve mucho en el uso de identificadores en objetos en Javascript. 
- Utiliza el elemento `<kbd></kbd>` que está pensado para representar una entrada del usuario, típicamente a través del teclado aunque puede ser cualquier otra entrada.
- Ambos elementos inline `<kbd></kbd> y <span></span>` están envueltos en un div, imagino que para manipular de forma consistente la presentación de estos, y proveer un nivel semántico de *markup* consistente con el contenido. (cada grupo de nodos kbd>span solo tiene significado como bloque, no como elementos sueltos).
- Utiliza la convención no estandarizada de atributos del tipo `data-...` para asignar un valor de tecla específico, además de asignar la clase en singular del parent-container `class="key"`
- Por último, fuera del wrapper `.keys` que contiene todos los elementos visibles, existe una lista de elementos `<audio>` directamente en el body de la siguiente forma: 

```html
<audio data-key="65" src="sounds/clap.wav"></audio>
```

- Si se observa su atributo `data-key` puede observarse que coincide con el valor del mismo atributo en el ejemplo del container `.key` más arriba. 
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

- Luego, entre las líneas 13-18, este pequeño snippet define el modo de display en `flex` para container `.keys`. Esto es lo más sencillo considerando el tipo de interfaz, y el hecho de que se diseño la estructura del html con la caja contenedora `.key`
- La propiedad `flex: 1` es un shorthand con una sintaxis especial que debe ser aprendida. Las propiedades que acorta son: 
  - `flex-grow, flex-shrink` y `flex-basis`
- Sin embargo, cuando no están presentes los 3 valores, una serie de reglas aplican. En este caso, indica explícitamente que el valor para `flex-grow:1` mientras que las propiedades `flex-shrink y flex-basis` las deja en 1 y auto respectivamente. [Lectura][9]
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
  1. En este bloque se le dan color, tamaño, alineación y sombra al texto, para luego darle borde y fondo al contenedor.
  2. Se definen ancho, padding y margin no responsivos en `rem`
  3. Se define una rápida transición de todas las propiedades posibles en el elemento (creo, investigar bien el valor `all`) [Leer sobre su uso en MDN][10]

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
- Esta clase no está aplicada a ningún elemento directamente en el código, y se aplica dinámicamente como respuesta a un `eventListener` que se agregará en el script a la ventana.
- Tengo la impresión de que como estas son las propiedades que modifican al elemento `.key` que a su vez tiene la propiedad `transition: all .7s ease`, finalente esta última propiedad define de qué manera y en cuánto tiempo sucede este cambio de estado.
- **Sigo sin estar seguro** si `all` aplica solo a las propiedades explícitamente declaradas en esta última regla o aplica a otros estilos, browser-dependientes por ejemplo. 

<hr>

## Javascript

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
