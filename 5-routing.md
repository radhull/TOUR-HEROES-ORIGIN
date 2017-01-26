> Capítulo anterior: [Servicios](./3-services.md)

# Routing

> Se conoce como *routing* a la técnica de cambiar las vistas en función de la URL del navegador. Routing es otro nombre para *Navegacion*. El *Router* es el encargado de navegar de una vista a otra.

Los nuevos requerimientos de nuestra aplicación son:

* Añadir una Dashboard (pantalla de bienvenida)
* Navegar entre la lista de los héroes y el dashboard.
* Cuando se hace click en un héroe se navegará a los detalles de del héroe.
* Podremos enviar por correo electrónico con la url de un heroe y desde él navegaremos directamente al detalle de un heroe concreto.

[Aquí](https://angular.io/resources/images/devguide/toh/nav-diagram.png) podemos ver como será la navegación en nuestra aplicación.

## Tareas a realizar

* Vamos a convertir `AppComponent` en un componente que tan solo muestre la vista que el roting indique.
* Moveremos todo lo relativo a héroes que tenemos en AppComponent a un nuevo componente que se llamará `HeroesComponent`.
* Añadiremos el routing.
* Crearemos un nuevo `DashboardComponent`.

## Separando nuestro AppComponent

Nuestro `AppComponent` debera mostrar un menú de navegación entre el Dashboard y la lista de héroes. Solo eso, no debe contener nada más.

### HeroesComponent

Vamos a renombrar::

* `app.component.ts` a `heroes.component.ts`
* La clase `AppComponent` a `HeroesComponent`
* Cambiamos el selector `my-app` to `my-heroes`

## Creamos el nuevo AppComponent

Seguimos estos pasos:

* Creamos el fichero `app/app.component.ts`
* Definimos y exportamos la una clase llamada `AppComponent`
* Añadimos el decorador `@Component` sobre la clase con el selector: `my-app`
* Movemos desde el `HeroesComponent` a `AppComponent`:
    * La propiedad `title`
    * El elemento `<h1>` de la plantilla, la cual contiene el binding del título.
* Añadimos el elemento `<my-heroes>` a la plantilla justo despues del título.
* Añadimos `HeroesComponent` al arra `declarations` de nuestro `AppModule` para que Angular reconozca el elemento `<my-heroes>`.

Quedará así:

**app.component.ts**
````typescript
import { Component } from '@angular/core';
@Component({
  selector: 'my-app',
  template: `
    <h1>{{title}}</h1>
    <my-heroes></my-heroes>
  `
})
export class AppComponent {
  title = 'Tour of Heroes';
}
````

**app.module.ts**
````typescript
import { NgModule }       from '@angular/core';
import { BrowserModule }  from '@angular/platform-browser';
import { FormsModule }    from '@angular/forms';
import { AppComponent }        from './app.component';
import { HeroDetailComponent } from './hero-detail.component';
import { HeroesComponent }     from './heroes.component';
import { HeroService }         from './hero.service';
@NgModule({
  imports: [
    BrowserModule,
    FormsModule
  ],
  declarations: [
    AppComponent,
    HeroDetailComponent,
    HeroesComponent
  ],
  providers: [
    HeroService
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule {
}
````

Si vemos el navegador, podemos comprobar que todo sigue funcionando.

## Añadir el routing

Angular Router no está en el core de Angular, está en la librería `@angular/router`. Para usarlo deberemos importar el NgModule del router e importarlo en nuestro NgModule.  
Una vez hecho vamos a configurar las rutas.

### Añadir el tag base en el HTML

Abrimos el `index.html` y añadimos `<base href="/">` a la sección `<head>`:

````html
<head>
  <base href="/">
  ...
  ...
</head>
````

Sin esto el router no funciona, es muy importante.

### Configurar las rutas

De momento no tenemos rutas definidas. Las rutas le van a decir al router que vista debe de cargar con cada ruta.

Vamos a definir nuestra primera ruta en nuestro `AppModule` tenemos que añadir este código:

````typescript
import { RouterModule }   from '@angular/router';

RouterModule.forRoot([
  {
    path: 'heroes',
    component: HeroesComponent
  }
])
````

La definición de la ruta tiene dos partes:
* path: la ruta que debe contener la url para que se active.
* component: el componente que se muestra cuando el path coincida con la ruta.

### Vamos activar el router

Hemos configurado una ruta inicial, pero ahora vamos a añadirla a nuestro NgModule.
Nuestro `app.module.ts` queda así


```` typescript
import { NgModule }       from '@angular/core';
import { BrowserModule }  from '@angular/platform-browser';
import { FormsModule }    from '@angular/forms';
import { RouterModule }   from '@angular/router';

import { AppComponent }        from './app.component';
import { HeroDetailComponent } from './hero-detail.component';
import { HeroesComponent }     from './heroes.component';
import { HeroService }         from './hero.service';

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    RouterModule.forRoot([
      {
        path: 'heroes',
        component: HeroesComponent
      }
    ])
  ],
  declarations: [
    AppComponent,
    HeroDetailComponent,
    HeroesComponent
  ],
  providers: [
    HeroService
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule {
}
````

### El router outlet

Cuando nosotros pongamos en nuestra barra de direcciones `/heroes` el router debería darse cuenta de que debe activar el componente `HeroesComponent`, pero ¿donde??

Le diremos donde tiene que mostrar las vistas usando  el elemento `<router-outlet>` al final de la plantilla de `AppComponent`.

### Enlaces para el router

Para no hacer que el usuario escriba las rutas en la barra de navegación, pondremos un enlace para que pueda acceder de forma facil, eso lo haremos con la directiva `routerLink`.

La plantilla de `AppComponent` quedará así:

````typescript
template: `
   <h1>{{title}}</h1>
   <a routerLink="/heroes">Heroes</a>
   <router-outlet></router-outlet>
`
````

Ahora refresca el browser y verás que ahora solo tienes el título de la aplicación y el enlace a los heroes.

Cuando pinchamos en el enlace la url cambia a `/heroes` y la lista de heroes aparece.

Hasta ahora el `app.component.ts` queda así:

```` typescript
import { Component } from '@angular/core';
@Component({
  selector: 'my-app',
  template: `
     <h1>{{title}}</h1>
     <a routerLink="/heroes">Heroes</a>
     <router-outlet></router-outlet>
   `
})
export class AppComponent {
  title = 'Tour of Heroes';
}
````

## Añadiendo el Dashboard

El routing tendrá más sentido cuando tengamos más vistas y eso es lo que vamos a hacer.
Vamos a crear el fichero `app/dashboard.component.ts` y dentro copiamos esto:

````typescript
import { Component } from '@angular/core';

@Component({
  selector: 'my-dashboard',
  template: '<h3>My Dashboard</h3>'
})
export class DashboardComponent { }
````

Luego volveremos a él para hacer cosas.

### Configurando la ruta del dashboard.

Volvemos a nuestro NgModule y vamos a decir como navegar al dashboard.

Importamos el componente y creamos la siguiente ruta:

````typescript
{
  path: 'dashboard',
  component: DashboardComponent
},
````

Además también tenemos que añadir el componente `DashboardComponent` a las declaraciones del NgModule:

````typescript
declarations: [
  AppComponent,
  DashboardComponent, // <<<-- así
  HeroDetailComponent,
  HeroesComponent
],
````

### Redirigiendo el router

Lo que queremos es que de primeras se cargue el dashboard, así que lo que haremos es redirigir la ruta `/` a la ruta `/dashboard`, lo vamos a hacer añadiendo esta ruta:

````typescript
{
  path: '',
  redirectTo: '/dashboard',
  pathMatch: 'full'
},
````

### Añadir el Dashboard al menú

Añadimos un enlace más para ir al dashboard en `AppComponent`:

````typescript
template: `
   <h1>{{title}}</h1>
   <nav>
     <a routerLink="/dashboard">Dashboard</a>
     <a routerLink="/heroes">Heroes</a>
   </nav>
   <router-outlet></router-outlet>
 `
````

Ahora tenemos una navegación completa con la que podemos visitar cada uno de los componentes de nuestra aplicación.

## Los principales héroes en el Dashboard

Vamos a mostrar los principales héroes en el dashboard para que se vean de un vistazo.

Empezamos por separar la plantilla del componente, reemplazamos la propiedad `template` por `templateUrl` en los metadatos del `DashboardComponent`.

Los metadatos quedarán así:

````typescript
@Component({  
  selector: 'my-dashboard',
  templateUrl: 'app/dashboard.component.html',
})
````

Y la plantilla irá a un nuevo fichero **app/dashboard.component.html**:
````html
<h3>Top Heroes</h3>
<div class="grid grid-pad">
  <div *ngFor="let hero of heroes" class="col-1-4">
    <div class="module hero">
      <h4>{{hero.name}}</h4>
    </div>
  </div>
</div>
````

Hemos vuelto a usar `*ngFor` para iterar sobre una lista de heroes, esta vez sobre un elemento `<div>`.

### Reutilizando HeroService

Si, volvemos a usar el servicio de héroes para cargar los héroes. Por esto lo separamos del AppComponent.

En `dashboard.component.ts` vamos a importar estos elementos:

````typescript
import { Component, OnInit } from '@angular/core';

import { Hero } from './hero';
import { HeroService } from './hero.service';
````

Y ahora vamos cambiar la implementación del componente con esto:

````typescript
export class DashboardComponent implements OnInit {

  heroes: Hero[] = [];

  constructor(private heroService: HeroService) { }

  ngOnInit(): void {
    this.heroService.getHeroes()
      .then(heroes => this.heroes = heroes.slice(1, 5));
  }
}
````

Básicamente lo que hemos hecho es:

* Definir `heroes`como un array.
* Inyectar `HeroService` en el constructor y mantenerlo como una propiedad privada como `heroService`.
* Implementar el Lifecycle Hook `ngOnInit`.
* Llamar en `ngOnInit` al servicio para cargar los heroes.

En el dashboard hemos cargado cuatro heroes, estos son el 2º, 3ª, 4º y 5º, esto lo hemos hecho con el método `Array.slice`.

Refresca el browser y observa tus héroes en el dashboard.

## Navegar al detalle del héroe

Aunque ahora mismo estamos mostrando el detalle del héroe en la parte baja de `HeroesComponent`, no tenemos ninguna forma de navegar al `HeroDetailComponent`. Tenemos que habilitar tres formas de navegar a los detalles:

1. Desde el dashboard cuando seleccionamos un héroe.
2. Desde la lista de heroes seleccionando uno de ellos.
3. Desde un enlace directo pegando la url en el browser.

Tenemos que añadir una ruta que navegue al detalle.

### Navegar al detalle

Añadiremos una ruta a la lista de rutas de nuestro NgModule igual que estan las otras, pero esta nueva ruta es distinta puesto que debemos pasarle al `HeroDetailComponent` qué héroe mostrar.

Ahora mismo `HeroDetailComponent` esta recibiendo el héroe a través de un binding de propiedad: 

````html
<my-hero-detail [hero]="selectedHero"></my-hero-detail>
````

Pero esto no funciona con el routing, no podemos meter el objeto `Hero` en la URL para pasarselo.

### Ruta con parametros

Lo que si podemos hacer es pasar el `id` del heroe a mostran en la URL. De forma que cuando queramos mostrar el héroe con `id` 11 la URL será `/detail/11`. La primera parte de la URL `/detail/` si será constante pero la segunda parte no. Necesitamos meter una variable como parte de la ruta.

Esta será la configuración de la ruta que debemos meter:

````typescript
{
  path: 'detail/:id',
  component: HeroDetailComponent
},
````

Los dos puntos (:) en el path indican que `:id` es el lugar donde meteremos el `id` del heroe cuando naveguemos a `HeroDetailComponent`.

Antes de añadir enlaces de navegación al detalle debemos preparar `HeroDetailComponent` para que funcione cogiendo el `id` del héroe de la ruta.

### Revisando HeroDetailComponent

Antes de cambiarlo echemosle un vistazo:

````typescript
import { Component, Input } from '@angular/core';
import { Hero } from './hero';
@Component({
  selector: 'my-hero-detail',
  template: `
    <div *ngIf="hero">
      <h2>{{hero.name}} details!</h2>
      <div>
        <label>id: </label>{{hero.id}}
      </div>
      <div>
        <label>name: </label>
        <input [(ngModel)]="hero.name" placeholder="name"/>
      </div>
    </div>
  `
})
export class HeroDetailComponent {
  @Input() hero: Hero;
}
````

La plantilla no va a cambiar, vamos a mostrar el héroe del mismo modo, pero si habrán cambios en la implementación.

El componente debe coger el `id` de la propiedad observable `params` que cogemos de un objeto del tipo `ActivatedRoute` y debemos usar `HeroService` para cargar el héroe a través del `id` obtenido.

La sección de los imports queda así:

````typescript
// Mantenemos de momento Input, más tarde lo eliminaremos
import { Component, Input, OnInit } from '@angular/core';
import { ActivatedRoute, Params }   from '@angular/router';
import { Location }                 from '@angular/common';

import { HeroService } from './hero.service';
````

Vamos a usar la inyección de dependencias para cargar los objetos de tipo `ActivatedRoute`, `HeroService` y `Location`, lo hacemos así:

````typescript
constructor(
  private heroService: HeroService,
  private route: ActivatedRoute,
  private location: Location
) {}
````

También vamos a importar el operador `switchMap` que usaremos despues con los parametros observables.

````typescript
import 'rxjs/add/operator/switchMap';
````

Haremos que nuestra clase `HeroDetailComponent` implemente el interface `OnInit`.

Cuando el componente se inicie usaremos la ruta actual para acceder a los parametros de la ruta, lo haremos así:

````typescript
ngOnInit(): void {
  this.route.params
    .switchMap((params: Params) => this.heroService.getHero(+params['id']))
    .subscribe(hero => this.hero = hero);
}
````

Fijate como obtenemos el objeto `params` para obtener el `id` de la ruta y usamos `heroService` para obtener el heroe.

> ### ¿Necesitamos cancelar la suscripción?  
> El `Router` manega los observables y sus suscripciones. Las suscripciones se eliminarán cuando el componente se destruya de forma que no hayan memory leaks. Por lo tanto, no, no necesitamos desuscribirnos a mano.

### Añadir `HeroService.getHero`

Abrimos `app/hero.service.ts` y añadimos este método:

````typescript
getHero(id: number): Promise<Hero> {
  return this.getHeroes()
             .then(heroes => heroes.find(hero => hero.id === id));
}
````

Hecho esto volvemos a `HeroDetailComponent` para atar cabos sueltos.

### Activar una forma de volver atrás

Navegaremos a `HeroDetailComponent` desde distintos sitios, ¿entonces como volvemos atrás? ¿a donde navegamos?

Activaremos un botón para navegar hacia atrás, añadimos esto a la plantilla:

````html
<button (click)="goBack()">Back</button>
````

Y este método al componente:

````typescript
goBack(): void {
  this.location.back();
}
````

Llegados a este punto podemos separar la plantilla del componente, creamos el fichero **app/hero-detail.component.html** con el contenido de la plantilla:

````html
<div *ngIf="hero">
  <h2>{{hero.name}} details!</h2>
  <div>
    <label>id: </label>{{hero.id}}</div>
  <div>
    <label>name: </label>
    <input [(ngModel)]="hero.name" placeholder="name" />
  </div>
  <button (click)="goBack()">Back</button>
</div>
````

Y cambiaremos los metadatos del componente a esto:
````typescript
@Component({  
  selector: 'my-hero-detail',
  templateUrl: 'app/hero-detail.component.html',
})
````

## Seleccionar un heroe en el dashboard

Cuando el usuario selecciona un héroe en el dashboard, la aplicación debería navegar a `HeroDetailComponent` para ver y editar el heroe seleccionado.

Vamos a `dashboard.component.html` y cambiamos el `<div *ngFor...>` por:

````html
<a *ngFor="let hero of heroes"  [routerLink]="['/detail', hero.id]"  class="col-1-4">
````

Fijate en el binding dl `routerLink`. Hasta ahora lo habíamos visto para navegar a rutas fijas ahora le estamos pasando una variable.

Refresca el browser y verás como funciona la navegación.

## Refactorizar las rutas

Nuestro `AppModule` tiene demasiadas lineas, vamos a extraer todo lo referente a las rutas.

Vamos a crear un modulo de rutas, un routing module.

Creamos `app-routing.module.ts` y lo rellenamos con este contenido:

````typescript
import { NgModule }             from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { DashboardComponent }   from './dashboard.component';
import { HeroesComponent }      from './heroes.component';
import { HeroDetailComponent }  from './hero-detail.component';

const routes: Routes = [
  { path: '', redirectTo: '/dashboard', pathMatch: 'full' },
  { path: 'dashboard',  component: DashboardComponent },
  { path: 'detail/:id', component: HeroDetailComponent },
  { path: 'heroes',     component: HeroesComponent }
];

@NgModule({
  imports: [ RouterModule.forRoot(routes) ],
  exports: [ RouterModule ]
})
export class AppRoutingModule {}
````

Características principales de los Routing Modules:

* Ponemos las rutas en una variable.
* Añadimos las rutas con `RouterModule.forRoot(routes)` en `imports`.
* Añadimos `RouterModule` a `exports` para que los componentes del los módulos que usen este reconozcan directivas como `RouterLink`y `RouterOutlet`.
* No hay `declarations`, eso es responsabilidad del `AppModule`
* Puede contener `providers`, aunque en este caso no los vamos a necesitar.

### Actualizamos AppModule
Ahora debemos eliminar la configuración del routing de nuestro módulo principal e importar `AppRoutingModule`, tanto el módulo ES6 como en nuestro NgModule.

Quedará así:

````typescript
import { NgModule }       from '@angular/core';
import { BrowserModule }  from '@angular/platform-browser';
import { FormsModule }    from '@angular/forms';

import { AppComponent }         from './app.component';
import { DashboardComponent }   from './dashboard.component';
import { HeroDetailComponent }  from './hero-detail.component';
import { HeroesComponent }      from './heroes.component';
import { HeroService }          from './hero.service';
import { AppRoutingModule }     from './app-routing.module';

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    AppRoutingModule
  ],
  declarations: [
    AppComponent,
    DashboardComponent,
    HeroDetailComponent,
    HeroesComponent
  ],
  providers: [ HeroService ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
````


## Seleccionando un héroe en HeroesComponent

Hemos añadido la posibilidad de seleccionar un heroe en el dashboard, ahora haremos algo parecido en `HeroesComponent`.

Ahora mismo `HeroesComponent` muestra un maestro/detalle con la lista de héroes arriba y los detalles del héroe seleccionado abajo.

```` typescript
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

Nuestro objetivo es mostrar el héroe seleccionado y que el usuario decida si quiere editarlo o no.

Eliminamos el `<h1>` de arriba (si no lo hemos quitado ya) y quitamos también el `<my-hero-detail>` de abajo.

Vamos a mostrar abajo los detalles del heroe seleccionado pero dejaremos que el usuario decida si quiere editarlo.

### Añadir un mini-detalle

Abajo, donde teníamos la edición del heroe añadimos esto:

````html
<div *ngIf="selectedHero">
  <h2>
    {{selectedHero.name | uppercase}} is my hero
  </h2>
  <button (click)="gotoDetail()">View Details</button>
</div>
````

Así cuando seleccione un héroe lo mostrará abajo y el usuario decide si navega a los detalles.

### Formatear con `uppercase` pipe

Fijate como el nombre del héroe se muestra en mayúsculas. Este es un efecto que hemos conseguido con un tipo de elemento de Angular que llamamos `pipe`:

````
{{selectedHero.name | uppercase}} is my hero
````

Los pipes son la mejor forma de formatear cadenas, cantidades con moneda, fechas y otras cosas parecidas. Angular viene con varios pipes y además tu puedes escribir los tuyos.

### Sacar contenido del fichero del componente.

No hemos terminado todavía. Necesitaremos navegar a `HeroDetailComponent` cuando el usuario pinche en el botón **View Details**.

Antes de esto, debemos darnos cuenta de que nuestro componente es demasiado grande, ya que tiene el HTML y el CSS en los metadatos.

Vamos a migrar la plantilla y los estilos a sus propios ficheros, haremos estos cambios:

1. Corta y pega el contenido de la plantilla a un nuevo fichero `heroes.component.html`.
2. Corta y pega los estilos un nuevo fichero `heroes.component.css`.
3. Establece las propiedades `templateUrl` y `styleUrls` apuntando a los correspondientes ficheros.

> La propiedad `styleUrls` es un array, podemos incluir más de un fichero de estilos.

Quedará así:

````typescript
@Component({
  moduleId: module.id,
  selector: 'my-heroes',
  templateUrl: 'heroes.component.html',
  styleUrls: [ 'heroes.component.css' ]
})
````

### Cambios en la clase `HeroesComponent`

El componente debe navegar a `HeroDetailComponent` en respuesta al click del botón, el cual está bindeado al método `gotoDetail` que debe navegar usando el `router` y diciendole donde ir.

Esto va a requerir algunos cambios:

1. Debemos importar el `router` del la librería `router` de Angular.
2. Inyectar el router en el constructor, al igual que `HeroService`
3. Implementar el método `gotoDetail` para que llame a `router.navigate`

El método `gotoDetail` será así:

````typescript
gotoDetail(): void {
  this.router.navigate(['/detail', this.selectedHero.id]);
}
````

Y la clase quedará así:

````typescript
export class HeroesComponent implements OnInit {
  heroes: Hero[];
  selectedHero: Hero;

  constructor(
    private router: Router,
    private heroService: HeroService) { }

  getHeroes(): void {
    this.heroService.getHeroes().then(heroes => this.heroes = heroes);
  }

  ngOnInit(): void {
    this.getHeroes();
  }

  onSelect(hero: Hero): void {
    this.selectedHero = hero;
  }

  gotoDetail(): void {
    this.router.navigate(['/detail', this.selectedHero.id]);
  }
}
````

Ahora ve al browser y prueba la navegación de la aplicación.

## Dando un poco más de estilos

### Estilos en el dashboard

Creemos un fichero de estilos **app/dashboard.component.css** con este contenido:

````css
[class*='col-'] {
  float: left;
  padding-right: 20px;
  padding-bottom: 20px;
}
[class*='col-']:last-of-type {
  padding-right: 0;
}
a {
  text-decoration: none;
}
*, *:after, *:before {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
h3 {
  text-align: center; margin-bottom: 0;
}
h4 {
  position: relative;
}
.grid {
  margin: 0;
}
.col-1-4 {
  width: 25%;
}
.module {
  padding: 20px;
  text-align: center;
  color: #eee;
  max-height: 120px;
  min-width: 120px;
  background-color: #607D8B;
  border-radius: 2px;
}
.module:hover {
  background-color: #EEE;
  cursor: pointer;
  color: #607d8b;
}
.grid-pad {
  padding: 10px 0;
}
.grid-pad > [class*='col-']:last-of-type {
  padding-right: 20px;
}
@media (max-width: 600px) {
  .module {
    font-size: 10px;
    max-height: 75px; }
}
@media (max-width: 1024px) {
  .grid {
    margin: 0;
  }
  .module {
    min-width: 60px;
  }
}
````

Recuerda referenciar el archivo usando `styleUrls`:

````typescript
styleUrls: [ 'app/dashboard.component.css' ]
````

### Estilos para los detalles

Hacemos lo mismo en `HeroDetailComponent`, añadimos el archivo **hero-detail.component.css**:

````css
label {
  display: inline-block;
  width: 3em;
  margin: .5em 0;
  color: #607D8B;
  font-weight: bold;
}
input {
  height: 2em;
  font-size: 1em;
  padding-left: .4em;
}
button {
  margin-top: 20px;
  font-family: Arial;
  background-color: #eee;
  border: none;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer; cursor: hand;
}
button:hover {
  background-color: #cfd8dc;
}
button:disabled {
  background-color: #eee;
  color: #ccc; 
  cursor: auto;
}

````

### Estilos para los enlaces de navegación

Igual para `AppComponent`, añadimos un archivo **app.component.css** con este contenido:

````css
h1 {
  font-size: 1.2em;
  color: #999;
  margin-bottom: 0;
}
h2 {
  font-size: 2em;
  margin-top: 0;
  padding-top: 0;
}
nav a {
  padding: 5px 10px;
  text-decoration: none;
  margin-top: 10px;
  display: inline-block;
  background-color: #eee;
  border-radius: 4px;
}
nav a:visited, a:link {
  color: #607D8B;
}
nav a:hover {
  color: #039be5;
  background-color: #CFD8DC;
}
nav a.active {
  color: #039be5;
}
````

> ### **La directiva *routerLinkActive***
> El router de Angular dispone de una directiva `routerLinkActive` que podemos usarla para añadir una clase al elemento HTML siempre que la ruta actual sea igual a la que pasamos a `routerLink`. La añadimos a los enlaces así:
> ````typescript
> template: `
>  <h1>{{title}}</h1>
>  <nav>
>    <a routerLink="/dashboard" routerLinkActive="active">Dashboard</a>
>    <a routerLink="/heroes" routerLinkActive="active">Heroes</a>
>  </nav>
>  <router-outlet></router-outlet>
>`,

Recuerda incluir el archivo css en `styleUrls`:

````typescript
styleUrls: ['app.component.css'],
````

### Estilos globales de la aplicación

Hasta ahora hemos añadido estilos a los componentes, pero también podemos añadir estilos como siempre.

Podemos crear un archivo `styles.css` e incluirlo de la forma estandar en HTML.

[Aquí](https://raw.githubusercontent.com/angular/angular.io/master/public/docs/_examples/_boilerplate/styles.css) tenemos el contenido de nuestro fichero `styles.css`.

Podemos añadirlo si queremos en el **index.html**

````html
<link rel="stylesheet" href="styles.css">
````

## Estructura actual de la aplicación

````
|-tour-heroes
| |-app
| |-app.component.css
| |-app.component.ts
| |-app.module.ts
| |-app-routing.module.ts
| |-dashboard.component.css
| |-dashboard.component.html
| |-dashboard.component.ts
| |-hero.service.ts
| |-hero.ts
| |-hero-detail.component.css
| |-hero-detail.component.html
| |-hero-detail.component.ts
| |-heroes.component.css
| |-heroes.component.html
| |-heroes.component.ts
| |-main.ts
| |-mock-heroes.ts
|-node_modules ...
|-index.html
|-package.json
|-styles.css
|-systemjs.config.js
|-tsconfig.json
````

## Recapitulando

Hemos avanzado bastante:

* Hemos añadido el Router de Angular para navegar entre los distintos componentes.
* Hemos aprendido como crear enlaces para montar menús de navegación.
* Hemos usado rutas con parametros para pasar parametros a través del router a los componentes a los que navegamos.
* Hemos compartido `HeroService` entre varios componentes.
* Hemos movido el HTML y el CSS a archivos separados de nuestros componentes.
* Hemos visto el uso del `pipe` `uppercase`
* Hemos refactorizado las rutas en un módulo aparte, construyendo un `Routing Module`

## Lo próximo

Hemos hecho ya mucho, nuestra aplicación ya casi está, pero nos falta algo importante, el acceso remoto a los datos.