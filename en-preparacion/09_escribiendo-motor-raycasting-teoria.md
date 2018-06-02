# Escribiendo un motor de Raycasting (Teoría). Parte 1
### Mayo 2018.

![Raycasting Parte 1](http://nicoavila.s3.amazonaws.com/articulos/09_01escribiendo-motor-raycasting-pt1.jpg)

Nunca olvidaré la primera vez que jugué [Wolfstein3D](https://en.wikipedia.org/wiki/Wolfenstein_3D) de niño: me quedé maravillado por los gráficos "3D" que en ese entonces se mostraban en mi monitor CRT (y un poco conmovido por la violencia del juego, pero aun así era genial y no podía parar de jugarlo!).  
En ese tiempo era dueño de un flamante computador equipado con un CPU **Intel 80286**, o comúnmente llamado [286](https://es.wikipedia.org/wiki/Intel_80286) que normalmente no superaba los 25mHz. Windows no existía (o al menos yo no tenía). Utilizaba MS-DOS 5.1 para operar mi PC.  
Recuerdo que mi padre hizo un agujero en la pared para "empotrar" el computador: era genial!.  
Para los que no conocen el título, *Wolfstein* es un juego publicado por [iD Software](https://www.idsoftware.com/?/age_gate) en 1992 que trata de un prisionero que debe escapar de una prisión infestada de Nazis. Es uno de los juegos precursores de los *First Person Shooter* modernos.  
Fué desarrollado por [John Carmack](https://en.wikipedia.org/wiki/John_Carmack), [John Romero](https://en.wikipedia.org/wiki/John_Romero) y [Tom Hall](https://en.wikipedia.org/wiki/Tom_Hall) en un pequeño estudio en Wisconsin, EEUU.

![Wolfstein3D](http://nicoavila.s3.amazonaws.com/articulos/09_02wolfstein3d.jpg)

El 2014, llegué por casualidad al blog de un desarrollador muy talentoso llamado [Fabien Sanglard](http://fabiensanglard.net/) buscando post-mortems de juegos antiguos (particularmente el de Doom 3, del cual también soy fanático). Fabien aborda conceptos extremadamente técnicos de una forma que es muy fácil entenderlos. Ahi fué cuando me enteré que estaba trabajando en un libro llamado **Game Engine Black Book: Wolfstein3D**. Tres años después, el 2017, se publica el libro el cual me apresuro a comprar en [Amazon](https://www.amazon.com/Game-Engine-Black-Book-Wolfenstein/dp/1539692876/ref=sr_1_1?ie=UTF8&qid=1527479654&sr=8-1&keywords=game+engine+black+book) para poder leerlo. TL;DR: El libro es maravilloso, lo recomiendo muchísimo :+1.

Wolfstein3D y otros títulos utilizan una técnica llamada **Raycasting** para simular, mediante transformaciones lineales, gráficos 3D a partir de gráficos 2D. El raycasting hace uso de rayos, los cuales son emitidos desde un punto en un espacio bidimensional. La colisión de los rayos son evaluadas para determinar la posición del jugador en el mundo y así realizar el [rendering](https://en.wikipedia.org/wiki/Rendering_(computer_graphics)) de la escena.

![Ejemplo Raycasting](http://nicoavila.s3.amazonaws.com/articulos/09_03ejemplo-raycasting.gif)

He tenido una fascinación un poco extraña por la rama de *computer graphics*: he leído mucho del tema, creo que es apasionante, pero nunca me he ensuciado las manos con algo de código al respecto. Creo que es tiempo de cambiar eso escribiendo de una vez por todas un *motor de raycasting*. Estos motores tienen una complejidad básica en cuanto a los cálculos que hay que realizar para dibujar algo y creo que es un buen ejercicio para ahondar más sobre el tema y desempolvar mis libros de una vez por todas :smiley:.

Esta es la primera parte de una serie de dos publicaciones. Particularmente abordaré los aspectos teóricos y algorítmicos de escribir un motor de raycasting, para luego, en una segunda entrega, implementar el mismo motor en JavaScript :heart:

## Referencias

* Lode's Computer Graphics Tutorial: [Web](http://lodev.org/cgtutor/raycasting.html).

* Ray-Casting Tutorial For Game Development And Other Purpose: [Web](https://permadi.com/1996/05/ray-casting-tutorial-table-of-contents/)

* Game Engine Black Book: Wolfstein 3D: [Amazon](https://www.amazon.com/Game-Engine-Black-Book-Wolfenstein/dp/1539692876/ref=sr_1_1?ie=UTF8&qid=1527479654&sr=8-1&keywords=game+engine+black+book)


