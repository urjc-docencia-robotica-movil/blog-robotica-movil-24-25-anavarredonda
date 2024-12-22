# blog-robotica-movil-24-25-anavarredonda
-----------------------------------------
# P5 Monte Carlo laser Localization

## Descripcion del algoritmo
En esta practica se pedia implementar el algoritmo de autolocalizacion MCL (Monte Carlo Localization), este algoritmo se divide en las siguientes partes:
  1. Inicializacion de las particulas
  2. Propagacion de las particulas
  3. Generacion de los laseres virtuales de cada particula
  4. Comparacion entre laser real y virtual, para asignar pesos a las particulas
  5. Algoritmo de la ruleta para seleccionar el lugar de nacimiento de la siguiente iteracion de particulas

### 1. Inicializacion de las particulas
En esta parte se crean las primeras particulas, estas estaran creadas aleatoriamente alrededor del mapa, en esta implementacion, las particulas no pueden ser inicializadas en lugares con obstaculos en el mapa.

### 2. Propagacion de las particulas
Una vez tenemos las particulas creadas, ya sean las particulas iniciales o las particulas creadas a partir del algorimo de la ruleta en la iteraccion anterior, estas deben de propagarse junto con el robot, para ello obtendremos cuanto se ha movido el robot de los encoders, y le agregaremos un ruido a este valor, esto se agrega debido a los errores que pueda tener los encoders, asi como hacer que el algoritmo sea capaz de explorar zonas cercanas en busca de mejores relaciones entre los laseres virtuales y reales.

### 3. Generacion de los laseres virtuales de cada particula
Se generara laseres virtuales desde cada particula, el numero de laseres lanzados desde cada particula es uno de los parametros del algoritmo, estos laseres simularan el laser real en busca de la distacia a los distintos obstaculos, otro de los parametros del algoritmo se el numero de saltos durante la creacion del rayo laser, es decir que en vez de simular todos las casillas en donde se lanzara el laser, que simule una si y otra no, estos parametros ayudaran a reducir la carga computacional del algoritmo.

### 4. Comparacion entre laser real y virtual, para asignar pesos a las particulas
Una vez que se tiene los laseres virtuales simulados, compararemos las distancias encontradas por los laseres virtuales y los laseres reales, vemos la diferencia entre real y virtual, y en funcion de esa diferencia asignamos el peso de cada particula, a menor error mayor peso, pues mas parecido es el laser simulados con el real.

### 5. Algoritmo de la ruleta para seleccionar el lugar de nacimiento de la siguiente iteracion de particulas
Una vez que todas las particulas tienen un peso asignado, normalizamos estas probabilidades para que esten en el intervalo [0-1], una vez hecho la normalizacion elegiremos al azar, basado en los pesos de las particulas, las posiciones de las nuevas particulas, a partir de aquellas particulas que tenian mas peso, naceran mas particulas.

## Problemas encontrados y soluciones a ellos
El principal problema que he encontrado es a la hora de comparar los laseres reales y virtuales, pues no estaba comparando entre los angulos correcos, sino que cogia cada laser virtual y lo comparaba con uno real, y luego con el siguiente real, es decir que si tenia 10 laseres estaba comparando esos 10 laseres virtuales con los primeros 10 grados del laser real, para solucionarlo basta con comparar entre laseres del mismo angulo.

## Video demonstracion del algoritmo
https://urjc-my.sharepoint.com/:v:/g/personal/a_navarredonda_2022_alumnos_urjc_es/Efvm8b44NPxGp0IGk-33tL4BzC5QjxOd8MIsxB3ta2g5Ig?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=aK5MjH

----------------------------------------------------------------------------------------------
# P4 Global Navigation

## Descripcion del algoritmo
En esta practica se pedia implementar el algoritmo de navegacion global Gradient Path Planning, para esta practica disponiamos de un mapa, sobre el que crearemos el cost grid que se utilizara para navegar.

