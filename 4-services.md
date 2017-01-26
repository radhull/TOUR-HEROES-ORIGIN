> Capítulo anterior: [Multiples componentes](./3-multiple-components.md)

# Servicios

## Creando el servicio de héroes

Ahora mismo estamos obteniendo la lista de heroes de un array en `AppComponent`, pero pronto tendremos también un dashboard, un panel de bienvenida, donde tendremos nuestro héroes favoritos y entonces necesitaremos consultar la lista de héroes.

Esto nos obliga a tener un servicio de héroes para compartir el acceso a los héroes entre los componentes.

### Creando HeroService

Creamos en la carpeta `app` un fichero llamado `hero.service.ts`.

Creamos este contenido:

````typescript
import { Injectable } from '@angular/core';

@Injectable()
export class HeroService {
}
````

### Servicios injectables

Fijate que hemos importado de angular el decorador `Injectable` y se lo hemos puesto a la clase con `@Injectable()`

> No te olvides nunca de poner los parentesis en el decorador, todos los decoradores de angular se usan con parentesis.

Esto va a hacer que Angular marque esta clase como inyectable en otros componentes o servicios.

### Obteniendo los héroes

Añadimos el método `getHeroes`:

````typescript
@Injectable()
export class HeroService {
  getHeroes(): void {} // stub
}
````

De momento lo dejamos así sin implementación. El componente que consuma el servicio no tiene por que saber de donde saca el servicio la información, la información puede ser obtenida desde un web service o desde el local storage o de donde sea, esto debe ser transparente para los consumidores del servicio.

### Falseando la lista de heroes (mocking).

De momento no vamos a leer los datos de ningún servidor, lo que vamos a crear es un fichero llamado `mock-heroes.ts`. Ahora vamos a cortar en `AppComponent` la lista de héroes y la vamos a pegar en el nuevo fichero. Quedará así:

````typescript
import { Hero } from './hero';

export const HEROES: Hero[] = [
  {id: 11, name: 'Mr. Nice'},
  {id: 12, name: 'Narco'},
  {id: 13, name: 'Bombasto'},
  {id: 14, name: 'Celeritas'},
  {id: 15, name: 'Magneta'},
  {id: 16, name: 'RubberMan'},
  {id: 17, name: 'Dynama'},
  {id: 18, name: 'Dr IQ'},
  {id: 19, name: 'Magma'},
  {id: 20, name: 'Tornado'}
];
````

Fijate que tambien hemos importado la clase `Hero` del fichero `hero.ts`.

En al AppComponent, ahora, la propiedad `heroes` quedará sin inicializar:

````typescript
heroes: Hero[];
````

Ahora veremos como hacemos llegar los héroes.

### Devolviendo los héroes desde nuestro servicio

Volviendo al `HeroService` debemos importar `HEROES` del fichero `mock-heroes.ts` y devolverlos en el método `getHeroes`:

````typescript
import { Injectable } from '@angular/core';

import { Hero } from './hero';
import { HEROES } from './mock-heroes';

@Injectable()
export class HeroService {
  getHeroes(): Hero[] {
    return HEROES;
  }
}
````

### Usando nuestro nuevo servicio

Vamos a nuestro `AppComponent`, aquí vamos a importar `HeroService`:

````typescript
import { HeroService } from './hero.service';
````

En vez de crear el servicio con `new` lo vamos a injectar.  
Vamos a crear un constructor en nuestro `AppComponent` de este modo:

````typescript
constructor(private heroService: HeroService) { }
````

Ahora si vemos la ejecución nos va a dar un error:

````
EXCEPTION: No provider for HeroService! (AppComponent -> HeroService)
````

¿Que es lo que pasa?

Sencillo, Angular necesita que `HeroService` esté declarado en los providers de los metadatos de nuestro NgModule.  
Debemos añadir `HeroService` a la propiedad `providers` de nuestro NgModule, para ello importaremos, en `app.module.ts`, el servicio así:

````typescript
import {HeroServic} from "hero.service.ts";
````

Y lo añadimos a `providers`:

````typescript
providers: [HeroService]
````

Ahora sí!

### getHeroes en AppComponent

Necesitamos hacer esto:

````typescript
this.heroes = this.heroService.getHeroes();
````

Pero en vez de hacerlo en el constructor vamos a crear un método que lo haga, de esta forma:

````typescript
getHeroes(): void {
    this.heroes = this.heroService.getHeroes();
}
````

De esta forma podremos llamar a esta función desde distintos sitios.

### ngOnInit - Ciclo de vida del componente - Lifecycle Hook

¿Desde donde, o cuando, vamos a llamar al metodo `getHeroes` de `AppComponent`??

No vamos a hacerlo en el constructor, lo vamos a hacer en un método especial que es llamado por Angular en los componentes cuando el componente está correctamente iniciado. 

