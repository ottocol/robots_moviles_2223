# Práctica evaluable 1: moverse evitando obstáculos

## Objetivo

**Desarrollad un programa en ROS que haga que el robot se mueva por el entorno evitando los obstáculos**.  No tiene por qué tener un destino determinado, simplemente "vagabundear" por el entorno. Aunque en la asignatura veremos algoritmos de evitación de obstáculos bastante sofisticados, no se pide nada de eso ya que el objetivo es simplemente que recordéis la forma de trabajar con ROS.

Como idea sencilla podéis mirar las lecturas que apuntan más o menos a la derecha y las que apuntan más o menos a la izquierda (en lugar de tomar una sola podéis coger la media de varias que estén en un ángulo similar). Si las lecturas de la izquierda son menores que las de la derecha hay que girar hacia la derecha, y viceversa. Podéis añadir mejoras, como por ejemplo:

 - Que la velocidad de giro sea mayor si la diferencia entre izquierda y derecha es mayor.
 - Que la velocidad lineal sea proporcional a la distancia al obstáculo más cercano.
 - ... Cualquier otra idea que se os ocurra.
 
 > **IMPORTANTE** la idea es si es posible, probar el código con los Turtlebot 2 del laboratorio, por lo que sería interesante que la probárais antes en un simulador que use un sensor de rango similar a los Turtlebot 2 del laboratorio. El simulador *stage* con el mundo de ejemplo y la configuración que os dejamps en moodle sería el más parecido en este sentido (consultad el apartado "Probando un robot en el simulador Stage" de los apuntes de "[Introducción a ROS como usuario"](intro_ROS_usuario.html). Aunque parezca contraintuitivo, el simulador de Turtlebot 2 con la configuración por defecto usa un sensor de rango muy distinto al laser de los robots reales y no es muy apropiado para este caso.

 > Cuidado, el nombre de los topics para los motores y el láser cambian entre Stage y el  Turtlebot real
 
> |         | Turtlebot real                   | Stage        |
|---------|----------------------------------|--------------|
| Láser   | `/scan`                          | `/base_scan` |
| Motores | `/mobile_base/commands/velocity` | `/cmd_vel`   |


## Ayuda para la implementación: sensores de rango 2D en ROS

Los sensores de rango 2D dan distancias a obstáculos en un rango angular determinado, normalmente los sensores apuntan hacia donde está mirando el robot. Podéis obtener la información del sensor normalmente en el topic `/scan` o `/base_scan`, dependiendo del robot o del simulador usado. Probad a ver cuál aparece al hacer un `rostopic list`.

