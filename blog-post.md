## Intro
[Ya que hemos tenido una vista general de Vue.js](/introduccion-a-vue-js), este post a detalle la manera en la que puedes utilizar Vue.js para crear interactuar con el dom y anadir interactividad a tu aplicacion.


## Mostrar texto
Lo primero es como mostrar algo en la pagina. La manera mas sencilla de mostrar datos en Vue es usando `{{ }}`, lo cual tambien es conocido como interpolacion.

Por ejemplo, si tenemos un mensaje (`msg`) en nuestra instancia de Vue como
```js
new Vue({
    el: '#app',
    data: {
        msg: 'Hola'
    }
})
```

Podemos mostralo en la aplicacion usando poniendo la variable entre `{{ }}`.
```html
<span id="app">Mensaje: {{ msg }}</span>
```

Esto se evaluara y como resultado quedara:
```html
<span id="app">Mensaje: Hola</span>

```
Ademas, si esa variable llega a ser modificada, Vue automaticamente actualizara la aplicacion para reflejar ese cambio.


## Expresiones
Algo importante de esta interpolacion es que tambien puede evaluar expresiones simples. Por ejemplo
```html
<span>{{ 1 + 1 }}</span>
```
Resultara en :
```html
<span>2</span>
```
Esto es importante porque nos permite usar operadores ternarios como:
```html
<span>{{ esSaludo ? 'hola' : 'adios' }}</span>
```

O incluso ejecutar metodos y mostrar su resultado como:
```html
<span>{{ sumar(1, 1) }}</span>
```

Sin embargo, no recomiendo usar expresiones en tus templates, ya que Vue nos ofrece una mejor forma de hacerlo, computed properties.


## Computed properties
Las computed properties, o propiedades calculadas(?), son propiedades igual que las que declaramos en `data`, pero su valor no se especifica explicitamente, sino que se calcula en tiempo de ejecucion.

Por ejemplo:
```html
<div id="app">
    <input type="text" v-model="mensaje">
    <p> {{ mensaje }} </p>
    <p> {{ mensajeInvertido }} </p>
</div>
```
```js
new Vue({
    el: '#app',
    data: {
        mensaje: 'Hola',
    },
    computed: {
        mensajeInvertido: function() {
            return this.mensaje.split().reverse().join();
        }
    }
})
```

En este caso tenemos una propiedad `mensaje`, la cual esta bindeada a un `input`. Como Vue se encarga de actualizar la aplicacion para mostrar el valor actual de las propiedades, no es sorpresa que al cambiar el valor del input la parte de `<p> {{ mensaje }} </p>` muestre el cambio tambien. Lo interesante es que `<p> {{ mensajeInvertido }} </p>` tambien se actualiza al cambiar el input. Esto es debido a la computed property que declaramos.

![](EJEMPLO)

La manera en que esto funciona es que tu declaras una funcion con el valor que deberia tener tu propiedad en todo momento. En nuestro caso, queremos mostrar el mensaje invertido asi que declaramos esa propiedad dentro de `computed` en nuestra instancia de Vue.
```js
new Vue({
    // ...
    computed: {
        mensajeInvertido: function() {
            return this.mensaje.split().reverse().join();
        }
    }
})
```

Vue analiza tu funcion y detecta de que otras propiedades depende (en este caso, depende de la propiedad `mensaje`) y actualiza el valor de la computed property cada vez que esas propiedades de las que depende cambien, usando la funcion que declaramos.

Esto quiere decir que cada vez que `mensaje` cambie, Vue automaticamente ejecuta la funcion asociada a `mensajeInvertido` y le asigna el valor que retorne esa funcion.

Esto es bueno porque nos permite mantener un template limpio y ademas Vue es capaz de realizar optimizaciones internas usando las computed properties que de otra manera no seria capaz de hacer. Debido a esto, es recomendable que uses computed properties cuando puedas en lugar de expresiones en los templates.


## Atributos
En el caso de los atributos de html, no podemos usar la interpolacion de texto. En su lugar, tenemos `v-bind`, el cual igual mantiene actualizado al atributo que le especifiquemos con el valor mas reciente de la variable.

La manera en que usamos `v-bind` es como `v-bind:atributo="variable"`. Por ejemplo:

```html
<button v-bind:disabled="botonDesabilitado">Boton</button>
```

En este caso, el atributo `disabled` tendra siempre el valor de la variable `botonDesabilitado`. Por lo que si `botonDesabilitado` tiene un valor de `true`, el boton estara desabilitado y funcionara solo cuando `botonDesabilitado` equivalga a `false`.

Como extra, `v-bind` puede ser acortado simplemente como `:`. Asi que podemos remplazar `v-bind:disabled="botonDesabilitado"` por `:disabled="botonDesabilitado"` y aun asi funcionara igual.

```html
<button :disabled="botonDesabilitado">Boton</button>
```

## Eventos
La manera en que podemos escuchar por eventos en Vue en usando la directiva `v-on`. `v-on` se declara como una directiva con el formato `v-on:[evento]="[cosasAEjecutar]"`, donde el `[evento]` es el nombre del tipo de evento que queremos escuchar.

Por ejemplo:

```html
<div id="app">
    <button v-on:click="sumarUno"></button>
    <p> {{ contador }} </p>
</div>
```
```js
new Vue({
    el: '#app',
    data: {
        contador: 1
    },
    methods: {
        sumarUno: function() {
            this.contador++;
        }
    }
})
```
En este caso tenemos una propiedad `contador` y un metodo `sumarUno` que incrementa el `contador` en 1, el cual ejecutamos cuando se hace click en el boton.

La manera en que especificamos fue evento es con `v-on:click="sumarUno"`, lo cual escucha al evento `click` y al suceder ejecuta la funcion `sumarUno`.

Tambien, igual que con el binding a atributos, vue nos ofrece `@` como manera de acortar `v-on`. Entonces, podemos remplazar `v-on:` por `@` y seguira funcionando igual.

```html
<div id="app">
    <button @click="sumarUno"></button>
    <p> {{ contador }} </p>
</div>
```


## Clases de css dinamicas
Vue nos ofrece diferentes maneras de tratar con los estilos y clases de css, pero la mas sencilla es usar computed properties para definir las clases que queremos que tenga un elemento, de la siguiente manera:

```css
.bloque {
    width: 400px;
    height: 400px;
}
.rojo { background: red; }
.azul { background: blue; }
```
```html
<div id="app">
    <div class="bloque" :class="color"></div>
    <botton @click="cambiarColor">Cambiar color</botton>
</div>
```
```js
new Vue({
    el: '#app',
    data: {
        color: 'rojo'
    },
    methods: {
        cambiarColor: function() {
            if (color === 'rojo') {
                return 'blue';
            }

            return 'rojo';
        }
    }
})
```

En este caso, simplemente cambiamos el color del bloque al hacer click en el boton.

Lo unico diferente aqui es que tenemos dos veces `class` en el `div` que queremos colorear. La primera, `class="bloque"`, es una clase que siempre va a tener el elemento. La segunda, `:class="color"`, es nuestra case dinamica. Vue se encarga de agregar el valor de `color` como una clase, por lo que si su valor es `rojo`, tendra las clases `bloque rojo`, y si es azul, `bloque azul`.

---

Con esto ya tenemos el conocimiento base de Vue.js para crear aplicaciones sencillas, pero si quieres aprender mas, checa el siguiente post donde revisamos como trabajar con condiciones y listas para agregar mayor valor a tus aplicaciones.

Como siempre, los ejemplos de este post están disponible en github para cualquier duda que tengas o mejora que quieras agregar, así que no dudes en hacerlo!

---

*Este post es parte de [una serie acerca de VueJS](/tag/vue-js/). Si tienes dudas o hay alguna funcionalidad que aun no haya cubierto, no dudes en avisarme.*