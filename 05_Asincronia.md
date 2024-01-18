# DWEC UT04: Programación asíncrona y obtención de datos.

## Asincronía

La **asincronía** es uno de los conceptos principales que rige el mundo de Javascript. Cuando comenzamos a programar, normalmente realizamos tareas de forma **síncrona**, llevando a cabo tareas secuenciales que se ejecutan una detrás de otra, de modo que el orden o flujo del programa es sencillo y fácil de observar en el código:

```js
primera_funcion();    // Tarea 1: Se ejecuta primero
segunda_funcion();    // Tarea 2: Se ejecuta cuando termina primera_funcion()
tercera_funcion();    // Tarea 3: Se ejecuta cuando termina segunda_funcion()
```

Sin embargo, en el mundo de la programación, tarde o temprano necesitaremos realizar operaciones asíncronas, especialmente en ciertos lenguajes como Javascript, donde tenemos que realizar tareas que tienen que **esperar a que ocurra un determinado suceso que no depende de nosotros**, y reaccionar realizando otra tarea sólo cuando dicho suceso ocurra.

En Javascript no todas las tareas son asíncronas, pero hay ciertas tareas que si lo son, y probablemente se entiendan mejor con ejemplos reales:

* Un fetch() a una URL para obtener un archivo .json.
* Un play() de un .mp3 que creamos mediante un new Audio().
* Una tarea programada con setTimeout() que se ejecutará en el futuro.

Todos estos ejemplos se realizan mediante tareas **asíncronas**, ya que realizan un procedimiento que podría bloquear la ejecución del resto del programa al tardar mucho: la descarga de un fichero grande desde un servidor lento, una conexión a internet muy lenta, un dispositivo saturado a la hora de comunicarse con el sensor del móvil, etc...

## Como gestionar tareas asincronas

En Javascript existen varias formas de gestionar la asincronía, donde quizás las más populares son las siguientes (que iremos viendo y profundizando en cada artículo de este tema)

| Método | Descripción |
|----------|----------|
| `callbacks` | Probablemente, la forma más clásica de gestionar la asincronía en Javascript. |
| `promesas` | Una forma más moderna y actual de gestionar la asincronía. |
| `async/await` | Seguimos con promesas, pero con `async/await` añadimos azúcar sintáctico. |

Iremos viendo la implementación de cada una de ellas en cada capítulo de este tema, utilizando de base un ejemplo sencillo donde lanzamos un dado un número concreto de veces, que explicaremos a continuación.

### Ejemplo del dado

Tenemos un array, en el cuál insertaremos números. Insertaremos la cantidad de números que figura en `iterations`. En cada iteración se insertará un `number`, que habrá sido generado con una simulación del lanzamiento de un dado (un número aleatorio del 1 al 6). En el caso de obtener un 6, paramos y rompemos el bucle:

```js
const iterations = 10;
const numbers = [];

for (let i = 0; i < iterations; i++) {
  const number = 1 + Math.floor(Math.random() * 6);
  numbers.push(number);
  if (number === 6) {
    console.log("ERROR");
    break;
  }
}

console.log(numbers);
```