# Guía para construir un formulario reactivo en Angular

En esta sección aprenderás a construir un formulario reactivo en Angular paso a paso. Cada paso incluye explicación detallada antes y después del código, pensado para quien nunca ha trabajado con Reactive Forms.

## Paso 1: Entender los enfoques de formularios

Angular ofrece dos enfoques para manejar formularios:

1. **Template-driven** (basado en plantilla): la lógica de validación y control está en el HTML con directivas como `ngModel`.
    - Ideal para formularios simples.
    - Menos control sobre la lógica de validación.

2. **Reactive** (reactivo): la lógica se gestiona desde el componente, usando objetos `FormGroup` y `FormControl`.
    - Mejor para formularios complejos.
    - Mayor control sobre la validación y el estado del formulario.

> [!NOTE]
> **¿Por qué elegir Reactive?**
> - Mayor control y escalabilidad.
> - Validaciones más predecibles.
> - Fácil composición de formularios complejos.

## Paso 2: Preparar el módulo de Angular

Antes de escribir cualquier formulario, debes indicar a Angular que vas a usar Reactive Forms. Para ello:

1. Abre tu archivo `app.module.ts` (o el módulo correspondiente).
2. Importa `ReactiveFormsModule` desde `@angular/forms`.
3. Añádelo al array `imports`.

```ts
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
// Importamos ReactiveFormsModule
import { ReactiveFormsModule } from '@angular/forms';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    ReactiveFormsModule  // <-- Habilita formularios reactivos
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

> **Explicación:** Al añadir `ReactiveFormsModule`, habilitas en tus plantillas las directivas necesarias (`formGroup`, `formControlName`, etc.).

---

## Paso 3: Crear el `FormGroup` en el componente

Vamos a crear un nuevo componente llamado `mi-formulario`. Dentro, definiremos un objeto `FormGroup`, que será el contenedor de todos los campos:

1. Genera el componente:
   ```bash
   ng generate component mi-formulario
   ```
2. En `mi-formulario.component.ts`, importa lo siguiente:

```ts
import { Component, OnInit } from '@angular/core';
// Importamos FormBuilder para facilitar la creación
import { FormBuilder, FormGroup } from '@angular/forms';
```

3. Declara la variable que contendrá el formulario:

```ts
export class MiFormularioComponent implements OnInit {
  // Aquí guardaremos nuestro formulario reactivo
  miFormulario: FormGroup;

  constructor(private fb: FormBuilder) {}  

  ngOnInit() {
    // Por ahora lo inicializamos vacío
    this.miFormulario = this.fb.group({});
  }
}
```

> **¿Qué hace **``**?**
>
> - Es una clase que simplifica la creación de formularios.
> - En lugar de `new FormGroup({...})`, llamamos a `this.fb.group({...})`.

---

## Paso 4: Añadir controles al formulario

Ahora vamos a incorporar campos a nuestro `FormGroup`. Empezaremos con **un solo campo** y luego iremos añadiendo más.

1. En el `group({ ... })`, define un control llamado `nombre` con valor inicial vacío (`''`):

```ts
ngOnInit() {
  this.miFormulario = this.fb.group({
    nombre: ['']  // Campo de texto vacío
  });
}
```

2. En tu plantilla HTML (`mi-formulario.component.html`), crea el formulario:

```html
<form [formGroup]="miFormulario" (ngSubmit)="onSubmit()">
  <label for="nombre">Nombre</label>
  <input id="nombre" formControlName="nombre" />
  <button type="submit">Enviar</button>
</form>
```

3. Añade la función `onSubmit` en el componente para ver los valores:

```ts
onSubmit() {
  console.log(this.miFormulario.value);
}
```

> **Prueba:** Inicia la aplicación, escribe un nombre y haz clic en "Enviar". Verás el objeto `{ nombre: 'valor' }` en la consola.

---

## Paso 5: Incorporar más campos y validaciones básicas

Ampliaremos el formulario con más controles: **email** y **contraseña**. Además, aplicaremos validaciones simples:

1. Importa `Validators`:
   ```ts
   import { Validators } from '@angular/forms';
   ```
2. Define los controles con validadores:

```ts
ngOnInit() {
  this.miFormulario = this.fb.group({
    nombre: ['', [Validators.required, Validators.minLength(3)]],
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(8)]]
  });
}
```

3. Muestra errores básicos bajo cada campo:

```html
<!-- Nombre -->
<div>
  <input formControlName="nombre" placeholder="Nombre" />
  <small *ngIf="miFormulario.get('nombre').touched && miFormulario.get('nombre').hasError('required')">
    El nombre es obligatorio.
  </small>
  <small *ngIf="miFormulario.get('nombre').hasError('minlength')">
    Mínimo 3 caracteres.
  </small>
</div>

<!-- Email -->
<div>
  <input formControlName="email" placeholder="Email" />
  <small *ngIf="miFormulario.get('email').hasError('email')">
    Formato de correo inválido.
  </small>
</div>
```

> **Nota importante:** Usamos `touched` para que el mensaje sólo aparezca tras salir del campo.

---

## Paso 6: Validaciones avanzadas y validación a nivel de grupo

### 6.1 Confirmación de contraseña

Para asegurar que el usuario escriba dos veces la misma contraseña:

1. Añade el control `confirmPassword`:

```ts
this.miFormulario = this.fb.group({
  // ... otros controles …
  password: ['', [Validators.required]],
  confirmPassword: ['', [Validators.required]]
}, {
  validators: this.passwordsIgualesValidator('password', 'confirmPassword')
});
```

2. Crea la función `passwordsIgualesValidator` en el componente:

```ts
private passwordsIgualesValidator(pwKey: string, cpwKey: string) {
  return (group: FormGroup) => {
    const pw = group.controls[pwKey].value;
    const cpw = group.controls[cpwKey].value;
    return pw === cpw ? null : { passwordMismatch: true };
  };
}
```

3. Mues*tra el error en la plantilla:*

```html
<small *ngIf="miFormulario.hasError('passwordMismatch')">
  Las contraseñas no coinciden.
