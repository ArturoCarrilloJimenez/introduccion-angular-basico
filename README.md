# Introducción a Angular: Pasos Básicos

Esta es una guía básica de angular a nivel muy básico que explica con varios ejemplos el funcionamiento de este

## 1. Overview

- Presentación de los conceptos esenciales de Angular (TypeScript, componentes, servicios, rutas).
- Cada rama del repositorio contiene un ejemplo práctico con los archivos y comandos necesarios.
- El objetivo es ofrecer una guía paso a paso para que en el futuro puedan ampliarse o personalizarse según las necesidades del proyecto.

## 2. Que se va ha ver en esta presentation

| Indice            | Contenido                                        |
| ----------------- | ------------------------------------------------ |
| `00-starter`      | Crear proyecto nuevo con Angular CLI             |
| `01-components`   | Generar y usar componentes                       |
| `02-inputs`       | Pasar datos desde un componente padre (Input)    |
| `03-outputs`      | Emitir eventos desde componentes (Output)        |
| `04-ngmodel`      | Two-way binding con `ngModel`                    |
| `05-http-service` | Servicio básico y llamadas HTTP con `HttpClient` |

---

## 3. Contenido

### 3.1. `00-start` – Comandos Básicos para iniciar un proyecto

```bash
# Instalación del cli de angular
# El decorador -g lo instala de forma global
npm install -g @angular/cli

# Comprobar que este instalado
ng --version

# Crear un nuevo proyecto
ng new <Nombre del proyecto>

# Ejecutar el proyecto de forma local
# El decorador -o se usa para abrirlo en el navegador
ng serve -o
```

### 3.2 `01-components` – Estructura de Componentes

#### Comandos para generar los diferentes componentes, servicios, guardias, etc.

| **Comando**                                   | **Descripción**                                                                 |
| --------------------------------------------- | ------------------------------------------------------------------------------- |
| `ng g component [name]` </br> `ng g c [name]` | Genera un nuevo componente en la ruta o nombre indicado (`[path/name]`).        |
| `ng g directive [name]` </br> `ng g d [name]` | Crea una nueva directiva.                                                       |
| `ng g guard [name]` </br> `ng g g [name]`     | Crea un nuevo guard para protección de rutas.                                   |
| `ng g interceptor [name]`                     | Crea un interceptor para interceptar y manipular peticiones HTTP (observables). |
| `ng g module [name]` </br> `ng g m [name]`    | Crea un nuevo módulo, que agrupará componentes, directivas, servicios, etc.     |
| `ng g pipe [name]` </br> `ng g p [name]`      | Crea un pipe que actúa como transformador de datos en plantillas.               |
| `ng g service [name]` </br> `ng g s [name]`   | Crea un servicio para compartir lógica o datos entre distintos componentes.     |

> [!NOTE]
> Todos los comandos aceptan la forma abreviada (`c`, `d`, `g`, `m`, `p`, `s`) y pueden recibir rutas anidadas, por ejemplo: `ng g component usuarios/perfil`.

#### Estructura de carpetas de un componente

Al generar un componente con Angular CLI, se crea una carpeta que normalmente incluye cuatro archivos principales (aunque solo utilizaremos habitualmente los dos primeros):

1. **`[nombre-componente].component.ts`**

   - Define la clase del componente y el decorador `@Component` con metadatos (selector, templateUrl, styleUrls).
   - Contiene la lógica de presentación (propiedades, métodos, ciclo de vida).

2. **`[nombre-componente].component.html`**

   - Plantilla HTML donde se define la estructura de la interfaz de usuario del componente.
   - Suelen incluirse directivas de Angular (p. ej., `*ngIf`, `*ngFor`, `[property]`, `(event)`).

3. **`[nombre-componente].component.css`**

   - Estilos específicos de ese componente.
   - Estos estilos están encapsulados por defecto (ViewEncapsulation), de modo que no afectan a otros componentes.

4. **`[nombre-componente].component.spec.ts`**
   - Archivo de pruebas unitarias creado automáticamente.
   - Se recomienda mantenerlo y complementar con test adicionales según crezca la complejidad del componente.

> [!TIP]
> Aunque Angular CLI genera la hoja de estilos (`.css`) y el archivo de pruebas (`.spec.ts`), en entornos corporativos o cursos introductorios puede optarse por minimizar estos dos archivos hasta que se aborden buenas prácticas de estilos avanzados y pruebas unitarias. Lo esencial al empezar es comprender la relación entre `*.component.ts` y `*.component.html`.

