> Capítulo anterior: [Editor de heroes](./1-hero-editor.md)

# Maestro detalle

¡¡Necesitamos más héroes!!

## Mostrando nuestros héroes

### Creamos la lista de héroes

En el mismo `app.component.ts` añadimos un array con nuestros héroes:

````typescript
const HEROES: Hero[] = [
  { id: 11, name: 'Mr. Nice' },
  { id: 12, name: 'Narco' },
  { id: 13, name: 'Bombasto' },
  { id: 14, name: 'Celeritas' },
  { id: 15, name: 'Magneta' },
  { id: 16, name: 'RubberMan' },
  { id: 17, name: 'Dynama' },
  { id: 18, name: 'Dr IQ' },
  { id: 19, name: 'Magma' },
  { id: 20, name: 'Tornado' },
  { id: 21, name: 'Zumbadin' }
];
````

El array es un array de objetos `Hero`, lo ideal sería cargar esta lista desde un servicio web, pero vamos a ir poco a poco.

### Exponiendo los héroes

Vamos a crearnos una propiedad en nuestro componente para poder referenciar el array desde nuestra plantilla:

````typescript
heroes = HEROES;
````

### Mostrando los héroes en nuestra plantilla

Crearemos una lista en la plantilla para mostrar los héroes, copiaremos este trozo de HTML con el título y los detalles:

````html
<h2>My Heroes</h2>
<ul class="heroes">
  <li>
    <!-- cada héroe va aquí -->
  </li>
</ul>
````

Ahora ya tenemos la plantilla preparada para ser rellenada con nuestros héroes.

### Recorriendo los héroes con `ngFor`

Ahora lo que queremos es hacer el binding de nuestro array `heroes` en la plantilla, iterar sobre cada heroe y mostrar sus detalles individualmente. Necesitamos la ayuda de Angular para hacer esto.

Primero modificaremos nuestra etiqueta `<li>` añadiendole la directiva `*ngFor` de este modo:

````html
<li *ngFor="let hero of heroes">
````

> El prefijo `*` de `ngFor` está indicando que el elemento `<li>` y su contenido constitullen una plantilla maestra (master template).  
> La directiva `ngFor` itera sobre `heroes`, que es el array que tenemos como propiedad en nuestro componente, y reproduce una instancia de esta plantilla maestra por cada elemento del array.  
> El texto entre comillas asignado a `ngFor` significa *"coge cada héroe en el array `heroes`, guardalo en una variable local llamada `hero` y hazla disponible en su correspondiente instancia de la plantilla maestra"*.  
> La palabra clave `let` antes de `hero`, identifica `hero` como una variable de entrada para la plantilla. Nosotros podremos referenciar esta variable entro de nuestra plantilla para acceder a las propiedades del héroe.

Ahora insertaremos dentro del `<li>` instrucciones que usan la variable de la plantilla, `hero`, para mostrar las propiedades de nuestros heroes.

````typescript
<li *ngFor="let hero of heroes">
  <span class="badge">{{hero.id}}</span> {{hero.name}}
</li>
````

Cuando el browser refresque, veremos la lista de nuestros heroes.

### Dando estilo a nuestros héroes

Para que se vea un poco más bonito añadiremos CSS a la propiedad `styles` de los metadatos de nuestro componente:

````typescript
styles: [`
  .selected {
    background-color: #CFD8DC !important;
    color: white;
  }
  .heroes {
    margin: 0 0 2em 0;
    list-style-type: none;
    padding: 0;
    width: 15em;
  }
  .heroes li {
    cursor: pointer;
    position: relative;
    left: 0;
    background-color: #EEE;
    margin: .5em;
    padding: .3em 0;
    height: 1.6em;
    border-radius: 4px;
  }
  .heroes li.selected:hover {
    background-color: #BBD8DC !important;
    color: white;
  }
  .heroes li:hover {
    color: #607D8B;
    background-color: #DDD;
    left: .1em;
  }
  .heroes .text {
    position: relative;
    top: -3px;
  }
  .heroes .badge {
    display: inline-block;
    font-size: small;
    color: white;
    padding: 0.8em 0.7em 0 0.7em;
    background-color: #607D8B;
    line-height: 1em;
    position: relative;
    left: -1px;
    top: -4px;
    height: 1.8em;
    margin-right: .8em;
    border-radius: 4px 0 0 4px;
  }
`]
````

