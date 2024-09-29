# blog-robotica-movil-24-25-anavarredonda
-----------------------------------------
# P1 - Vacuum Cleaner

## Descripcion del algoritmo
Para esta practica era necesario la implementacion de una maquina de estados, en mi caso he decidido implementarla con 5 estados distintos, estos son:
  1. Estado Initial
  2. Estado advance
  3. Estado right
  4. Estado left
  5. Estado reverse

Imagen con estados y transiciones entre estados:
https://urjc-my.sharepoint.com/personal/a_navarredonda_2022_alumnos_urjc_es/_layouts/15/embed.aspx?UniqueId=0f6c49bc-27f7-49e9-ba41-5e05b985c091

El estado en el que la maquina de estados iniciara sera en el estado initial, en este estado el roboto girara dibujando una espiral creciente hasta toparse con un obstaculo que detectara el bumper. En funcion de que parte del bumper detecte la colision, pasara al estado reverse(colision frontal), estado left(colision en la derecha del bumper), o estado right(colision en la parte izquierda del bumper). Este estado dibuja una espiral creciente con la intencion de aspirar una gran cantidad de area antes de toparse con el primer obstaculo. Tras el primer cambio de estado, la maquina de estados nunca mas volvera a este estado inicial.

El estado left consiste en que el robot detenga su moviento lineal, y haga un moviento rotatorio hacia la izquierda, el robot entrara en este estado cuando el bumper derecho se active, este estado es alcanzable desde los estados initial, y advance. El estado cambiara a advance una vez que el bumper derecho deje de activarse.

El estado right consiste en que el robot detenga su moviento lineal, y haga un moviento rotatorio hacia la derecha, el robot entrara en este estado cuando el bumper izquierdo se active, este estado es alcanzable desde los estados initial, y advance. El estado cambiara a advance una vez que el bumper izquierdo deje de activarse.

El estado reverse consiste en que el robot de marcha atras a la vez que gire hacia la derecha o izquierda, hacia que lado gire el robot mientras da marcha atras es aleatorio con un 50% de probabilidad a girar hacia cualquiera de los dos lados.
El robot llegara a este estado tras la activacion del bumper frontal, y podremos llegar desde los estados de initial, y advance. El estado transicionara hacia el estado advance una vez que el bumper deje de estar activado.

Por ultimo tenenemos el estado advance, en este estado el robot avanza en linea recta, hasta que pasados 2 segundo desde la activacion de este estado, pasara a avanzar hacia delante pero con una ligera rotacion hacia la izquierda(la explicacion del porque este comportamiento estara en el apartado Problemas encontrados y soluciones para ellos). 
Se alcanzara este estado desde los estados right, left, y reverser, y se activara cuando ninguno de las tres partes del bumper esten activas, pasara al estado left(cuando se active el bumper derecho), al estado right(cuando se active el bumper izquierdo), o al estado reverse(cuando se active el bumper frontal).

## Problemas encontrados y soluciones para ellos
Un problema que encontre es que habia casos en los que el robot se quedaba bloqueado en una misma zona, recorriendola una y otra vez, para solucionar esto decide que en el estado advance, pasados dos segundo, enpezara a girar ligeramente a la izquierda, de tal forma que se rompiera el rectangulo que el robot dibujaba una y otra vez, pudiendo pasar a nuevas zonas.

No tanto un problema sino una mejora significativa que hice fue agregar un estado inicial que dibujase una espiral creciente, anteriormente la maquina de estados no disponia de este estado, por lo que iniciaba directamente en el estado advance, esto provocaba que fuera en linea recta contra el primer obstaculo. Agregando este estado, he conseguido que el area recorrida antes del primer obstaculo aumente.

Otro problema que tuve fue en el estado reverse, al principio cuando daba marcha atras el robot siempre giraba a la izquierda, por lo que habia situaciones en las que no podia salir quedandose bloquedo, para solucionarlo agrege que el robot al dar marcha atras pudiera girar a derecha o izquierda de manera aleatoria.

## Video demostracion del algoritmo

