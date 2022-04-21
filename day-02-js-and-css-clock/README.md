## Notas al día 2 - Javascript and CSS clock
![imagen del proyecto][0]

<hr>

## Referencias! *---'cause we nerds, love them :)--*

- [MDN sobre CSS background-size][1]
- [MDN sobre CSS transform-origin][2]
- [][6]
- [][7]


<hr>

Este proyecto, el día 2 de los 30, consiste en crear un pequeño reloj con CSS, y animarlo a través del uso de un `setInterval` para que las manecillas reciban un cambio en la propiedad `transform` así como pasa el tiempo. 

## HTML

- El código HTML de este proyecto es realmente mínimo: 

```html
<div class="clock">
    <div class="clock-face">
      <div class="hand hour-hand"></div>
      <div class="hand min-hand"></div>
      <div class="hand second-hand"></div>
    </div>
  </div>
```

- Nada de discusiones filosóficas sobre el elemento adecuado. Un lote de divs anidados. El primero, de clase `.clock` es el contenedor general del reloj que genera el border blanco, el segundo div, de clase `.clock-face` es un marco invisible que ocupa todo el espacio del primero y contiene los siguientes últimos 3 divs, compartiendo la clase `.hand` y anidados en el tercer nivel, son las manecillas del reloj. 
- Respecto a estos últimas tres, aunque comparten la clase `.hand` porque el proyecto no contempla establecer diferencias visuales entre las manecillas, cada una tiene su propia clase de tipo `.hour | min | second -hand` que sirve para apuntarla con la utilidad del DOM que la animará. 

<hr>

## CSS

Respecto al CSS, consiste en 5 reglas que se pueden agrupar en 2 bloques generales; en un bloque se pueden considerar las 4 primeras reglas que dan el aspecto al fondo, y al reloj, y por otro lado, la última regla que tiene el aspecto de las manecillas, pero también un par de reglas necesarias para que la animación con Javascript funcione correctamente. Entonces:

```css
/* BLOQUE 1 - aspecto general */

html {
  background: #018ded url(https://unsplash.it/1500/1000?image=881&blur=5);
  background-size: cover;
  font-family: 'helvetica neue';
  text-align: center;
  font-size: 10px;
}

body {
  margin: 0;
  font-size: 2rem;
  display: flex;
  flex: 1;
  min-height: 100vh;
  align-items: center;
}
```

- En estas dos primeras reglas, se establece el background del proyecto, una imagen azul, además de un color hexadecimal como fallback (o por si fallara la propiedad `background-size`). En la segunda línea, a través del valor `contain` se establece que la imagen debe crecer hasta cubrir todo el espacio disponible, manteniendo la relación de aspecto de la imagen. Si la relación de aspecto del contenedor no corresponde al de la imagen, simplemente se recorta esta última para ajustarla al contenedor. Leer sobre [`background-size`][1]
- No imagino qué efecto podría tener configurar una fuente y tamaño excepto para el alto de los `div`'s pero esos están configurados con altos explícitos. Hago una prueba y veo que el tipo de letra y su alineación no cambian nada pero el tamaño de fuente definitivamente tiene un impacto en el tamaño del reloj. **Debo curiosear del porqué si los `div`'s centrales no tienen contenido.** 
- Luego en la segunda regla, utiliza un `margin: 0` como un *microreset* y luego configura un tamaño de fuente que parece no tener ningún efecto. Entonces setea el `display: flex` que le permite alinear verticalment el reloj con la propiedad `align-items: center` Las otras dos reglas definen que el body ocupará el 100% del alto de la pantalla del dispositivo. 
- La declaración `flex: 1` parece no tener ningún efecto inmediato.

- Las siguientes dos reglas dan aspecto al reloj mismo. Van así: 

```css
.clock {
  /* crean un cuadrado ...*/
  width: 30rem;
  height: 30rem;
  /* con un border blanco y grueso... */
  border: 20px solid white;
  /* que se redondea hasta quedar circular */
  border-radius: 50%;
  /* El elemento se alinea horizontalmente */
  margin: 50px auto;
  /* se determina que su posición sea relative */
  position: relative;
  /* no tiene mucho sentido en este ejercicio (creo) */
  padding: 2rem;
  /* da un efecto de relieve al marco del reloj */
  box-shadow:
    0 0 0 4px rgba(0,0,0,0.1),
    inset 0 0 0 3px #EFEFEF,
    inset 0 0 10px black,
    0 0 10px rgba(0,0,0,0.2);
}

.clock-face {
  /* probablemente para definir absolute las manecillas */
  position: relative;
  /* alto y ancho al completo del contenedor */
  width: 100%;
  height: 100%;
  /* para mover ligeramente el div a la izquierda y que las manecillas queden centradas */
  transform: translateY(-3px);
}
```

<hr>

- Finalmente, la última regla les da estilo a las manecillas. Las primeras 3 declaraciones solo dan estilo y tamaño a las manecillas, las siguientes, las posicionan y declaran el punto de pivoteo de la rotación que sufrirán al marcar la hora. Además se dejan inicializadas a las 12 en punto. Las últimas 2 declaraciones crean una transición para que las manecillas no avanzan bruscamente, y configuran el timing de esa transición en una curva tal, que crea un efecto muy notable similar a los antiguos relojes que parecía que "acomodaban" las manecillas después de avanzar [Leer sobre esta función][2]: 

```css
.hand {
  /* alto, ancho y fondo */
  width: 50%;
  height: 6px;
  background: black;
  /* posicionamiento al centro de .clock-face */
  position: absolute;
  top: 50%;
  /* se mueve pivote de rotación del centro del elemeneto a su extremo derecho */
  transform-origin: 100%;
  /* se coloca la manecilla en la posición "12 hrs" */
  transform: rotate(90deg);
  /* se crea la transición para suavizar avance de las manecillas */
  transition: all 0.05s;
  /* se busca con el editor de curvas bezier del inspector una que */
  /* imite el aspecto del retroceso de las agujas de un reloj mecánico */
  transition-timing-function: cubic-bezier(0.1, 2.7, 0.58, 1);
}
```

<hr>

Ahora, algunas capturas que ilustran como afectan los distintos posicionamientos y display al layout final de los elementos:

![imagen1][3]

bla bla comentarios

<hr>

![imagen1][4]

bla bla comentarios

<hr>

![imagen1][5]


<hr>



<hr>

## Javascript

bla blah bla blah.

<hr>

[0]:./img/proyecto.png
[1]:https://developer.mozilla.org/en-US/docs/Web/CSS/background-size
[2]:https://developer.mozilla.org/en-US/docs/Web/CSS/transform-origin
[3]:./img/inspector.clock.png
[4]:./img/inspector.clock-face.png
[5]:./img/inspector.hand.png