Lo primero que se debe de implementar es la seleccion de objetivo que el coche debera de ancalzar, tras esto, desde ese punto seleccionado, lanzaremos el algorirmo de busqueda en anchura para recorrer el mapa hasta llegar a la posicion del coche, mientras que buscamos la posicion del coche crearemos un cost grid en el que iremos sumando el coste hasta el objetivo. Es muy importante normalizar los valores, pues el valor maximo de coste que podremos mostrar es de 255, por lo que si no normalizamos cuando llegue a 255 empezara de nuevo en 0 creando un corte en la imagen que se muestra, aunque no en el cost grid. 
Mientras que creamos el cost grid, el coste que sumaremos a celdas que hallamos recorrido sera de 1 si hemos llegado a la celda de manera recta, y 1.4 (raiz de 2) en caso de que hallamos llegado de forma diagonal.

Una vez que tenemos el cost grid hecho, el siguiente paso es engordar el coste cerca de los obstaculos, para evitar que el coche se acerque peligrosamente a ellos, o incluso choque. Para esto deberiamos de haber guardado durante la creacion del cost grid la ubicacion de los obstaculos, teniendo los obstaculos lo que hago yo es ver hacia que lado del obstaculo se encuentra la calle, y engordar un 4 casillas hacia delante. 
Sin embargo con esto no basta, pues las esquinas no estaran engordadas, por lo que para engordar las esquinas deberemos de identificar obstaculos que tengan calle por dos lados, y engordar diagonalmente.

El ultimo paso es usar el cost grid con los obstaculos engordados para navegar sobre, para esto creamos una submatriz 10x10, a partir de cost grid, alrededor de la posicon del coche, y encontramos el coste minimo dentro de esa submatriz.
Una vez tengamos el valor minimo dentro de la submatriz, deberemos de encontrar sus coordenadas en el cost grid, y calcularemos con estas coordenadas y la posicion del coche el angulo a girar para llegar a ese punto, como cuanto menor sea el coste, mas cerca estaremos del objetivo, al final llegaremos al objetivo.

## Problemas encontrados y soluciones para ellos
El principal problema que he tenido ha sido con las esquinas a la hora de engordar el cost grid, pues no encontraba forma de engordar las diagonales de manera eficiente sin sobrecargar los costes de otras partes del cost grid como pueden ser las rectas, finalmente lo que hago es distinguir las esquinas por separado, engordar la diagonal, y alredodor de la diagonal formando un cuandrado que esta en la esquina.

Otro problema recurrente que tuve fue la inversion de indices a la hora de acceder a las matrices, pues como al mapa tiene las coordenadas iniciadas en la esquina superior izquierda, a menudo me encontraba con que las cosas no coincidian. Por suerte tiene facil solucion, invertir los indices para acceder.

Por ultimo, a la hora de navegar con el coche es muy importante normalizar el angulo entre -pi y pi, para no tener problemas con angulos superiores a 360 grados.

## Video demostracion del algoritmo
https://urjc-my.sharepoint.com/:v:/g/personal/a_navarredonda_2022_alumnos_urjc_es/EfWecIicX3ZGpB14AXB_OjABjrfW4phZB9dr1mX0NfMNNg?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=aV7kpR

----------------------------------------------------------------------------------------------
# P3 Obstacle Avoidance

## Descripcion del algoritmo
En esta practica se pedia implementar el algoritmo de navegacion local VFF, para esta practica disponiamos de los objetivos que el coche debe de alcanzar y un laser con el que detectaremos los obstaculos.

Empece implementando el vector atractivo, para ello debemos de obtener el objetivo que nos dan, y pasarlo a coordenadas relativas al coche, una vez hecho esto bastaria con limitar el modulo del vector, en mi caso a 5, para evitar que se sienta extremadamente atraido por objetivos alejados.