Fijate que hemos vuelto a usar las template string para poder poner cadenas en multilinea.

Bien, la plantilla de nuestro componente queda como algo así:

````html
<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
````

## Seleccionando un héroe

Ahora tenemos una lista de heroes y tambien un solo héroe mostrandose en nuestra aplicación. La lista de heroes y el héroe mostrado en la edición no están conectados de ninguna forma. Queremos que el usuario pueda seleccionar un heroe de la lista y que pueda modificarlo. Esto es lo que se conoce como maestro detalle, en este caso el maestro es la lista de heroes y el detalle la zona de edición.

Vamos a conectar ambas cosas a través de una propiedad que vamos a llamar `selectedHero`.

### Evento click

Lo primero es modificar el tag `<li>` de la lista de heroes para añadirle el evento click:

````html
<li *ngFor="let hero of heroes" (click)="onSelect(hero)">
  <span class="badge">{{hero.id}}</span> {{hero.name}}
</li>
````

Fijandonos en el event-binding: `(click)="onSelect(hero)"`

Los parentesis identifican el evento que queremos capturar y la expressión de la derecha llama a un método de nuestro componente, en concreto al metodo `onSelect()`, pero lo llamamos pasandole la variable de la plantilla que se corresponde con el héroe que hemos representado. Este variable es la misma que definimos en el `ngFor`.

### El manejador del evento

Debemos añadir el método `onSelect` a nuestro componente. ¿Que debe de hacer este metodo? Este método debería establecer el heroe actual con el que le llega por parametros, pero nuestro componente no tiene forma de hacerlo, así que lo primero que haremos será añadir una propiedad que represente el heroe seleccionado.

### Exponiendo el heroe seleccionado

Facil, debemos crear una propiedad en nuestro componente que llamaremos `selectedHero` de este modo:

````typescript
selectedHero: Hero;
````

En principio la propiedad no tendrá valor, pero la establecemos en el método `onSelect`. Ahora si:

````typescript
onSelect(hero: Hero): void {
  this.selectedHero = hero;
}
````

Ahora debemos enlazar este héroe seleccionado a la zona de los detalles, lo haremos cambiando `hero` por `selectedHero`:

````html
<h2>{{selectedHero.name}} details!</h2>
<div><label>id: </label>{{selectedHero.id}}</div>
<div>
    <label>name: </label>
    <input [(ngModel)]="selectedHero.name" placeholder="name"/>
</div>
````

### Ocultar la edición con `ngIf`

Conforme estamos ahora, si recargamos la página obtendremos un error:

````
EXCEPTION: TypeError: Cannot read property 'name' of undefined in [null]
````

Recuerda que en la plantilla estamos mostrando `selectedHero.name`. Al cargar la aplicación falla por que `selectedHero` no tiene valor inicialmente.  
Lo que tenemos que lograr es no cargar en el DOM el detalle hasta que haya un héroe seleccionado, para esto usaremos otra directiva de Angular llamada `ngIf`.

Envolveremos nuestra edición con un `<div>` y la condicionaremos:

````html
<div *ngIf="selectedHero">
  <h2>{{selectedHero.name}} details!</h2>
  <div><label>id: </label>{{selectedHero.id}}</div>
  <div>
    <label>name: </label>
    <input [(ngModel)]="selectedHero.name" placeholder="name"/>
  </div>
</div>
````

Cuando `selectedHero` no está asignado, la directiva `ngIf` elimina el HTML, de la edición, del DOM. Entonces no hay componentes de edición, no hay binding y no tienes que preocuparte.  
Cuando el usuario pincha un héroe, `selectedHero` coge un valor y `ngIf` vuelve a poner el HTML en el DOM y entonces se evaluan los bindings.

> `ngIf` y `ngFor` son llamadas directivas estructurales por que modifican el DOM.

Ahora si, refresca la página y funciona bien.

### Dando estilos a la selección

Ya podemos seleccionar un heroe de la lista, pero para que la aplicación sea más agradable estaría bien resaltar el héroe que seleccionamos.

