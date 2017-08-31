Programación concurrente
========================

En este tema se introuce la noción de proceso, tarea y hebra o hilo y revisa los
modelos que utilizan los diseñadores de lenguajes y de sistemas operativos. El
término tarea se utiliza genéricamente para representar una actividad
concurrente. Se aprovecha también este tema pra estudiar la distribución de
tareas cuando se dispone de multiprocesadores o de sistemas distribuidos.
Dejándose para los siguientes dos temas la comunicación entre tareas.

La programación concurrente es el nombre que se le da a las notaciones y
expresiones que permiten expresar paralalelismo potencial y resolver problemas
de sincronización y comunicación.

- Un programa secuencia -> contiene un único camino de ejecución.
- Un programa concurrente son varios procesos secuenciales autónomos y en
  paralelismo potencial.

En los sistemas operatios un proceso contiene un hilo de control que normalmente
se ejecuta en su propia máquina virtual. Se crean diferentes hilos de proceso
dentro de un programa que tienen acceso libre a la memoria compartida por este,
estos hilos se denominan **hebras o tareas**.

Las hebras o tareas que se ejecutan independientemente del sistema operavio y a
nivel de aplicación se llaman **fibras**.

Tipos de implementación
-----------------------

* Se multiplexa la ejecución de las diferentes tareas en un único procesador
  **multiprogramación**
* Se ejecutan las tareas en un sistema multiprocesador con memoria compartida
  **multiprocesamiento**
* Se ejecutan en varios procesadores sin memoria compartida **Sistemas
  distribuidos**

El RTTS (sistema de soporte de tiempo de ejecución) se sitúa entre el hardware y
la aplicación para gestionar las disferentes tareas.

Mecanismos básicos de los lenguajes concurrentes
------------------------------------------------

* Expresión de actividades concurrentes
* Sincronización entre las actividades concurrentes.
* Primitivas de soporte de comunicación entre tareas.

Comportamiento entre las tareas
-------------------------------

* Independientes -> No se comunican ni se sincronizan en ningún momento.
* Cooperativas -> necesitan comunicarse y sincronizarse entre ellas.
* Competitivas -> se dan casos en los que se dispone de recursos limitados, las
  tareas compiten para usar estos recursos.

Diferencias en la noción de tareas en los diferentes lenguajes
--------------------------------------------------------------

* Estructura

  * Estática -> el número de tareas no varia y se conoce en tiempo de
    compilación.
  * Dinámica -> varia el número de tareas en tiempo de ejecución.

* Nivel

  * Anidado -> las tareas se definen en cualquier nivel.
  * Plano -> todas las tareas son definidas en el mismo nivel

* Granularidad

  * Gruesa -> pocas tareas con larga duración.
  * Fina -> muchas tareas con poca duración.

* Inicialización y terminación

Las relaciones entrete tareas
-----------------------------

Las relaciones entre tareas pueden dar paso a una **jerarquía de tareas**:

* Relación padre/hijo.
* Relación guardián/dependiente -> El guardián no puede terminar su ejecución
  hasta que finalicen todos sus dependientes.

En el paradigma orientado a objetos los objetos puede ser:

* Activos -> ejecutan acciones por si mismos
* Reactivos -> solo realizan acciones al ser invocados por un objeto activo

Todo esto da lugar a diferentes tipos de entidades:

* Pasiva -> reactiva sin restricciones de sincronización
* Recurso protegido -> reactivo con restricciones, el acceso es por tanto
  protegido.

Los objetos activos tienen un hilo interno de ejecución.

Mecanismos básicos para representar la ejecución concurrente
------------------------------------------------------------

* Fork/join, especifica que la rutina invocada debe ejecutarse concurrentemente
  con el invocador
* Cobegin, es una estructura que permite la ejecución concurrente de una serie
  de senticas que se especifcan como sigue:
  cobegin
  S1:
  S2:
  ....
  La ejecución termina cuando terminan de ejecutarse todas las sentencias.
* Declaración explicita de tareas, se ha convertido en el mecanismo por
  excelencia para los lenguajes con mecanismos de programción en sistemas de
  tiempo real.
  Existen dos mecanimos principales para la declaración explicita de tareas en
  java:

  * Creación de hilos extendiendo a la clase **java.lang.Thread**.
  * Implementación de la interfaz "**Runnable**" cuyo método run permite la
    ejecución concurrente al ser sobreescrito.
