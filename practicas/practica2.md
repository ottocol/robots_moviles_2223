# Práctica 2 de Robots Móviles. Mapeado y localización

En esta práctica vamos a trabajar con varios algoritmos de localización y mapeado de robots móviles que iremos viendo en las sesiones teóricas.

La práctica tiene varias partes: para la primera no es necesario que escribáis código, solo que probéis los algoritmos ya implementadas en ROS. Para la mayoría de las partes adicionales tendréis que escribir algo de código, en principio en Python aunque podríais hacerlo en otro lenguaje.

Para realizar los requisitos mínimos tendréis que consultar referencias adicionales, aquí no se va a explicar cómo crear mapas y localizarse en ROS ya que la idea es que consultéis la información vosotros mismos. En el [apéndice 1](#apendice_1) tenéis unas cuantas referencias, pero podéis usar otras que encontréis en Internet, libros, etc. si lo deseáis.


## Construcción de mapas del entorno (requisito mínimo)

> Para esta parte podéis usar el simulador y el robot que queráis (Stage con Turtlebot 2, Gazebo con Turtlebot 2, Gazebo con Turtlebot 3, otros robots si los instaláis por vuestra cuenta...)

En esta primera parte de la práctica nos centraremos en la generación de mapas 2D del entorno. Este mapa se utilizará posteriormente para la localización del robot. Utilizaremos los paquetes por defecto en ROS para mapeado. Se usa un filtro de partículas tipo "FastSLAM" o "Rao-Blackwellizado". Este algoritmo lo veremos en clase de teoría con posterioridad . No obstante no es necesario entender su funcionamiento para probarlo. 

El mapa lo va construyendo el robot a medida que se va moviendo. Para el manejo del robot se recomienda usar la teleoperación mediante teclado, ya que así podemos ir a la velocidad que nos parezca mejor y dirigirlo por donde queramos. Como es lógico el robot no puede mapear aquellas partes del entorno por donde no ha pasado, y el mapa saldrá peor si nos movemos más rápido.

Prueba el mapeado como mínimo en un par de entornos distintos (por ejemplo un edificio con habitaciones y un entorno abierto con varios obstáculos)

Pon en la documentación imágenes con los mapas obtenidos y contesta a estas preguntas: 

- ¿Qué resolución tienen los mapas obtenidos? (está en el fichero del mapa, busca en las referencias o en internet dónde aparece esa información)
- ¿Crees que hay cierto tipo de entornos en los que funciona mejor? (espacios abiertos, espacios pequeños, pasillos,...)


## Localización y navegación (requisito mínimo)

> Para esta parte podéis usar el simulador y el robot que queráis (Stage con Turtlebot 2, Gazebo con Turtlebot 2, Gazebo con Turtlebot 3, otros robots si los instaláis por vuestra cuenta...), pero tendrá que ser el mismo que con el que habéis generado el mapa.


Una vez tenemos un mapa, lo podemos usar para localizar al robot (saber dónde está en todo momento) y para navegar por el entorno: ir del punto donde está el robot a un destino cualquiera, planificando la mejor trayectoria y sin chocar con obstáculos por el camino. En la barra de botones de la parte superior de `rviz` tenemos dos botones para esto:

- `2D pose estimate` nos permite marcar la posición (clic con el ratón) y la orientación (*arrastrar* el ratón) donde se encuentra ahora mismo el robot. Esto es necesario para poder inicializar el algoritmo de localización que es un filtro de partículas. Este algoritmo lo veremos en clase de teoría el día 3 de noviembre, aunque intuitivamente es fácil de entender, la nube de "flechitas" verdes representa las posiciones y orientaciones más plausibles para el robot en el instante actual, si el algoritmo funciona bien esta nube irá siguiendo la posición real del robot en todo momento, y cuanto mejor localizado esté el robot más "condensada" estará la nube.
- `2D Nav Goal`: para marcar el punto al que queremos que se mueva el robot. Primero tenemos que asegurarnos de que el robot está localizado (que la "nube" de flechitas verdes está en torno a la posición real. Si hay una trayectoria posible, aparecerá dibujada en rviz y el robot se irá moviendo por ella.

No obstante **estos botones no funcionarán si no está cargado en memoria el mapa y los nodos de ROS necesarios** para la planificación de trayectorias y evitación de obstáculos. Las instrucciones concretas para hacer esto dependen del robot y del simulador empleado, tendrás que buscarlas por Internet o consultar las referencias del [apéndice 1](#apendice_1)

Preguntas a contestar en este apartado:

- Investiga qué significa esa especie de "recuadro de colores" que aparece en `rviz` 	rodeando al robot cuando se pone a calcular la trayectoria y se va moviendo ¿qué significan los colores cálidos/frios?
- Los algoritmos que usa ROS para calcular la trayectoria óptima desde el origen hasta el destino están implementados en el paquete de ROS `global_planner`. Investiga qué diferentes algoritmos se usan en este paquete, si alguno te suena de haberlo dado en otras asignaturas del grado, describe en un párrafo cómo funcionaban y en cuál o cuáles asignaturas los viste.
- El "navigation stack" son los nodos de ROS que necesitamos cargar en memoria para que funcione la navegación. Averigua algo más sobre estos nodos, pon los nombres y describe brevemente el papel de cada uno en 1-2 frases.

## Parte adicional: Modelo de movimiento (hasta 0,75 puntos)

Se trata de implementar la versión muestreada del modelo de movimiento basado en odometría. El algoritmo visto en clase está en la transparencia titulada [**"Modelo de movimiento de odometría muestreado"**](https://moodle2022-23.ua.es/moodle/pluginfile.php/138578/mod_resource/content/0/tema2.2.%20modelo%20error%20movimiento.pdf#page=23) del tema 2, parte 2.

Se os proporciona en moodle una plantilla de código para que todos implementéis  las funciones con los mismos parámetros. La implementación se debe realizar en el archivo `modelo_movimiento.py`. Se incluye un pequeño programa que hace un gráfico del modelo para que podáis comprobar visualmente que está bien implementado. 

En este apartado además de la implementación deberías cambiar los parámetros del modelo (los alfa) y incluir distintas figuras con los resultados, discutiendo brevemente el por qué de las diferencias entre ellas.

Mirad el LEEME y los comentarios al fuente de la plantilla para más instrucciones.

## Parte adicional: Modelo de sensor tipo *distancia-orientación* (hasta 0,75 puntos)

Se pide implementar un **modelo del sensor para un sensor basado en landmarks**, que detecte la distancia y orientación a un landmark. El algoritmo está en la transparencia titulada "Modelo de sensor de distancia y orientación" del tema 2, parte 3. Supondremos resuelto el problema de la asociación de datos (el sensor ya sabe a qué landmark corresponde la medida).

La plantilla con el formato de la función a implementar es la misma que en el apartado anterior. La implementación se debe realizar en el archivo `modelo_sensor.py`. Se incluye un  programa que hace un gráfico del modelo para que podáis comprobar que está bien implementado. 

En este apartado además de la implementación deberías cambiar los parámetros del modelo (SIGMA_D y SIGMA_ALFA) y incluir distintas figuras con los resultados, discutiendo brevemente el por qué de las diferencias entre ellas.


Mirad el LEEME y los comentarios al fuente de la plantilla para más instrucciones.

## Parte adicional: implementar un filtro de partículas en ROS (hasta 1.5 puntos)

> Para hacer esta parte adicional necesitáis haber implementado el modelo de movimiento y el modelo del sensor

Se pidee **implementar en ROS un algoritmo de localización con filtro de partículas** usando el modelo de movimiento y del sensor de los puntos anteriores. Se dejará en moodle una plantilla con instrucciones, para que no tengáis que escribir el algoritmo desde cero.


## Parte adicional: probar el mapeado en los robots reales (hasta 1 punto)

Los días 2 y 9 de noviembre se podrá probar el mapeado y la localización en los Turtlebot reales. Se harán turnos para que se puedan usar los robots en grupos más reducidos, os puede tocar hacer la prueba uno de estos dos días (pero no ambos). La lista de turnos se elaborará durante la semana del 17 al 23 de octubre. 

## Entrega de la práctica

### Baremo

- **Requisitos mínimos (hasta un 6)**: la práctica debe estar adecuadamente documentada, respondiendo como mínimo a las preguntas que se plantean y detallando los resultados de todos los experimentos realizados. 
- **(hasta 0.75 puntos adicionales)**: implementación del modelo de movimiento.
- **(hasta 0.75 puntos adicionales)**: implementación del modelo del sensor.
- **(hasta 1.5 puntos adicionales)**: probar alguna implementación ya hecha de un algoritmo de filtro de partículas para localización. Deberías
	+ Realizar varias pruebas con el algoritmo variando los parámetros que puedas (ccomo mínimo el número de partículas)
	+ Explicar con el máximo detalle que puedas cómo funciona el código identificando las partes que hemos visro en clase (modelo del sensor, modelo de movimiento, bucle del algoritmo, resampling...)
- **(hasta 1 punto adicional)** probar el mapeado en los Turtlebot reales.
- En lugar de alguno de los puntos anteriores se puede hacer cualquier otra ampliación que se os ocurra, por ejemplo
	+ Probar un algoritmo ya implementado de mapeado y/o localización que no sea de los vistos en clase, hacer experimentos, compararlo con los que vemos nosotros, ...
	+ Leer un artículo científico sobre mapeado/localización (estado actual de la investigación, algoritmos alternativos...) haciendo un resumen del contenido. 
	+ **Consultad con el profesor** lo que queréis hacer para ver cuánto se podría valorar en el baremo, si queréis que os pase enlaces a artículos o algoritmos, etc.


### Plazos y procedimiento de entrega

La práctica se podrá entregar hasta las 23:59 horas del **martes 15 de Noviembre del 2022**.

La entrega se realizará a través del Moodle de la asignatura.

## <a name="apendice">Apéndice: Referencias sobre mapeado y localización en ROS</a>

Para realizar los requisitos mínimos necesitaréis consultar información adicional sobre localización y mapeado en ROS, ya que en clase de prácticas no vamos a explicar las instrucciones exactas para realizarlo. No tenéis por qué seguir exactamente estas referencias que ponemos aquì, podéis usar cualquier otra si os gusta más.

> Como recomendación general, fijaos en que la biblioteca de la UA tiene disponibles muchos [libros en formato electrónico](https://ua.on.worldcat.org/search?databaseList=1953%2C1931%2C1875%2C1941%2C2259%2C2237%2C3313%2C2375%2C239%2C2570%2C638%2C2260&queryString=ros+robots&clusterResults=true) sobre ROS.

### Turtlebot 2

Si tenéis instalado el soporte de Turtlebot 2 podéis consultar estas referencias. Recordad que los ordenadores de laboratorio tienen este soporte instalado.

> **IMPORTANTE**: Los ficheros de configuración de Stage para Turtlebot 2 que están preparados para mapeado y navegación usan en lugar de un laser una kinect simulada, con un campo de visión muy reducido. En el moodle tienes los ficheros modificados para ampliar el campo de visión al que tienen los turtlebot reales, usa estos, los mapas deberían salir mucho mejor.

- Para mapeado y localización/navegación en **Stage** los capítulos 9 y 10 respectivamente del libro *"Programming Robots with ROS"*, de Quigley, Gerkey y Smart, ed. O'Reilly, 2015. El capítulo 9 os puede servir entero y el 10 hasta la sección "Navigating in code", no incluído. Podéis [consultarlo *online*](https://linker2.worldcat.org/?jHome=https%3A%2F%2Fbua.idm.oclc.org%2Flogin%3Furl%3Dhttps%3A%2F%2Flearning.oreilly.com%2Flibrary%2Fview%2F%7E%2F9781449325480%2F%3Far&linktype=best) por medio de la biblioteca de la UA. 
- Para **Gazebo** podéis seguir por ejemplo [este tutorial online](https://learn.turtlebot.com/2015/02/03/8/).

### Turtlebot 3

La empresa Robotis, fabricante de los servos que llevan los Turtlebot 3 tiene un manual bastante completo *online* con una sección para [SLAM simulado](https://emanual.robotis.com/docs/en/platform/turtlebot3/slam_simulation/) y otra para [navegación](https://emanual.robotis.com/docs/en/platform/turtlebot3/nav_simulation/) (aseguráos de seleccionar la versión "Noetic" en la barra superior)

En el libro "ROS robot programming", que está escrito por los desarrolladores del Turtlebot 3 y está [disponible en Internet de manera gratuita](https://community.robotsource.org/t/download-the-ros-robot-programming-book-for-free/51) puedes encontrar más información en el capítulo 11. Aunque aquí lo hace con un turtlebot de verdad, para hacerlo en simulación básicamente se trata de sustituir el comando `roslaunch turtlebot3_bringup turtlebot3_robot.launch`, que arranca el robot real por `roslaunch turtlebot3_gazebo turtlebot3_world.launch`que arranca la simulación.

