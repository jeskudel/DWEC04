# DWEC UT04: Programación asíncrona y obtención de datos.

## El moderno `fetch()`

`fetch` es el nombre de una nueva API para Javascript con la cuál podemos realizar peticiones HTTP asíncronas utilizando **promesas** y de forma que el código sea un poco más sencillo y menos verbose. La forma de realizar una petición es muy sencilla, básicamente se trata de llamar a `fetch` y pasarle por parámetro la URL de la petición a realizar.

```js
let promesa = fetch(url, [options])
```

> Una `Promise` (promesa en castellano) es un objeto que representa la terminación o el fracaso de una operación asíncrona. Esencialmente, una promesa es un objeto devuelto al cual se adjuntan funciones `callback`, en lugar de pasar `callbacks` a una función. Lo veremos en un [tema]() mas adelante.

`fetch()` devolverá una `promise` que será **aceptada** cuando reciba una respuesta y sólo será **rechazada** si hay un fallo de red o si por alguna razón no se pudo completar la petición.

El modo más habitual de manejar las promesas es utilizando `.then()`, aunque también se puede utilizar `async/await`. Esto se suele reescribir de la siguiente forma, que queda mucho más simple y evitamos constantes o variables temporales de un solo uso.

```js
fetch("/robots.txt")
    .then(function(response) {
    /** Código que procesa la respuesta **/
    });
```

Al método `.then()` se le pasa una función callback donde su parámetro **response** es el objeto de respuesta de la petición que hemos realizado. En su interior realizaremos la lógica que queramos hacer con la respuesta a nuestra petición.

### Opciones de `fetch()`

A la función `fetch()`, al margen de la url a la que hacemos petición, se le puede pasar un segundo parámetro de opciones de forma opcional, un `object` con opciones de la petición HTTP.

| Campo | Descripción |
|----------|----------|
| `method` | Método HTTP de la petición. Por defecto, `GET`. Otras opciones: `HEAD`, `POST`, etc... | 
| `headers` | Cabeceras HTTP. Por defecto, `{}`. | 
| `body` | Cuerpo de la petición HTTP. Puede ser de varios tipos: `String`, `FormData`, `Blob`, etc... | 
| `credentials` | Modo de credenciales. Por defecto, `omit`. Otras opciones: `same-origin` e `include`. | 

```js
const jsonData = {
    nombre: "Manolo",
    apellidos: "Rodriguez Jimenez"
}
const options = {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify(jsonData)
};

fetch("https://jsonplaceholder.typicode.com/posts", options)
    .then(response => response.text())  // procesamos la respuesta como si fuera texto plano
    .then(data => {
        console.log(data)
    });
```

## Manipulando la respuesta

Por lo general, obtener una respuesta es un proceso de dos pasos.

* **Primero**, la promesa devuelta por fetch, resuelve la respuesta con un objeto de la clase incorporada `response` tan pronto como el servidor responde con los encabezados de la petición. En este paso, podemos chequear el status HTTP para poder ver si nuestra petición ha sido exitosa o no, y chequear los encabezados, pero aún no disponemos del cuerpo de la misma.
La promesa es **rechazada** si `fetch` no ha podido establecer la petición HTTP, por ejemplo, por problemas de red o si el sitio especificado en la petición no existe. Estados HTTP anormales, como el `404` o `500` no generan errores.

* **Segundo**, para obtener el cuerpo de la respuesta, necesitamos utilizar un método adicional. `response` provee múltiples métodos basados en promesas para acceder al cuerpo de la respuesta en distintos formatos:

    * **response.text()** – lee y devuelve la respuesta en formato texto,
    * **response.json()** – convierte la respuesta como un JSON,
    * **response.formData()** – devuelve la respuesta como un objeto FormData
    * **response.blob()** – devuelve la respuesta como Blob (datos binarios tipados),
    * **response.arrayBuffer()** – devuelve la respuesta como un objeto ArrayBuffer (representación binaria de datos de bajo nivel),

```js
// Utilizando .then() con fetch()
fetch('https://randomuser.me/api/')
    .then(response => response.json())
    .then(data => console.log(data.results[0]));
```

Veamos como quedaria un ejemplo algo mas completo haciendo alguna comprobación más.

```js
fetch("/robots.txt")
  .then(response => {
    if (response.ok)
      return response.text();
    throw new Error(response.status);
  })
  .then(data => {
    console.log("Datos: " + data);
  })
  .catch(err => {
    console.error("ERROR: ", err.message)
  });
```

* Comprobamos que la petición es correcta con `response.ok`
* Utilizamos `response.text()` para procesarla
* En el caso de producirse algún **error**, lanzamos excepción con el código de error
* Procesamos los datos y los mostramos en la consola
* En el caso de que la sea **rechazada**, capturamos el error con el `catch`
* Si ocurre un error 404, 500 o similar, lanzamos error con `throw` para capturarlo en el `catch`

## Cabeceras en `fetch()`

Los encabezados de respuesta están disponibles como un objeto de tipo `Map` dentro del response.headers. No es exactamente un `Map`, pero posee métodos similares para obtener de manera individual encabezados por nombre o si quisiéramos recorrerlos como un objeto.

| Método | Descripción |
|----------|----------|
| `.has(name)` | Comprueba si la cabecera `name` está definida. |
| `.get(name)` | Obtiene el valor de la cabecera `name`. |
| `.set(name, value)` | Establece o modifica el valor value a la cabecera `name`. |
| `.append(name, value)` | Añade un nuevo valor value a la cabecera `name`. |
| `.delete(name)` | Elimina la cabecera `name`. |

Como muchos otros objetos iterables, podemos utilizar los métodos .`entries()`, `.keys()` y/o `.values()` para recorrerlos.

```js
const headers = new Headers();
headers.set("Content-Type", "application/json");
headers.set("Content-Encoding", "br");

for ([key, value] of headers.entries()) {
  console.log("Clave: ", key, "valor: ", value);
}
```

Para peticiones con pocas cabeceras no hay mayor problema, pero en peticiones más complejas utilizar `Headers` es una buena práctica.

```js
fetch('https://randomuser.me/api/')
    .then(response => {
        // obtenemos un encabezado
        alert(response.headers.get('Content-Type'));

        // iteramos todos los encabezados
        for (let [key, value] of response.headers) {
            console.log(`${key} = ${value}`);
        }
    });
```