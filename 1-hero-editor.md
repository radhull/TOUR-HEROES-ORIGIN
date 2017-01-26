# Editor de heroes

## Mostrar nuestro héroe

Actualizamos el componente AppComponent añadiendole dos propiedades, `title` para el nombre de la aplicación y `hero` para el nombre de nuestro héroe que en principio será "Windstorm".

```` typescript 
export class AppComponent {
  title = 'Tour of Heroes';
  hero = 'Windstorm';
}
````

Ahora actualizamos la plantilla del decorador `@Component` para mostra el título y el nombre de nuestro héroe usando interpolación.

````html
template: '<h1>{{title}}</h1><h2>{{hero}} details!</h2>'
````

Al guardar se debe refrescar la información en el browser.

### El objeto `Hero`

Vamos a necesitar más información aparte del nombre del héroe así que creamos una clase `Hero` con las propiedades `id` y `name`.

````typescript
export class Hero {
  id: number;
  name: string;
}
````

Ahora debemos actualizar la propiedad `hero` de nuestro componente, ya no es un `string`, ahora es un objeto del tipo `Hero` con id igual a 1 y nombre "Windstorm".

````typescript
hero: Hero = {
  id: 1,
  name: 'Windstorm'
};
````

Por supuesto también actualizamos la plantilla para mostrar la propiedad `name` de nuestro objeto `hero`.

````typescript
template: '<h1>{{title}}</h1><h2>{{hero.name}} details!</h2>'
````

### Añadiendo un poco de HTML

Para hacerlo un poco mejor, vamos a añadir un div para cada propiedad y pondremos la etiqueta de cada campo, también vamos a usar las template strings para poder usar multilinea.

```` typescript
template:`
  <h1>{{title}}</h1>
  <h2>{{hero.name}} details!</h2>
  <div><label>id: </label>{{hero.id}}</div>
  <div><label>name: </label>{{hero.name}}</div>
  `
````

## Editando nuestro héroe

Para editar el nombre de nuestro héroe añadimos un `<input/>` como se muestra a continuación:

````typescript
template:`
  <h1>{{title}}</h1>
  <h2>{{hero.name}} details!</h2>
  <div><label>id: </label>{{hero.id}}</div>
  <div>
    <label>name: </label>
    <input value="{{hero.name}}" placeholder="name">
  </div>
  `
````

Si miramos el resultado podemos ver la caja de texto con el nombre del héroe, pero ¿que pasa cuando lo modificamos? Si prestamos atención al nombre del héroe bajo el título nos damos cuenta de que no cambia cuando cambiamos el contenido de la caja de texto.

Esto es por que hemos usado one-way binding en el input, hemos asignado a `value` el nombre del heroe pero no hemos habilitado ningún mecanismo para que se modifique el nombre en nuestro componente.

### Two-way binding

Lo que queremos es mostrar el nombre del héroe en nuestra caja de texto y que, cuando cambiemos dicho nombre, se actualice la propiedad en nuestro componente. En otras palabras lo que queremos es un binding en las dos direcciones, un two-way binding.

Antes de que podamos usar el two-way binding, necesitamos importar el módulo angular `FormsModule`. Tenemos que añadir al array de `imports` de nuestro módulo angular dicho módulo, el cual es importado de la librería `@angular/forms`. Este módulo exporta la directiva `ngModel` que debemos usar para hacer el two-way binding.

Nuestro módulo angular debe quedar así:

````typescript
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule }   from '@angular/forms'; // <--- importamos ...
import { AppComponent }  from './app.component';
@NgModule({
  imports: [
    BrowserModule,
    FormsModule // <<--- ... y lo referenciamos
  ],
  declarations: [
    AppComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
````

Ahora en la plantilla de nuestro componente debemos usar `ngModel` de esta forma:

````typescript
<input [(ngModel)]="hero.name" placeholder="name">
````

El browser se refresca y ahora vemos que al cambiar el nombre del héroe se actualiza el contenido de nuestro tag `<h2>`

## Resumen de lo realizado

Hagamos un resumen de lo que hemos realizado:

* Nuestra aplicación usa la interpolación, las dobles llaves `{{}}`, que es un tipo de one-way data binding, para mostrar el título de la aplicación y las propiedades de nuestro objeto de tipo `Hero`.
* Hemos usado las template strings, que es una caracteristica de ES6, para poder escribir texto en varias lineas y hacer nuestra plantilla más legible.
* Hemos podido mostrar y cambiar el nombre de nuestro héroe añadiendo two-way data binding al elemento `<input>` usando la directiva `ngModel` que Angular pone a nuestra disposición. La cual la hemos obtenido referenciando un módulo propio de angular que la exporta, `FormsModule`.
* Al hacer two-way binding hemos podido ver como los cambios en nuestro objeto se propagan a otros binding de la propiedad que hemos cambiado.

El fichero `app.component.ts` deberá quedar así:

````typescript
import { Component } from '@angular/core';
export class Hero {
  id: number;
  name: string;
}
@Component({
  selector: 'my-app',
  template: `
    <h1>{{title}}</h1>
    <h2>{{hero.name}} details!</h2>
    <div><label>id: </label>{{hero.id}}</div>
    <div>
      <label>name: </label>
      <input [(ngModel)]="hero.name" placeholder="name">
    </div>
    `
})
export class AppComponent {
  title = 'Tour of Heroes';
  hero: Hero = {
    id: 1,
    name: 'Windstorm'
  };
}
````

## Lo próximo
Nuestra aplicación solo muestra un héroe y nosotros realmente queremos mostrar una lista de heroes. Queremos también permitir al usuario seleccionar un heroe y mostrar su detalle. Lo siguiente que haremos es aprender a recuperar una lista de héroes, asociarla a la plantilla y permitir al usuario que seleccione uno de ellos.

> Siguiene capítulo: [Maestro/Detalle](./2-master-detail.md)