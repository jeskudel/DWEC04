# DWEC UT04: Programación asíncrona y obtención de datos.

## Libreria JQuery

### Que es JQuery

**jQuery** podría resumirse en una navaja suiza para simplificar el modo en que accedemos al *DOM* de la web usando JavaScript. jQuery es una librería desarrollada en 2006 por John Resig que permite añadir una capa de interacción AJAX entre la web y las aplicaciones que desarrollemos controlando eventos, creando animaciones y diferentes efectos para enriquecer la experiencia de usuario.  

Cuenta con un diseño que facilita la navegación por un documento y seleccionar elementos DOM proporcionando a los desarrolladores de aplicaciones web complementos que agilizan el desarrollo de proyectos. Esto permite a los desarrolladores centrarse en lo importante y crear abstracciones para interacción y animación de bajo nivel, efectos avanzados y widgets temáticos de alto nivel sin invertir tiempo en desarrollar complejos algoritmos y métodos que los controlen desde cero y generando menos código que las aplicaciones hechas con JS puro. Por ese motivo jQuery es muy popular y podemos verlo en muchas páginas web. 

<p align="center"> 
<img src="./img/jquery-logo.png" width="60%" height="60%" style="display: block; margin: 0 auto" />
</p>

### Caracteristicas de JQuery

En este punto las ventajas de jQuery son claras. Nos permite realizar consultas sobre la estructura del DOM de la web y realizar personalizaciones a medida como aplicar diferentes estilos y efectos en función de los eventos que definamos. Todo esto se logra mediante una sintaxis simplificada y fácil de entender que permite un desarrollo ágil en nuestros proyectos. 

Para entender cómo funciona jQuery debemos atender a sus características. 

```js
jQuery('h2').css('color', ‘blue’’);
// '$' es un alias de 'jQuery', por lo que se suele usar así
$('h2').css('color', 'red');
```
Los métodos en jQuery pueden realizar consultas mediante **iteración implícita**. Es decir, buscando múltiples coincidencias y aplicando modificaciones para cada elemento en una única instrucción, Por ejemplo:

* $('h2') Nos devuelve el conjunto de los títulos nivel 2 (h2), pero si por ejemplo quisiéramos cambiar el color a un gris oscuro (#333), esto lo podríamos hacer de la siguiente manera:

```js
$.each($('h2'), function() {
    $(this).css("color", "#333");
}); 
```

> Para obtener mas información acerca del uso básico de JQuery podeis consultar el siguiente [enlace](https://www.arkaitzgarro.com/jquery/capitulo-3.html).

> JQuery tambien nos facilita la gestión de eventos con sus metodos mas avreviados, podeis consultar mas información acerca de eventos en JQuery en el siguiente [enlace](https://www.arkaitzgarro.com/jquery/capitulo-5.html).

> JQuery tambien nos facilita la aplicación de efectos a elementos del HTML con sus metodos mas avreviados, podeis consultar mas información acerca de eventos en JQuery en el siguiente [enlace](https://www.arkaitzgarro.com/jquery/capitulo-6.html).

> #### *Tener en cuenta que ...*
> JQuery es una libreria desarrolada para funcionar encima del motor de JS, es por eso que para poder utilizarlo en nuestros scripts tendremos que añadir la libreria en si. Se puede descargar a la máquina local o enlazarlo desde algun CDN en internet. Podeis consultar este [enlace](https://desarrolloweb.com/articulos/pasos-para-ejecutar-jquery.html) para saber más.

## Obtención de información con JQuery

A través de varios métodos, jQuery provee soporte para Ajax, permitiendo abstraer las diferencias que pueden existir entre navegadores. Los métodos en cuestión son `$.ajax()` `$.get()`, `$.getScript()`, `$.getJSON()`, `$.post()` y `$().load()`.

### .ajax()

El método `$.ajax` es configurado a través de un objeto, el cual contiene todas las instrucciones que necesita jQuery para completar la petición. Dicho método es particularmente útil debido a que ofrece la posibilidad de especificar acciones en caso que la petición haya fallado o no. Además, al estar configurado a través de un objeto, es posible definir sus propiedades de forma separada, haciendo que sea más fácil la reutilización del código. 

```js
$.ajax({
    // la URL para la petición
    url : 'post.php',
    // la información a enviar
    data : { id : 123 },
    // especifica si será una petición POST o GET
    type : 'GET',
    // el tipo de información que se espera de respuesta
    dataType : 'json',
    // código a ejecutar si la petición es satisfactoria;
    success : function(json) {
        $('<h1/>').text(json.title).appendTo('body');
        $('<div class="content"/>')
            .html(json.html).appendTo('body');
    },
    // código a ejecutar si la petición falla;
    // el objeto jqXHR (extensión de XMLHttpRequest), un texto con el estatus
    // de la petición y un texto con la descripción del error que haya dado el servidor
    error : function(jqXHR, status, error) {
        alert('Disculpe, existió un problema');
    },
    // código a ejecutar sin importar si la petición falló o no
    complete : function(jqXHR, status) {
        alert('Petición realizada');
    }
});
```

> Podeis consultar la documentación oficial de JQuery para consultar más cosas y ver diferentes ejemplos que acompañan en el siguiente enlace [enlace](https://api.jquery.com/jQuery.ajax/).

### Otors métodos más especificos

En caso que no quiera utilizar el método `$.ajax`, y no necesite los controladores de errores, existen otros métodos más convenientes para realizar peticiones Ajax (aunque estos están basados el método `$.ajax` con valores pre-establecidos de configuración).

Los métodos que provee la librería son:

* $.get - Realiza una petición GET a una URL provista. [Enlace](https://api.jquery.com/jQuery.post/)
* $.post - Realiza una petición POST a una URL provista. [Enlace](https://api.jquery.com/jQuery.get/)
* $.getScript - Añade un script a la página. [Enlace](hhttps://api.jquery.com/jQuery.getScript/)
* $.getJSON - Realiza una petición GET a una URL provista y espera que un dato JSON sea devuelto. [Enlace](https://api.jquery.com/jQuery.getJSON/)

Aqui teneis algunos ejemplos sencillos.

```js
//Enviar datos con POST
$.post( "test.php", { name: "John", time: "2pm" } );
//Enviar los datos de campos de un formulario
$.post( "test.php", $( "#testform" ).serialize() );
//Enviar datos y realizar algo con los datos obtenidos de vuelta
$.post( "test.php", { name: "John", time: "2pm" })
  .done(function( data ) {
    alert( "Data Loaded: " + data );
  });
```
```js
//Petición simple con GET
$.get( "test.php" );
//Petición enviando datos (en la URL)
$.get( "test.php", { name: "John", time: "2pm" } );
//Enviar datos y realizar algo con los datos obtenidos de vuelta
$.get( "test.cgi", { name: "John", time: "2pm" } )
  .done(function( data ) {
    alert( "Data Loaded: " + data );
  });
```
```js
//Obtención y visualización de datos
$.getJSON( "test.js", function( json ) {
  console.log( "JSON Data: " + json.users[ 3 ].name );
 });

//Obtención y visualización de datos contemplando posibles errores
$.getJSON( "test.js", { name: "John", time: "2pm" } )
  .done(function( json ) {
    console.log( "JSON Data: " + json.users[ 3 ].name );
  })
  .fail(function( jqxhr, textStatus, error ) {
    var err = textStatus + ", " + error;
    console.log( "Request Failed: " + err );
});
```