### 3.3 `02-inputs` – Pasar Datos al Componente (**Input**)

Una de las claves para crear componentes reutilizables en Angular es poder pasar datos desde un componente padre a uno hijo. Esto se logra mediante la propiedad **@Input** o usando **input signals** a partir de Angular 17.

#### Forma tradicional con `@Input()`

```ts
// src/app/input/child.component.ts
import { Component, Input } from "@angular/core";

@Component({
  selector: "app-child",
  template: `<p>Mensaje recibido: {{ mensaje }}</p>`,
})
export class ChildComponent {
  @Input() mensaje: string = "";
}
```

#### Nueva forma con `input() signal` **(Angular 17+)**

```ts
// src/app/input/child.component.ts
import { Component, input } from "@angular/core";

@Component({
  selector: "app-child",
  template: `<p>Mensaje recibido: {{ message() }}</p>`,
})
export class ChildComponent {
  // Input requerido
  message = input.required<string>();

  // Input opcional con valor por defecto
  optionalMessage = input<string>("Mensaje por defecto");
}
```

#### Cómo pasar datos desde el componente padre

La sintaxis en la plantilla HTML del componente padre no cambia, independientemente del método utilizado:

```html
<!-- src/app/parent/parent.component.html -->
<app-child [mensaje]="'Hola desde el padre'"></app-child>
```

> [!IMPORTANT]
> Asegúrate de que el nombre del atributo usado en la plantilla (**[mensaje]**) **coincida** con el decorador **@Input()** o la propiedad **input()** definida en el hijo.

#### Comparativa: `@Input()` vs. `input()` Signal

| **Característica**             | **`@Input()` (Forma tradicional)**                                                                                                                                             | **`input()` Signal (Angular 17+)**                                                                                                                                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Declaración**                | - Decorador en la clase del componente hijo:<br> `@Input() propiedad: Tipo`<br> - Se expone como atributo en la plantilla del padre.                                           | - Función `input()` en la clase del componente hijo:<br> `message = input.required<string>()`<br> - Se define de forma reactiva, sin necesidad de decoradores adicionales.                 |
| **Detección de cambios**       | - Basada en el mecanismo de “change detection” de Angular (check always).<br> - Cada ciclo revisa si el valor cambió.<br> - Para lógica adicional, se utiliza `ngOnChanges()`. | - Sistema de señales: solo se notifica cuando el valor realmente cambia.<br> - No requiere `ngOnChanges()`; la propia señal dispara la actualización de la vista.                          |
| **Rendimiento**                | - Puede generar ciclos de chequeo en cascada, incluso si el valor no cambia.<br> - En aplicaciones muy grandes, revisiones frecuentes impactan ligeramente.                    | - Solo se recomputa cuando la señal recibe un nuevo valor.<br> - Minimiza re-renderizados innecesarios en componentes anidados.<br> - Escala mejor en proyectos de alta complejidad.       |
| **Simplicidad de uso**         | - Muy familiar y documentado en todas las versiones anteriores.<br> - Requiere sincronizar nombres entre padre e hijo y, si hay lógica, implementar `ngOnChanges()`.           | - Enfoque declarativo: el hijo “escucha” automáticamente la señal.<br> - Reduce boilerplate: no hay que implementar hooks de ciclo de vida para detectar cambios.                          |
| **Legibilidad/Mantenibilidad** | - Separación clara: padre asigna valor, hijo recibe con `@Input()`.<br> - Lógica de cambio dispersa si se usa `ngOnChanges()`.                                                 | - Clara intención reactiva: el hijo define explícitamente “qué señal recibe”.<br> - Facilita composición con otras señales (`computed()`, `effect()`).                                     |
| **Compatibilidad/Futuro**      | - Sigue siendo totalmente soportado y no se deprecará.<br> - Ideal para proyectos existentes que no requieran migrar a señales.                                                | - Representa la evolución del modelo reactivo de Angular.<br> - Permite adaptar APIs futuras (formularios, router, etc.) a un rendimiento optimizado con señales.                          |
| **Cuándo usarlo**              | - Proyectos legacy o módulos sencillos sin necesidad de lógica compleja al recibir cambios.<br> - Casos donde la sobrecarga de `ngOnChanges()` es aceptable.                   | - Nuevos proyectos donde se busque un enfoque completamente reactivo.<br> - Componentes que reaccionan a cambios en tiempo real sin hooks adicionales.<br> - Alta escalabilidad requerida. |

> [!NOTE]
>
> - `@Input()` continuará siendo la base para la mayoría de aplicaciones existentes
> - `input()` Signal impulsa un modelo de reactividad de próxima generación, reduciendo ciclos de detección y facilitando arquitecturas más escalables y mantenibles.