Este método se llama `ngOnInit`, hay otros métodos en el Lifecycle de los componentes de Angular que iremos viendo más adelante.

El método queda así:

````typescript
ngOnInit(): void {
    this.getHeroes();
}
````

Angular llamará al metodo cuando el componente esté inicializado.

Para hacerlo mejor, vamos a hacer que `AppComponent` implemente el interfaz `OnInit`, lo haremos en dos pasos. Importamos el interfaz:

```` typescript
import { OnInit } from '@angular/core';
````

Y marcamos la clase diciendo que implementa dicha interfaz:

````typescript
export class AppComponent implements OnInit {
````


## Servicio asincrono y promesas

Ahora mismo estamos cargando los héroes de forma sincrona:

````typescript
this.heroes = this.heroService.getHeroes();
````

Pero normalmente los datos se cargan de forma asincrona puesto que suelen requerir llamadas a servidores o a recursos externos.

Así que vamos a hacer que nuestro servicio devuelva una promesa, vamos a `hero-service.ts` y cambiamos el metodo `getHeroes` para dejarlo así:

````typescript
getHeroes(): Promise<Hero[]> {
  return Promise.resolve(HEROES);
}
````

Como los datos que tenemos son falsos (mocks), tenemos los datos de forma instantanea pero creamos un promesa que es resuelta inmediatamente con los datos falsos.

### Leer la promesa

Ahora como nuestro servicio devuelve una promesa, debemos esperar a que esta esté resuelta para obtener el valor. Lo hacemos así:

````typescript
getHeroes(): void {
  this.heroService.getHeroes().then(heroes => this.heroes = heroes);
}
````

Así quedan los ficheros que más hemos tocado:

**hero.service.ts**
````typescript
import { Injectable } from '@angular/core';

import { Hero } from './hero';
import { HEROES } from './mock-heroes';

@Injectable()
export class HeroService {
  getHeroes(): Promise<Hero[]> {
    return Promise.resolve(HEROES);
  }
}
````

**mock-heroes.ts**
````typescript
import { Hero } from './hero';
export const HEROES: Hero[] = [
  {id: 11, name: 'Mr. Nice'},
  {id: 12, name: 'Narco'},
  {id: 13, name: 'Bombasto'},
  {id: 14, name: 'Celeritas'},
  {id: 15, name: 'Magneta'},
  {id: 16, name: 'RubberMan'},
  {id: 17, name: 'Dynama'},
  {id: 18, name: 'Dr IQ'},
  {id: 19, name: 'Magma'},
  {id: 20, name: 'Tornado'}
];
````

**app.component.ts**
````typescript
import { Component, OnInit } from '@angular/core';
import { Hero } from './hero';
import { HeroService } from './hero.service';
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
    <my-hero-detail [hero]="selectedHero"></my-hero-detail>
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
  `],
  providers: [HeroService]
})
export class AppComponent implements OnInit {
  title = 'Tour of Heroes';
  heroes: Hero[];
  selectedHero: Hero;
  constructor(private heroService: HeroService) { }
  getHeroes(): void {
    this.heroService.getHeroes().then(heroes => this.heroes = heroes);
  }
  ngOnInit(): void {
    this.getHeroes();
  }
  onSelect(hero: Hero): void {
    this.selectedHero = hero;
  }
}

````

## Apendice: Hacer que vaya más despacio

Para simular que el servicio está llamando a un servidor vamos a simular una latencia de dos segundos.
Vamos a añadir un método a nuestro servicio `HeroService` llamado `getHeroesSlowly`:

````typescript
getHeroesSlowly(): Promise<Hero[]> {
  return new Promise(resolve => {
    // Simulate server latency with 2 second delay
    setTimeout(() => resolve(this.getHeroes()), 2000);
  });
}
````

Ahora en nuestro `AppComponent` cambiamos a llamar a este método y podrás ver que ahora tarda un poco más simulando una llamada al servidor. Esto lo hemos conseguido gracias a usar las promesas.

## Resumen de lo realizado

* Hemos creado una clase servicio que puede ser compartida con otros componentes.
* Hemos usado `ngOnInit` y nos hemos introducido en los ganchos de ciclo de vida de los componentes Angular.
* Hemos definido `HeroService` como un provider en nuestro NgModule para poder ser inyectado en `AppComponent`.
* Hemos creado un archivo con datos provisionales al que solo accede el servicio.
* Hemos diseñado nuestro servicio para que devuelva promesas y así podrá ser asincrono en un futuro.


## Lo próximo

Nuestra aplicación está siendo cada vez más reusable gracias a los componentes compartidos y los servicios. Ahora crearemos una pantalla de bienvenida y añadiremos un menú con enlaces que cambiaran la ruta del navegador para navegar entre distintas vistas.

> Siguiente capítulo: [Routing](./5-routing.md)