Tras esto, implemente el vector repulsivo, para este vector se tiene que tener en cuenta todos los valores obtenidos por el laser, extraemos los vectores desde el coche hasta cada deteccion del laser, tras esto debemos de modificar el modulo para que cuanto mas cerca este del coche mas grande sea el modulo, para esto yo use la siguiente formula: 0.18 / (1 + e^dist), esta formula la obtuve en la aplicacion de GeoGebra lo que me permitio ver la evolucion de la curva para seleccionar la que mas se adaptaba a lo que se necesitaba. Tras esto debemos de darle la vuelta a el vector para que este apuntando hacia el sentido contrario del obstaculo, pues nos queremos alejar de el, para esto durante el cambio a coordenadas geometricas, sumamos pi al angulo en la obtencion de la coordenada x. 
Tras esto ponderaremos los vectores obtenidos dependiendo de donde estan los obstaculos que los han generado, puesto que si el obstaculo esta delante del coche sera mas relevante que si el obstaculo se encuentra a los lados del coche, es por esto que aumentaremos el valor de los vectores generados a partir de obstaculos delante del coche, y disminuiremos el valor de los vectores que se encuentran a los lados del coche.
Por ultimo haremos la media de todos los valores obtenidos que sera nuestro vector repulsivo resultante.

Por ultimo calcularemos el valor del vector total, cogeremos el vector atractivo y lo sumaremos al vector repulsivo, tras esto limitaremos el modulo del vector total. 
Una vez obtenido el vector total, usaremos sus valores xy para las velocidades angulares y lineares.

## Problemas encontrados y soluciones para ellos
El primer problema fue como hallar una formula para el modulo repulsivo que haga que cuanto mas cerca del obstaculo mas grande sea el modulo, para ello use la aplicacion GeoGebra que me permitio ver la curva que seguiria el modulo, para seleccionar la mejor formula.

Otro problema que tuve relacionado tambien con el vector repulsivo es que se daba la vuelta a ambas coordenadas, provocando que el vector estuviera dado la vuelta pero apuntando hacia el lado que no era, basto con eliminar el giro de la coordenada y.

Tambien tuve un problema relacionado con la velocidad angular, puesto que le ponia la misma velocidad angular que la coordenada y del vector total, provocaba que girara muy violentamente, por lo que dividi la coordenada entre 3.5 para que el giro maximo fuese mas suave.

## Video demostracion del algoritmo
https://urjc-my.sharepoint.com/:v:/g/personal/a_navarredonda_2022_alumnos_urjc_es/EbamzHGV54xAqxhBzx7ecDMBaBoXCKbYO1PcoMypTMY44w?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=DtM6l5

----------------------------------------------------------------------------------------------
# P2 - Follow Line

## Descripcion del algoritmo
En esta practica se pedia implementar un sigue lineas a traves de una camara, implementando dos modelos diferentes, un modelo heuristico y uno no heuristico, para ellos en ambos modelos se debe de filtrar primero la imagen proporcionada por la camara.
Para filtrar la imagen he utilizado un filtro HSV pues es mas fiable que un filtro RGB, con este filtro obtenemos una imagen solo con la linea roja a seguir.
Una vez que hemos obtenido la imagen filtrada, debemos de elegir en que parte de la imagen debemos de fijarnos, para ello he eligido la fila 30 a partir del medio de la imagen, por lo que tenemos una porcion de la linea que permite conocer bien lo que se encuentra adelante del coche.

Una vez que tenemos la porcion de la linea que vamos a tomar, encontramos el punto medio de la linea estando de frente al coche, en este caso esta en la columna 319 debido a que la camara esta ligeramente desplazada a la izquierda, tomaremos esta columna como referencia, tras esto debemos de obtener todos los pixeles rojos en la linea, y obtener el punto medio de esa franja roja, lo que indicara el punto medio de la linea roja actualmente, comparamos el punto medio obtenido de la linea con la referencia, por lo que obtenemos el numero de pixeles de error que tiene el coche respecto a la imagen, con este error podemos implementar un PID.