Para ello vamos a asignar la clase `selected` al `<li>` que contiene el heroe seleccionado, Angular nos ayuda a esto por que permite hacer binding de clases de estilo en nuestros elementos, pongamos este binding en el elemento `<li>`.

````typescript
[class.selected]="hero === selectedHero"
````

Fijate como `class.selected` esta envuelto con corchetes `[]` con esto le decimos que el valor que debe pasar es la evaluación de la expresión que asignamos.

Ahora el `<li>` queda así:

````html
<li *ngFor="let hero of heroes"
  [class.selected]="hero === selectedHero"
  (click)="onSelect(hero)">
  <span class="badge">{{hero.id}}</span> {{hero.name}}
</li>
````

Al refrescar nuestra aplicación y seleccionar un héroe vemos que el estilo cambia y resalta el héroe seleccionado.

Nuestro `app.component.ts` queda así:

````typescript
import { Component } from '@angular/core';
export class Hero {
  id: number;
  name: string;
}
const HEROES: Hero[] = [
  { id: 11, name: 'Mr. Nice' },
  { id: 12, name: 'Narco' },
  { id: 13, name: 'Bombasto' },
  { id: 14, name: 'Celeritas' },
  { id: 15, name: 'Magneta' },
  { id: 16, name: 'RubberMan' },
  { id: 17, name: 'Dynama' },
  { id: 18, name: 'Dr IQ' },
  { id: 19, name: 'Magma' },
  { id: 20, name: 'Tornado' }
];
@Component({
  selector: 'my-app',
  template: `
    <h1>{{title}}</h1>
    <h2>My Heroes</h2>
    <ul class="heroes">
      <li *ngFor="let hero of heroes"
        [class.selected]="hero === selectedHero"
        (click)="onSelect(hero)">
        <span class="badge">{{hero.id}}</span> {{hero.name}}
      </li>
    </ul>
    <div *ngIf="selectedHero">
      <h2>{{selectedHero.name}} details!</h2>
      <div><label>id: </label>{{selectedHero.id}}</div>
      <div>
        <label>name: </label>
        <input [(ngModel)]="selectedHero.name" placeholder="name"/>
      </div>
    </div>
  `,
  styles: [`
    .selected {
      background-color: #CFD8DC !important;
      color: white;
    }
    .heroes {
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      width: 15em;
    }
    .heroes li {
      cursor: pointer;
      position: relative;
      left: 0;
      background-color: #EEE;
      margin: .5em;
      padding: .3em 0;
      height: 1.6em;
      border-radius: 4px;
    }
    .heroes li.selected:hover {
      background-color: #BBD8DC !important;
      color: white;
    }
    .heroes li:hover {
      color: #607D8B;
      background-color: #DDD;
      left: .1em;
    }
    .heroes .text {
      position: relative;
      top: -3px;
    }
    .heroes .badge {
      display: inline-block;
      font-size: small;
      color: white;
      padding: 0.8em 0.7em 0 0.7em;
      background-color: #607D8B;
      line-height: 1em;
      position: relative;
      left: -1px;
      top: -4px;
      height: 1.8em;
      margin-right: .8em;
      border-radius: 4px 0 0 4px;
    }
  `]
})
export class AppComponent {
  title = 'Tour of Heroes';
  heroes = HEROES;
  selectedHero: Hero;
  onSelect(hero: Hero): void {
    this.selectedHero = hero;
  }
}
````

## Resumen de lo realizado

En este capítulo hemos realizado estas tareas:

1. Hemos mostrado una lista de heroes.
2. Hemos añadido la posibilidad de seleccionar un heroe y mostramos su detalle/edición.
3. Hemos aprendido a usar las directivas `ngIf` y `ngFor` que vienen con Angular.
4. Hemos aprendido a hacer binding a clases de estilo.

## Lo próximo
Nuestra aplicación ha crecido, pero está lejos aún de estar completa. No debemos poner nuestra aplicación completa en un mismo componente. Necesitamos partir nuestra app en distintos sub-componentes y enseñarlos a trabajar juntos.

> Siguiente capítulo: [Múltiples componentes](./3-multiple-components.md)