Los mensajes en este *topic* son del tipo `sensor_msgs/LaserScan`. El campo más importante de estos mensajes es el array `ranges`, pero además hay otros campos importantes. Podéis consultarlos por ejemplo en [http://docs.ros.org/kinetic/api/sensor_msgs/html/msg/LaserScan.html](http://docs.ros.org/kinetic/api/sensor_msgs/html/msg/LaserScan.html). 

Dos campos interesantes son `angle_min` y `angle_max`, que como indica la página anterior son el ángulo donde empieza el scan y donde acaba. Teniendo en cuenta además el campo `angle_increment` podemos saber en qué ángulo apunta cada lectura del array ranges. La [0] apuntará a `angle_min`, la [1] a `angle_min+angle_increment`, y así sucesivamente.

Llevad cuidado porque en ROS el sentido de giro positivo es "a izquierdas" y eso da lugar a algunas consecuencias un poco antiintuitivas.

Al menos en los sensores simulados en Stage y en Gazebo con el Turtlebot 2, las primeras posiciones del array apuntan "a la derecha" del robot visto desde su punto de vista (ángulos negativos), y las últimas "a la izquierda" (ángulos positivos). El punto medio del array serían 0 radianes, la dirección en la que mira el robot. Por ejemplo en el scan del Turtlebot en gazebo va de -0,52 radianes aprox. a 0.52.

Esto es así para ser coherente con el sistema de coordenadas de ROS: a mayor ángulo "más giras a la izquierda" (en [https://answers.ros.org/question/198843/need-explanation-on-sensor_msgslaserscanmsg/](https://answers.ros.org/question/198843/need-explanation-on-sensor_msgslaserscanmsg/) tenéis una figura que lo explica mejor). 

En conclusión: para saber el ángulo de una lectura tenéis que hacer cuentas con `angle_min`, `angle_max` y `angle_increment`. 

En los Turtlebot 3 simulados en Gazebo las lecturas empiezan con angle_min=0 (al frente del robot) y terminan en 6.28 radianes (dan una visión de 360 grados alrededor del robot).

## Prueba del código en el Turtlebot real

El día 28 podéis probar el código en los robots. Cosas importantes:

- Como solo hay un Turtlebot por mesa, lo más sencillo es que os juntéis varios grupos para probar el código. No es necesario que probéis el código de todos, pero si podéis, mejor
- El turtlebot real tiene un láser con un campo de visión de 240 grados aprox.,  prácticamente igual que el que simulábamos en stage,
- El topic del laser es `/scan`, el de los motores es `/mobile_base/commands/velocity
- Los robots tienen ROS Indigo/Kinetic, que usa **Python 2**:
    * si tenéis algún print, no debe llevar paréntesis
    * si hay alguna ñ o acento en los comentarios, dará error, poned la siguiente línea al principio del archivo: # -*- coding: utf-8 -*-
- **Importante**: el laser está montado "boca abajo", por lo que el array "ranges" está al revés de como lo esperáis 😬. Para que funcione vuestro algoritmo creo que lo más sencillo es "invertir" el array lo primero de todo en el callback del láser, pero podéis hacerlo como queráis
```python
//callback del laser
def callback(msg):  
   msg.ranges = list(msg.ranges)
   msg.ranges.reverse()
   //a partir de aquí ya todo sería igual que en vuestro algoritmo...
   //...
```   

> CUIDADO: Copiad al robot solo el archivo .py con vuestra práctica 1. No es necesario el workspace entero (catkin_ws). **NO COPIEIS EL CATKIN_WS al robot ya que ya tiene uno, necesario para su funcionamiento y lo machacaríais**

En el robot poned en marcha solo los motores y el laser, la cámara no es necesaria. Recordad que las instrucciones completas están en la guía de laboratorio de los turtlebots.

Podéis documentar este apartado con 1-2 páginas máximo explicando:

- Qué ha funcionado y qué no
- Semejanzas y diferencias (si las hay) entre el comportamiento del robot en el entorno simulado y en el real
- Si habéis modificado algún parámetro del algoritmo o el código, incluid el código modificado explicando el por qué lo habéis cambiado (salvo el tema del `reverse` en el array, que tenéis que hacer todos igualmente)
- Adjuntad videos del comportamiento del robot para ilustrar gráficamente vuestras explicaciones


## Normas de entrega

La práctica se puede desarrollar **individualmente o bien por parejas**. 

> Podéis compartir con vuestros compañeros las ideas de cómo funciona el algoritmo (y de hecho si lo discutís y os dais ideas entre vosotros será más enriquecedor) pero el código Python o C++ debería ser exclusivamente vuestro. Sería recomendable que si habéis colaborado en el desarrollo del algoritmo os referenciéis unos a otros en la documentación entregada ("la idea del algoritmo ha surgido en colaboración con XXX e YYY..."). Así evitaréis que en caso de darme cuenta de la semejanza asuma que os habéis copiado.

La práctica se entregará a través de moodle. El plazo de entrega concluye el **martes 4 de octubre a las 23:59**. Si la hacéis por parejas solo debéis hacer una entrega conjunta desde el moodle de uno de los dos componentes. En la documentación figurará el nombre de ambas personas.

Tenéis que entregar:

- **Código fuente** del programa con comentarios de cómo funciona
- **Documentación de las pruebas realizadas**: en qué circunstancias funciona el algoritmo, en cuáles ha fallado, por qué creéis que lo ha hecho y cómo creéis que se podría solucionar. Podéis incluir capturas de pantalla, adjuntar videos, los ficheros de los mundos...
    + Como mínimo probad un mundo en el simulador stage (usando el `ejemplo.world` que vimos en la introducción a ROS), y otro en el simulador gazebo. Ten en cuenta que el sensor simulado en stage (un láser) tiene mucho más campo de visión que el de Gazebo (una cámara 3D): 270 grados para el láser vs. 60 para la cámara, y eso influirá en el comportamiento del algoritmo, más que el que sea un entorno 2D o 3D.
  
**IMPORTANTE**: en evitación de obstáculos (como en todo lo demás) **no hay algoritmos perfectos  ni que funcionen siempre** igual de bien en todos los casos. No debéis intentar "esconder" los casos en los que vuestro código no funciona, sino documentarlos, intentar encontrarle una explicación y (idealmente) implementar mejoras que lo solucionen, o al menos proponerlas. Para poder aplicar un algoritmo es importante conocer sus limitaciones.

## Baremo de evaluación

- **Hasta un 6**: código entregado y documentado (con comentarios al fuente) y una explicación breve (de 1/2 a 1 página) de la idea básica de vuestro algoritmo
- **Hasta 1 punto más**: todo lo anterior más pruebas documentadas en al menos dos entornos simulados que sean distintos (p.ej. pocos obstáculos vs muchos). Podéis buscar entornos por internet o crearlos vosotros.
- **Hasta 1.5 puntos más**: todo lo anterior más hacer que el robot en lugar de "vagabundear" sin rumbo intente ir a un destino que se especificará como una coordenada `(x,y)` en el sistema de coordenadas `odom`. El programa debería admitir como argumentos estas coordenadas.
- **Hasta 1.5 puntos más** por probar el código en el robot real y documentar las pruebas según se indica en el apartado correspondiente.