Para la implementacion del PID, he implementado un PID que controla la velocidad angular, y otro para la velociada lineal.
Para la velocidad angular he seguido la formula clasica del PID de (Kp * error + Kd * (error - prev_error)) * angular_velocity, con lo que dependiendo del signo del error, que indicara hacia que lado se ha perdido la linea, girara hacia la derecha o la izquierda buscando a la linea.
Para la velocidad linear he implementado una velocidad base, y la velocidad calculada por el controlador se le resta: linear_velocity - min(abs( Kp_V * error + Kd_V * (error - prev_error)), 13). Ademas de que no queremos que el coche vaya hacia atras por lo que nos quedamos siempre con valores positivos, y no queremos que el coche se detenga, por lo que establecemos un maximo de 13 que se le puede restar  a la velocidad base, con esto hacemos que el controlador frene el coche cuando hay error, es decir cuando se esta en una curva.

Ademas en caso de que se pierda la imagen, he implementado un metodo de recuperacion que permite encontrar la linea, que consiste en dar marcha atras ligeramente, y girar buscando la linea por el ultimo lado que se ha visto.

## Problemas encontrados y soluciones para ellos
El principal problema de este problema era ajustar los PIDS en funcion del modelo, para evitar que oscile excesivamente, mientras que da la vuelta al circuito de manera rapida.

Otro problema que tuve fue que el error obtenido es demasiado grande, pues las velocidades estan en m/s, y el error en pixeles, por lo que para solucionarlo dividi el error entre 250.

## Video demostracion del algoritmo
No ackerman: https://urjc-my.sharepoint.com/:v:/g/personal/a_navarredonda_2022_alumnos_urjc_es/Eai5IgNSob9HjgKcfpBlMRoB4AuFBo7ytgdBm3kv1j9sqw?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=o1pn25

Ackerman: https://urjc-my.sharepoint.com/:v:/g/personal/a_navarredonda_2022_alumnos_urjc_es/Ea_WNXhrpQNDlo3kiGy7H8cBg_sx4I7lau-vObOwX0QdvA?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=Q2K4NL

----------------------------------------------------------------------------------------------
# P1 - Vacuum Cleaner

## Descripcion del algoritmo
Para esta practica era necesario la implementacion de una maquina de estados, en mi caso he decidido implementarla con 5 estados distintos, estos son:
  1. Estado Initial
  2. Estado advance
  3. Estado right
  4. Estado left
  5. Estado reverse

Imagen con estados y transiciones entre estados:
https://urjc-my.sharepoint.com/:i:/g/personal/a_navarredonda_2022_alumnos_urjc_es/EbxJbA_3J-lJukFeBbmFwJEBq9IL9OpzEt8mvHaoFNRqPg?e=W2UtYv

El estado en el que la maquina de estados iniciara sera en el estado initial, en este estado el roboto girara dibujando una espiral creciente hasta toparse con un obstaculo que detectara el bumper. En funcion de que parte del bumper detecte la colision, pasara al estado reverse(colision frontal), estado left(colision en la derecha del bumper), o estado right(colision en la parte izquierda del bumper). Este estado dibuja una espiral creciente con la intencion de aspirar una gran cantidad de area antes de toparse con el primer obstaculo. Tras el primer cambio de estado, la maquina de estados nunca mas volvera a este estado inicial.

El estado left consiste en que el robot detenga su moviento lineal, y haga un moviento rotatorio hacia la izquierda, el robot entrara en este estado cuando el bumper derecho se active, este estado es alcanzable desde los estados initial, y advance. El estado cambiara a advance
 una vez que el bumper derecho deje de activarse.

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

https://urjc-my.sharepoint.com/:v:/g/personal/a_navarredonda_2022_alumnos_urjc_es/EWCSoJZpicdGuwbzXWCzGtEBVMlkSrPbFoXRlx52I1Z3aA?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=1HAb9C