</small>
```

### 6.2 Fecha de nacimiento y edad mínima

1. Añade `fechaNacimiento` con validador custom:

```ts
fechaNacimiento: ['', [Validators.required, this.edadMinimaValidator(18)]]
```

2. Crea `edadMinimaValidator`:

```ts
private edadMinimaValidator(minEdad: number) {
  return (control: FormControl) => {
    const fecha = new Date(control.value);
    const hoy = new Date();
    const edad = hoy.getFullYear() - fecha.getFullYear();
    return edad >= minEdad ? null : { edadMenor: true };
  };
}
```

3. En la plantilla:

```html
<small *ngIf="miFormulario.get('fechaNacimiento').hasError('edadMenor')">
  Debes ser al menos de {{18}} años.
</small>
```

---

## Paso 7: Validación asíncrona de email

Para simular comprobación en servidor:

1. Declara un array de emails existentes:
   ```ts
   private emailsExistentes = [
     'usuario1@ejemplo.com',
     'admin2@dominio.org',
     'test3@correo.net'
   ];
   ```
2. Crea el validador asíncrono con `of()` y `delay()`:

```ts
import { AsyncValidatorFn, AbstractControl, ValidationErrors } from '@angular/forms';
import { Observable, of } from 'rxjs';
import { delay, map } from 'rxjs/operators';

private emailAsyncValidator(): AsyncValidatorFn {
  return (control: AbstractControl): Observable<ValidationErrors | null> => {
    return of(this.emailsExistentes.includes(control.value))
      .pipe(
        delay(1000),
        map(existe => (existe ? { emailExistente: true } : null))
      );
  };
}
```

3. Asigna el validador al control `email`:

```ts
email: ['', [Validators.email], [this.emailAsyncValidator()]]
```

4. Muestra el error:

```html
<small *ngIf="miFormulario.get('email').hasError('emailExistente')">
  Este correo ya está registrado.
</small>
```

> **Cómo llevarlo a producción:** cambia `of()` por `this.http.get` a tu API.

---

## Paso 8: Resumen final y estilo de presentación

Has visto:

- Configuración del módulo.
- Creación paso a paso del `FormGroup`.
- Añadir controles uno a uno.
- Validaciones sincrónicas y asincrónicas.
- Validaciones a nivel de grupo.
- Varias formas de mostrar errores.

Ahora, ajusta este ejemplo a tu proyecto:

- Añade más campos (dirección, teléfono…).
- Cambia estilos CSS o utiliza librerías de UI.
- Integra validaciones reales contra tu backend.

---

## Paso 9: Lista rápida de validaciones útiles

Para facilitar tu trabajo, aquí tienes las validaciones clasificadas por frecuencia de uso y recomendaciones:

### Validaciones más comunes

| Validador                  | Descripción                     | Ejemplo                                  |
| -------------------------- | ------------------------------- | ---------------------------------------- |
| `Validators.required`      | Campo obligatorio.              | `['', [Validators.required]]`            |
| `Validators.minLength(n)`  | Mínimo de `n` caracteres.       | `['', [Validators.minLength(3)]]`        |
| `Validators.maxLength(n)`  | Máximo de `n` caracteres.       | `['', [Validators.maxLength(20)]]`       |
| `Validators.email`         | Formato de correo válido.       | `['', [Validators.email]]`               |
| `Validators.pattern(expr)` | Coincide con expresión regular. | `['', [Validators.pattern(/^[0-9]+$/)]]` |

### Validaciones menos comunes (pero útiles en casos específicos)

| Validador                   | Descripción                                                       | Ejemplo                                                     |
| --------------------------- | ----------------------------------------------------------------- | ----------------------------------------------------------- |
| Custom Validator (sync)     | Lógica propia para reglas complejas (p. ej. nombres, códigos).    | `this.fb.control('', [this.miValidator()])`                 |
| Custom Validator (async)    | Validación contra backend o lógica asíncrona.                     | `['', [], [this.miAsyncValidator()]]`                       |
| Validación a nivel de grupo | Compara o valida múltiples controles juntos (p. ej. contraseñas). | `this.fb.group({...}, { validators: this.validadorGrupo })` |
| `Validators.nullValidator`  | Validador que no hace nada (útil como placeholder).               | `['', [Validators.nullValidator]]`                          |

### Validaciones no recomendadas o con precaución

| Validador                 | Descripción                                                                 | Razón                                      |
| ------------------------- | --------------------------------------------------------------------------- | ------------------------------------------ |
| `Validators.requiredTrue` | Solo para `checkbox` (campo true).                                          | Limitado a casos muy concretos.            |
| `Validators.compose`      | Combina varios validadores, pero es mejor pasar un array directamente.      | Sintaxis menos legible.                    |
| Validadores extensos con  | Expresiones regulares muy complejas o cadenas demasiado largas.             | Difíciles de mantener y debuggear.         |
| `Validators.min`/`max`    | Para valores numéricos, pero puede generar confusión sin control adicional. | Usar con cuidado en formularios numéricos. |

> **Tip:** Utiliza validadores integrados siempre que el caso lo permita. Reserva los custom y asíncronos para necesidades específicas.

---

¡Con esta clasificación rápida, tendrás claro cuándo y cómo usar cada tipo de validador en tus formularios reactivos! 🎯