### 3.4 `03-outputs` – Emitir Eventos al Padre (**Output**)

Para que un componente hijo notifique al padre sobre eventos o cambios, Angular ofrece tradicionalmente el decorador `@Output()` con `EventEmitter`.**A partir de Angular 17** se introduce la función` output()`, que simplifica la declaración y optimiza el manejo de eventos.

#### Forma tradicional con @Output()

```ts
// src/app/output/child.component.ts
import { Component, Output, EventEmitter } from "@angular/core";

@Component({
  selector: "app-child",
  template: `<button (click)="enviarEvento()">Enviar mensaje</button>`,
})
export class ChildComponent {
  @Output() clicEvento = new EventEmitter<string>();

  enviarEvento(): void {
    this.clicEvento.emit("¡Datos desde el hijo!");
  }
}
```

`@Output()` define una propiedad pública que emite eventos hacia el componente padre.

El padre deberá suscribirse a (clicEvento) en la plantilla para manejar la información.

#### Nueva forma con output() (Angular 17+)

```ts
// src/app/output/child.component.ts
import { Component, output } from "@angular/core";

@Component({
  selector: "app-child",
  template: `<button (click)="enviarEvento()">Enviar mensaje</button>`,
})
export class ChildComponent {
  // Declaración reactiva de un evento de salida
  readonly clicEvento = output<string>();

  enviarEvento(): void {
    // Lanza el valor al padre; equivale a EventEmitter.emit()
    this.clicEvento.emit("¡Datos desde el hijo!");
  }
}
```

`output<T>()` crea un emisor de eventos sin necesidad del decorador @Output().

Se declara como propiedad de solo lectura (readonly), fomentando inmutabilidad.

El `método .emit(...)` **funciona idéntico** a EventEmitter.emit(...), pero la API reacciona de forma más “fina” a suscripciones.

#### Cómo escuchar eventos en el componente padre

La sintaxis en la plantilla del padre es idéntica en ambos casos; solo cambia el origen interno del evento:

```html
<!-- src/app/parent/parent.component.html -->
<app-child (clicEvento)="recibirMensaje($event)"></app-child>
<p>Mensaje del hijo: {{ mensajeRecibido }}</p>
```

```ts
// src/app/parent/parent.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-parent",
  templateUrl: "./parent.component.html",
})
export class ParentComponent {
  mensajeRecibido: string = "";

  recibirMensaje(valor: string): void {
    this.mensajeRecibido = valor;
  }
}
```

> [!IMPORTANT]
> Asegúrate de que el nombre del evento en la plantilla ((clicEvento)) coincida con la propiedad declarada en el hijo, ya sea @Output() clicEvento o readonly clicEvento = output<string>().

| **Característica**             | **`@Output()` (Tradicional)**                                                                                                                     | **`output()` (Angular 17+)**                                                                                                                                 |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Declaración**                | - Decorador en la clase del hijo:<br> `@Output() evento = new EventEmitter<T>()`<br> - Define una propiedad pública mutable.                      | - Función `output<T>()` en la clase del hijo:<br> `readonly evento = output<T>()`<br> - Se define como señal inmutable (solo lectura).                       |
| **Detección de eventos**       | - Basado en la implementación de `EventEmitter`, que envía eventos al padre.<br> - El ciclo de detección de cambios de Angular notifica al padre. | - Internamente optimizado para notificar solo cuando `.emit()` se invoca.<br> - Se integra con el sistema de señales, evitando detecciones innecesarias.     |
| **Rendimiento**                | - Cada emisión recorre la cadena de suscripciones de `EventEmitter`.<br> - Puede generar sobrecarga en componentes muy anidados.                  | - Solo dispara notificaciones a suscriptores cuando `.emit()` se invoca con un nuevo valor.<br> - Minimiza re-renderizados en estructuras profundas.         |
| **Simplicidad de uso**         | - Muy familiar para versiones anteriores.<br> - Requiere `import { Output, EventEmitter } from "@angular/core"`.                                  | - Más conciso: `import { output } from "@angular/core"`.<br> - No hay necesidad de instanciar `new EventEmitter()`.                                          |
| **Legibilidad/Mantenibilidad** | - Lógica de emisión dispersa (constructor + métodos).<br> - Propiedad mutable: riesgo de reasignaciones accidentales.                             | - Propiedad inmutable (`readonly`).<br> - Código más declarativo: queda claro “qué eventos expone” el componente desde la firma de la clase.                 |
| **Compatibilidad/Futuro**      | - Totalmente soportado y no se deprecará.<br> - API establecida, ideal para proyectos existentes que no usen señales.                             | - Parte del modelo de señales de Angular (17+).<br> - Se espera que más APIs internas (router, formularios) adopten este patrón para mejorar la reactividad. |
| **Cuándo usarlo**              | - Proyectos legacy o módulos sencillos que no requieran migrar a señales.<br> - Casos donde el sobrecosto de `EventEmitter` es aceptable.         | - Nuevos proyectos o módulos donde se busque un enfoque totalmente reactivo.<br> - Componentes con alta frecuencia de eventos que requieren eficiencia.      |

