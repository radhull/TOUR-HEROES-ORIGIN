> Capítulo anterior: [Maestro detalle](./2-master-detail.md)

# Múltiples componentes

## Creando el componente del detalle o de edición

Vamos a crear un nuevo componente, creamos un nuevo fichero `hero-detail.component.ts` en la carpeta `app` y crearemos `HeroDetailComponent` como sigue:

````typescript
import { Component, Input } from '@angular/core';

@Component({
  selector: 'my-hero-detail',
})
export class HeroDetailComponent {
}
````

Hemos importado los decoradores `Component` e `Input` por que los vamos a necesitar.

Hemos creado los metadatos del componente y hemos especificado `my-hero-detail` como selector del componente y hemos exportado este componente para hacerlo visible desde otros módulos JavaScript.

Cuando terminemos este componente lo usaremos en `AppComponent` escribiendo en la plantilla el elemento `<my-hero-detail>`.

## Antes de seguir hay que actualizar el módulo angular

Ahora tenemos un componente nuevo, para poder usarlo desde `AppComponent` este nuevo componente debe estar en la lista de declaraciones de nuestro `AppModule`, importamos el módulo JavaScript:

````typescript
import { HeroDetailComponent } from './hero-detail.component';
````

Y añadimos la declaración:

````typescript
@NgModule({
  imports: [
    BrowserModule,
    FormsModule
  ],
  declarations: [
    AppComponent,
    HeroDetailComponent // <<-- aquí!
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
````

## Volvemos al nuevo componente, creamos la plantilla

Nos traemos la plantilla de `AppComponent` y sustituimos `selectedHero` por `hero`, el resultado debe ser esto:

````typescript
template: `
  <div *ngIf="hero">
    <h2>{{hero.name}} details!</h2>
    <div><label>id: </label>{{hero.id}}</div>
    <div>
      <label>name: </label>
      <input [(ngModel)]="hero.name" placeholder="name"/>
    </div>
  </div>
`
````

## Añadir la propiedad para el heroe

Debemos añadir la propiedad `hero` que referenciamos en la plantilla:

````template
hero: Hero;
````

Pero ... ahora la clase Hero no está disponible, no podemos traernosla aquí por que `AppComponent` también la usa, así que haremos un nuevo módulo js para almacenar la clase, `hero.ts`

````typescript
export class Hero {
  id: number;
  name: string;
}
````

Ahora importamos dicha clase desde `AppComponent` y `hero-detail.component.ts`:

````typescript
import { Hero } from './hero';
````

## La propiedad hero como @Input

Al componente `HeroDetailComponent` le tenemos que decir que héroe debe mostrar, quien se lo va a decir? En nuestro caso será el componente padre `AppComponent`.

`AppComponent` sabe que heroe es el que hay que mostrar, lo tiene en la propiedad `selectedHero`, por lo tanto debemos enlazar esta propiedad del padre a la popriedad `hero` del hijo. Debemos hacer esto:

````html
<my-hero-detail [hero]="selectedHero"></my-hero-detail>
````

Para que esto funcione Angular nos obliga a marcar la propiedad `hero` del componente hijo como una propiedad de entrada, debemos marcarla con el decorador `@Input`:

````typescript
@Input()
hero: Hero;
````

## Actualizamos AppComponent

Ahora vamos a la plantilla de `AppComponent` y donde teniamos el HTML del detalle/edición debemos poner:

````html
<my-hero-detail [hero]="selectedHero"></my-hero-detail>
````

La plantilla debe quedar así:

````typescript
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
````

## Recordando como queda nuestro proyecto

La estructura:

````
tour-heroes
|-app
| |-app.component.ts
| |-app.module.ts
| |-hero.ts
| |-hero-detail.component.ts
| |-main.ts
|-node_modules ...
|-index.html
|-package.json
|-tsconfig.json
````

**app/hero-detail.component.ts**
````typescript
import { Component, Input } from '@angular/core';
import { Hero } from './hero';
@Component({
  selector: 'my-hero-detail',
  template: `
    <div *ngIf="hero">
      <h2>{{hero.name}} details!</h2>
      <div><label>id: </label>{{hero.id}}</div>
      <div>
        <label>name: </label>
        <input [(ngModel)]="hero.name" placeholder="name"/>
      </div>
    </div>
  `
})
export class HeroDetailComponent {
  @Input()
  hero: Hero;
}
````

**app/app.component.ts**
````typescript
import { Component } from '@angular/core';
import { Hero } from './hero';
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

**app/hero.ts**
````typescript
export class Hero {
  id: number;
  name: string;
}
````

**app/app.module.ts**
````typescript
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule }   from '@angular/forms';
import { AppComponent }  from './app.component';
import { HeroDetailComponent } from './hero-detail.component';
@NgModule({
  imports: [
    BrowserModule,
    FormsModule
  ],
  declarations: [
    AppComponent,
    HeroDetailComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
````

## Resumen de lo realizado

A ver...

* Hemos creado un componente reusable.
* Hemos aprendido como un componente acepta un parametro de entrada.
* Hemos aprendido a declarar las directivas/componentes que vamos a utilizar en nuestra aplicación, en el array `declarations` de los metadatos de nuestro NgModule.
* Hemos aprendido a hacer binding entre componentes, a enlazar el componente padre con el hijo.

## Lo próximo

Nuestra aplicación debería ser más reusable usando algún componente más.

Seguimos teniendo los datos falseados (mocking) dentro del `AppComponent`. Deberíamos refactorizar el acceso a los datos separando este en un servicio y usarlo desde los componentes.

> Siguiente capítulo: [Servicios](./4-services.md)