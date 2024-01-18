# DWEC UT04: Programación asíncrona y obtención de datos.

## Callbacks

Los callbacks (a veces denominados funciones de retrollamada o funciones callback) no son más que un tipo de funciones que se pasan por parámetro a otras funciones. El objetivo de esto es tener una forma más legible de escribir funciones, más cómoda y flexible para reutilizarlas, y además entra bastante en consonancia con el concepto de asincronía de Javascript.

Probablemente, el caso más fácil de entender es utilizar un temporizador mediante la función `setTimeout`(`function` callback, `number` time). Dicha función nos exige dos parámetros:

* La función callback a ejecutar
* El tiempo time que esperará antes de ejecutarse

```js
function action() {
  console.log("He ejecutado la función");
}
setTimeout(action, 2000);

// Utilizando arrow functions
setTimeout(() => {
  console.log("He ejecutado la función");
}, 2000);
```

En cualquiera de los casos, lo importante es darse cuenta que estamos usando una función `callback` para pasársela a `setTimeout()`, que es otra función. En este caso, se trata de «programar» un suceso que ocurrirá en un momento concreto del futuro, pero muchas veces desconociendo cuando se producirá exactamente (o incluso si se llegará a producir).

```js
setTimeout(() => {
  console.log("Código asíncrono.");
}, 2000);

console.log("Código síncrono.");
```

El último `console.log` del código se ejecuta primero (forma parte del flujo principal de ejecución del programa). El `setTimeout()` que figura en una línea anterior, aunque se ejecuta antes, pone en espera a la función `callback`, que se ejecutará cuando se cumpla una cierta condición (transcurran 2 segundos desde ese momento).

### Ejemplo del dado

Vamos a utilizar el ejemplo del lanzamiento de 10 dados que explicamos en el capítulo de [Asincronía](./05_Asincronia.md), para adaptarlo a funciones `callbacks`.

```js
const doTask = (iterations, callback) => {
  const numbers = [];

  for (let i = 0; i < iterations; i++) {
    const number = 1 + Math.floor(Math.random() * 6);
    numbers.push(number);
    if (number === 6) {
      callback({
        error: true,
        message: "Se ha sacado un 6"
      });
      return;
    }
  }

  /* Termina bucle y no se ha sacado 6 */
  return callback(null, {
    error: false,
    value: numbers
  });
}
```

Como se puede ver, estamos utilizando `arrow functions` para definir la función `doTask()`. Le pasamos un parámetro `iterations` que simplemente indica el número de iteraciones que tendrá el bucle (número de lanzamientos del dado). Por otro lado, el segundo parámetro es nuestro `callback`, que recordemos que es una función, por lo que podremos ejecutarla en momentos concretos de nuestro código. Lo hacemos en dos ocasiones:

1. En el `if` cuando number es 6 (detectamos como error cuando obtenemos un 6). Le pasamos un objeto por parámetro que contiene un `error` y `message`, el mensaje de error.

2. Tras el `for`, con dos parámetros. El primer parámetro: , ya que en este caso no hay error. El segundo parámetro: un objeto que contiene un campo value con el `array` de resultados.

Teniendo claro esto, veamos la llamada a la función `doTask()`, donde le pasamos esa función `callback` e implementamos el funcionamiento, que en nuestro caso serán dos simples `console.error()` y `console.log()`:

```js
doTask(10, function(err, result) {
  if (err) {
    console.error("Se ha sacado un ", err.message);
    return;
  }
  console.log("Tiradas correctas: ", result.value);
});
```

Esto es una forma clásica donde utilizamos una función callback para gestionar la asincronia y facilitar la reutilización, pudiendo reutilizar la función con la lógica, aplicando diferentes funciones callback según nos interese.

### Desventajas de utilizar callbacks

A pesar de ser una forma flexible y potente de controlar la asincronía, que permite realizar múltiples posibilidades, las funciones `callbacks` tienen ciertas **desventajas** evidentes. En primer lugar, el código creado con las funciones es algo caótico y algo feo. Por ejemplo, el tener que pasar un `null` por parámetros en algunas funciones, no es demasiado elegante.

Pero sobre todo, uno de los problemas evidentes viene a la hora de tener que gestionar la asincronía varias veces en una misma función, donde al introducir varias funciones con callbacks en su interior, conseguimos una estructura anidada similar a la siguiente:

```js
function doStep1(init, callback) {
  const result = init + 1;
  callback(result);
}

function doStep2(init, callback) {
  const result = init + 2;
  callback(result);
}

function doStep3(init, callback) {
  const result = init + 3;
  callback(result);
}

function doOperation() {
  doStep1(0, (result1) => {
    doStep2(result1, (result2) => {
      doStep3(result2, (result3) => {
        console.log(`resultado: ${result3}`);
      });
    });
  });
}

doOperation();

```

La forma triangular que produce es conocida como **Callback Hell** o **Pyramid of Doom**, debido a su forma, resultando un código muy poco elegante que se puede complicar demasiado de cara a la legibilidad. Es cuando entran en juego las **promesas**.