> [!NOTE]
> - @Output() seguirá existiendo para garantizar compatibilidad plena.
> - output() impulsa una evolución hacia un sistema de eventos más reactivo y optimizado para Angular en el futuro.

### 3.5 `04-ngmodel` – Two-Way Binding con `ngModel`

1. **Importar `FormsModule` en el módulo**
   ```ts
   // src/app/app.module.ts
   import { FormsModule } from "@angular/forms";
   @NgModule({
     imports: [
       BrowserModule,
       FormsModule,
       /* … */
     ],
     /* … */
   })
   export class AppModule {}
   ```
2. **Crear componente con campo de entrada**

   ```html
   <!-- src/app/binding/binding.component.html -->
   <input [(ngModel)]="texto" placeholder="Escribe aquí" />
   <p>Valor actual: {{ texto }}</p>
   ```

   ```ts
   // src/app/binding/binding.component.ts
   import { Component } from "@angular/core";

   @Component({
     selector: "app-binding",
     templateUrl: "./binding.component.html",
   })
   export class BindingComponent {
     texto: string = "";
   }
   ```

3. **Arrancar la aplicación**
   - `ng serve`
   - Observar que al escribir en el `<input>`, el `<p>` se actualiza en tiempo real.

### 3.6 `05-http-service` – Servicio y Llamadas HTTP

1. **Importar `HttpClientModule` en el módulo**
   ```ts
   // src/app/app.module.ts
   import { HttpClientModule } from "@angular/common/http";
   @NgModule({
     imports: [
       BrowserModule,
       HttpClientModule,
       /* … */
     ],
     /* … */
   })
   export class AppModule {}
   ```
2. **Crear servicio para consumo de API**

   ```ts
   // src/app/servicios/api.service.ts
   import { Injectable } from "@angular/core";
   import { HttpClient } from "@angular/common/http";
   import { Observable } from "rxjs";

   @Injectable({ providedIn: "root" })
   export class ApiService {
     private apiURL = "https://jsonplaceholder.typicode.com/posts";

     constructor(private http: HttpClient) {}

     obtenerPosts(): Observable<any[]> {
       return this.http.get<any[]>(this.apiURL);
     }
   }
   ```

3. **Consumir el servicio en un componente**

   ```ts
   // src/app/posts/posts.component.ts
   import { Component, OnInit } from "@angular/core";
   import { ApiService } from "../servicios/api.service";

   @Component({
     selector: "app-posts",
     template: `
       <ul>
         <li *ngFor="let post of posts">{{ post.title }}</li>
       </ul>
     `,
   })
   export class PostsComponent implements OnInit {
     posts: any[] = [];

     constructor(private apiService: ApiService) {}

     ngOnInit(): void {
       this.apiService.obtenerPosts().subscribe((datos) => {
         this.posts = datos;
       });
     }
   }
   ```

4. **Ejecutar y verificar datos**
   - `ng serve`
   - Comprobar que la lista de títulos de posts se muestra correctamente.

## 4. Buenas Prácticas y Próximos Pasos

- **Arquitectura modular:** Organizar cada conjunto de componentes y servicios en su propio módulo para facilitar el mantenimiento y escalabilidad.
- **Lazy loading:** Cargar módulos de forma diferida cuando sean necesarios, reduciendo el tamaño del bundle inicial.
- **Documentación interna:** Comentar funciones relevantes y explicar la lógica central de servicios y componentes.
- **Pruebas automatizadas:** Añadir `*.spec.ts` en cada componente y servicio para garantizar la calidad futura de la aplicación.
- **Actualizaciones periódicas:** Utilizar `ng update` para mantener Angular y sus dependencias al día, aprovechando nuevas funcionalidades y mejoras de rendimiento.
- **Visión de futuro:** Investigar temas avanzados como Angular Universal (SSR), micro frontends o integración con Web Components.
