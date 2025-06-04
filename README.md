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

## 3.1. `00-start` – Comandos Básicos para iniciar un proyecto

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

| **Comando**                                    | **Descripción**                                                                 |
| ---------------------------------------------- | ------------------------------------------------------------------------------ |
| `ng g component [name]` </br> `ng g c [name]`  | Genera un nuevo componente en la ruta o nombre indicado (`[path/name]`).       |
| `ng g directive [name]` </br> `ng g d [name]`  | Crea una nueva directiva.                                                       |
| `ng g guard [name]` </br> `ng g g [name]`      | Crea un nuevo guard para protección de rutas.                                   |
| `ng g interceptor [name]`                      | Crea un interceptor para interceptar y manipular peticiones HTTP (observables). |
| `ng g module [name]` </br> `ng g m [name]`     | Crea un nuevo módulo, que agrupará componentes, directivas, servicios, etc.     |
| `ng g pipe [name]` </br> `ng g p [name]`       | Crea un pipe que actúa como transformador de datos en plantillas.               |
| `ng g service [name]` </br> `ng g s [name]`    | Crea un servicio para compartir lógica o datos entre distintos componentes.     |

> **Nota:** Todos los comandos aceptan la forma abreviada (`c`, `d`, `g`, `m`, `p`, `s`) y pueden recibir rutas anidadas, por ejemplo: `ng g component usuarios/perfil`.

---

#### Estructura de carpetas de un componente

Al generar un componente con Angular CLI, se crea una carpeta que normalmente incluye cuatro archivos principales (aunque solo utilizaremos habitualmente los dos primeros):

src/app/[nombre-componente]/
├── [nombre-componente].component.ts # Lógica en TypeScript y decorador @Component
├── [nombre-componente].component.html # Plantilla HTML relacionada al componente
├── [nombre-componente].component.css # Hoja de estilos CSS específica (puede ser .scss, .sass, etc.)
└── [nombre-componente].component.spec.ts # Pruebas unitarias (Jasmine/Karma)

markdown
Copiar
Editar

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

> **Recomendación:** Aunque Angular CLI genera la hoja de estilos (`.css`) y el archivo de pruebas (`.spec.ts`), en entornos corporativos o cursos introductorios puede optarse por minimizar estos dos archivos hasta que se aborden buenas prácticas de estilos avanzados y pruebas unitarias. Lo esencial al empezar es comprender la relación entre `*.component.ts` y `*.component.html`.

---

#### Ejemplo práctico

1. **Generar un nuevo componente llamado `usuario-lista`**  
   ```bash
   ng g component usuario-lista
Verificar la estructura generada

bash
Copiar
Editar
src/app/usuario-lista/
├── usuario-lista.component.ts
├── usuario-lista.component.html
├── usuario-lista.component.css
└── usuario-lista.component.spec.ts
Registrar el componente en el módulo (si no se hizo automáticamente)
En app.module.ts:

ts
Copiar
Editar
import { UsuarioListaComponent } from './usuario-lista/usuario-lista.component';

@NgModule({
  declarations: [
    AppComponent,
    UsuarioListaComponent,  // ← Componente generado
    /* …otros componentes… */
  ],
  imports: [ BrowserModule ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
Usar el selector en la plantilla principal
En app.component.html (u otro componente):

html
Copiar
Editar
<app-usuario-lista></app-usuario-lista>
Con estos comandos y la estructura descrita, dispondrá de la base para crear y organizar componentes de manera profesional y escalable dentro de un proyecto Angular. ```

---

### 4.3 `02-inputs` – Pasar Datos al Componente (Input)

1. **Definir propiedad con `@Input()` en componente hijo**

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

2. **En el componente padre, pasar valor**
   ```html
   <!-- src/app/parent/parent.component.html -->
   <app-child [mensaje]="'Hola desde el padre'"></app-child>
   ```
3. **Verificar que se muestra en pantalla**
   - `ng serve`
   - Asegurarse de que “Mensaje recibido: Hola desde el padre” aparece correctamente.

---

### 4.4 `03-outputs` – Emitir Eventos al Padre (Output)

1. **Definir `@Output()` y `EventEmitter` en componente hijo**

   ```ts
   // src/app/output/child.component.ts
   import { Component, Output, EventEmitter } from "@angular/core";

   @Component({
     selector: "app-child",
     template: `<button (click)="enviarEvento()">Enviar</button>`,
   })
   export class ChildComponent {
     @Output() clicEvento = new EventEmitter<string>();

     enviarEvento() {
       this.clicEvento.emit("¡Clic detectado en hijo!");
     }
   }
   ```

2. **Suscribirse al evento en componente padre**

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

     recibirMensaje(evento: string) {
       this.mensajeRecibido = evento;
     }
   }
   ```

3. **Probar en navegador**
   - Al hacer clic en el botón del hijo, la variable `mensajeRecibido` debe actualizarse.

---

### 4.5 `04-ngmodel` – Two-Way Binding con `ngModel`

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

---

### 4.6 `05-http-service` – Servicio y Llamadas HTTP

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

---

## 5. Instrucciones Generales de Uso

1. **Clonar el repositorio y acceder a la carpeta**
   ```bash
   git clone https://github.com/usuario/repositorio-angular.git
   cd repositorio-angular
   npm install
   ```
2. **Cambiar a la rama correspondiente**
   - Por ejemplo, para ver el ejemplo de componentes:
     ```bash
     git checkout 01-components
     ```
3. **Instalar dependencias e iniciar**
   ```bash
   npm install
   npm start  # o ng serve -o
   ```
4. **Explorar el código**
   - Cada carpeta de la rama contiene un README que describe los cambios realizados y qué archivos revisar.
   - La rama `master` muestra el código final tal como se espera una vez completados todos los ejemplos.

---

## 6. Buenas Prácticas y Próximos Pasos

- **Arquitectura modular:** Organizar cada conjunto de componentes y servicios en su propio módulo para facilitar el mantenimiento y escalabilidad.
- **Lazy loading:** Cargar módulos de forma diferida cuando sean necesarios, reduciendo el tamaño del bundle inicial.
- **Documentación interna:** Comentar funciones relevantes y explicar la lógica central de servicios y componentes.
- **Pruebas automatizadas:** Añadir `*.spec.ts` en cada componente y servicio para garantizar la calidad futura de la aplicación.
- **Actualizaciones periódicas:** Utilizar `ng update` para mantener Angular y sus dependencias al día, aprovechando nuevas funcionalidades y mejoras de rendimiento.
- **Visión de futuro:** Investigar temas avanzados como Angular Universal (SSR), micro frontends o integración con Web Components.
