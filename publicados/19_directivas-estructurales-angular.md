# Directivas Estructurales en Angular
### Julio 2018

![Directivas Estructurales Angular](http://nicoavila.s3.amazonaws.com/articulos/19_01directivas-estructurales-angular.jpg)

[Angular](https://angular.io), al igual que otros framework de JS modernos, también tiene la característica de controlar los elementos del [DOM](https://developer.mozilla.org/es/docs/DOM) gracias a atributos especiales. Estos atributos son llamados **directivas estructurales**.  
En esta breve guía les mostraré cuales son esos atributos, que ocurre tras bambalinas y como podemos beneficiarnos de las directivas estructurales en Angular para manipular el DOM al desarrollar nuestras aplicaciones.

## ¿Qué son las Directivas Estructurales?
Las directivas estructurales corresponden a elementos en el HTML que permiten añadir, manipular o eliminar elementos del DOM. Estos elementos, en forma de atributos, se aplican a *elementos huéspedes*. Al hacer esto, la directiva hace lo que debe hacer sobre el elemento huesped y sus elementos hijos. Estas directivas son fácilmente reconocibles debido a que están antecedidas por un *asterisco* **(*)** seguido del nombre de la directiva.

![Acción directiva](http://nicoavila.s3.amazonaws.com/articulos/19_02accion-directivas.jpg)
Esquema que muestra la acción de la directiva *ngIf sobre un elemento huésped y sus hijos

## Tras bambalinas de las directivas: el prefijo *
El prefijo * que antecede a las directivas permite a Angular envolver al elemento huésped en una etiqueta ```<ng-template>``` durante el proceso de transpilación de Angular CLI.

![ng-template](http://nicoavila.s3.amazonaws.com/articulos/19_03ng-template.jpg)

Ocurren dos cosas:
* La directiva (```ngIf``` en este caso) pasa al elemento padre ```<ng-template>```.
* El elemento huesped (incluyendo todos sus atributos), pasan a ser hijos de ```ng-template```.

## Tipos de directivas
En Angular existen dos tipos de directivas:

* **Directivas de componente:** Una directiva de componente es aquella que administra una región de HTML de una manera nativa como un elemento HTML. Técnicamente es una directiva con un template.
* **Directivas de atributo:** Una directiva de atributo cambia la apariencia o comportamiento de un elemento, componente u otra directiva.

> Se pueden aplicar muchas *directivas de atributo* a un elemento huesped. Sin embargo solo es posible aplicar una *directiva estructural* a un elemento huesped

![Valido/Invalido](http://nicoavila.s3.amazonaws.com/articulos/19_04valido-invalido.jpg)

Angular posee 2 directivas estructurales:

* ***ngIf:** Permite mostrar / ocultar elementos del DOM.
* ***ngFor:** Permite ejecutar bucles sobre elementos del DOM.

La tercera directiva, ngSwitch, es una directiva de atributo (pero que depende de otras directivas estructurales para funcionar):

* ***ngSwitch:** Permite ejecutar casos condicionales sobre elementos del DOM.

A continuación describiremos en detalle cada directiva y un ejemplo de uso:

## *ngIf
Esta directiva toma una expresión booleana y hace que toda la porción del DOM aparezca o desaparezca dada esa condición.
![ngIf](http://nicoavila.s3.amazonaws.com/articulos/19_05ngif.jpg)

### Ejemplo
```html
<!-- Dado var variable = true. El elemento va a aparecer -->
<p *ngIf="variable">Este es un párrafo que se aparece</p>

<!-- Dado var variable = false. El elemento no va a aparecer -->
<p *ngIf="variable">Este es un párrafo que no aparece</p>
```

> Algo muy importante de mencionar, es que la directiva *ngIf hace que **aparezca o desaparezca** el elemento del DOM. Esto es totalmente distinto a ocultar un elemento con CSS (ej: display: none;), ya que el que elemento esté ausente indica que todo el *event binding* correspondiente a ese elemento estará ausente, mejorando así la performance de nuestra aplicación.  
Adicionalmente, es posible utilizar **operadores lógicos** para evaluar la condición booleana de *ngIf, tales como **&&**, **||**, etc.

## *ngFor
Esta directiva es un poco más compleja que *ngIf. *ngFor permite generar iteraciones de elementos HTML. Posee partes *obligatorias* y *opcionales*.
Las partes obligatorias:

* Declaración de la variable que contiene el valor de la iteración
* Utilización de palabra ```of```.
* Variable a iterar.

Las partes opcionales:

* Índice de la iteración
* Imprimir la variable que contiene el valor de la iteración con data binding.

![ngfor](http://nicoavila.s3.amazonaws.com/articulos/19_06ngfor.jpg)

### Ejemplo
```html
<!-- Dado la variable var canasto = ["Manzanas", "Peras", "Platanos", "Frutillas"]-->
<h1>Frutas en el canasto</h1>
<div *ngFor="let frutas of canasto">
    <p>El canasto contiene {{frutas}}</p>
</div>
```

## *ngSwitch
Esta directiva corresponde a una seria de directivas que cooperan entre si para generar un resultado. Estas directivas son ```ngSwitch```, ```ngSwitchCase``` y ```ngSwitchDefault```.
La directiva **ngSwitch** es una directiva de atributo, mientras que las directivas **ngSwitchCase** y **ngSwitchDefault** corresponden a directivas estructurales.

![ngSwitch](http://nicoavila.s3.amazonaws.com/articulos/19_06ngswitch.jpg)

```html
<!-- Dado la variable var estado = 1-->
<div [ngSwitch]="estado">
    <p *ngSwitchCase="1">El estado es 1</p>
    <p *ngSwitchCase="2">El estado es 2</p>
    <p *ngSwitchDefault>No es ningún estado conocido</p>
</div>
```
Espero que hayas disfrutado esta publicación tanto como disfruté yo redactándola :smile:. Recuerden que si tienen alguna duda pueden contactarme a través de mi Twitter @nicoavila_a.

Si te gustó la publicación, compártela y deja algunos claps :clap: