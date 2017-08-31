Acciones atómicas, Tareas Concurrentes y Fiabilidad
===================================================

En este tema se amplian las discusiones iniciales sobre tolerancia a fallos
describiendo con cuánta fiabilidad puede ser programada la cooperación entre
procesos. Para esta discusión es fundamental la noción de acción atómica y las
técnicas de manejo de eventos asíncronos.

Los objetivos del tema pasan por:

Acciones atómicas
-----------------

Una acción es atómica si las tareas que la llevan a cabo no son conscientes de
las actividades de las primeras, durante el tiempo en el que llevan a cabo la
acción atómica.

* Sin comunicación con otras tareas
* No detectan cambios de estado externos
* Los cambios de estado internos no se comunican hasta finalizar

Las acciones atómicas son vistas desde fuera como indivisibles e instantáneas.

Para permitir la descomposición modular de las acciones atomicas se incorpora la
noción de acciones atómicas anidadas. Los procesos comprometidos en una acción
anidada deben ser un subconjunto de los que están involucrados en el nivel
externo de la acción.

Las propiedades que tiene una acción atómica:

* Una acción es atómica si los procesos que la realizan no saben de la existencia
  de ningún otro proceso activo, y ningún otro proceso activo tiene constancia de
  las actividades de los procesos durante el tiempo que en el que están
  realizando la acción.
* Una acción es atómica si los procesos que la realizan no se comunican con otros
  procesos mientras está siendo realizada la acción.
* Una acción es atómica si los procesos que la realizan no pueden detectar ningún
  cambio de estado salvo aquellos realizados por ellos mismos, y si no revelan
  sus cambios de estado hasta que la acción se haya completado.
* Las acciones son atómicas si, en lo que respecta a otros procesos, pueden ser
  consideradas indivisibles e instantáneas, de forma que los efectos sobre el
  sistema sean como si estuvieran entrelazadas y no en concurrencia.

Acciones atómicas recuperables
------------------------------

La expresión transacciones atómicas se ha utilizado frecuentemente en el marco
conceptual de los sistemas operativos y las bases de datos. Una transacción
atómica tiene todas las propiedades de una acción atómica, más la característica
adicional de que su ejecución puede tener exito o fallar. Por fallo se entiende
la ocurrencia de un error del que la transacción no puede recuperarse, por
ejemplo, un fallo de procesador. Si falla una unidad atómica, los componentes
del sistema que están siendo manipulados por la acción pueden quedar en estado
inconsistente. Ante un fallo, una transacción atómica garantiza que los
componentes son devueltos a su estado original. Las transacciones atómicas a
veces se conocen como acciones recuperables.

Las dos propiedades distintivas de las transacciones atómicas son:

* Atomicidad de fallo, lo que significa que la transacción debe o bien ser
  completada con éxito o (en el caso de fallar) no tener efecto.
* Atomicidad de sincronización (o aislamiento), lo que significa que la
  transacción es indivisible, en el sentido de que su ejecución parcial no puede
  ser observada por ninguna transacción que se esté ejecutando concurrentemente.

Notificación asíncrona
----------------------

El requisito fundamental de un mecanismo de notificación asíncrona es permitir
que un proceso responda rápidamente a una condición que ha sido detectada por
otro proceso. El énfasis esta en la prontitud de la respuesta, obviamente, un
proceso siempre puede responder a un evento simplemente sondeando o esperando
ese evento. La notificación del evento puede construirse sobre el mecanismo de
comunicación y sincronización de proceso. El proceso, cuando está disponible
para recibir el evento, simplemente realiza la petición apropiada.

Desafortunadamente, hay ocasiones en las que no es adecuado esperar o sondear la
ocurrencia de enventos, como en las siguientes:

* *Recuperacion de errores*
  Cuando hay grupos de procesos que realizan acciones atómicas, la detección de
  un error en un proceso necesita de la participación del resto de procesos en
  la recuperación. Por ejemplo, un fallo hardware puede suponer que el proceso
  nunca termine su ejecución prevista porque las condiciones de comienzo ya no
  se cumplen; el proceso puede que nunca alcance su punto de sondeo. También
  podría ocurrir un fallo de temporización, por lo que no se podría cumplir el
  plazo límite de su servicio. En estas situaciones el proceso debe ser
  informado de que ha sido detectado un error y de que debe efectuar alguna
  forma de recuperación de error lo más rápidamente posible.
* *Cambios de modo*
  Los sistemas de tiempo real normalmente tienen distintos modos de operacion.
  En ocasiones habrá cambios de modo, y estos deben ser gestionados en puntos
  bien definidos de la ejecución del sistema. Sin embargo, en algunas áreas de
  aplicación, los cambios de modo pueden ser esperados pero no planificados. En
  estas situaciones, los procesos deben ser informados de forma rápida y segura
  de que el modo en el que estaban operando ha cambiado y deben proceder a un
  conjunto de acciones diferentes.

* *Planificación utilizando computaciones parciales/imprecisas*
  Existen muchos algoritmos en los que la precisión de los resultados depende del
  tiempo asignado a los cálculos. Por ejemplo, los cálculos numéricos,
  computaciones estadísticas y búsquedas heurísticas, generan una estimación
  inicial del resultado requerido, que despues es mejorada con una mayor
  precisión. En tiempo de ejecución, a cada algoritmo se le asigna una cierta
  cantidad de tiempo, transcurrido el cual el proceso debe interrumpirse, de
  forma que se finaliza el mecanismo de refinamiento del resultado.

* *Interrupciones de usuario*
  En un entorno interactivo general, los usuarios a
  menudo desean finalizar el procesamiento actual porque han detectado una
  condición de error y desean comenzar de nuevo.

Una aproximación al manejo asíncrono de notificación es abortar el proceso y
permitir que otro proceso efectúe alguna forma de recuperación. Todos los
sistemas operativos y la mayoría de los lenguajes de programación concurrente
proporcionan esta disponibilidad. Sin embargo, el aborto de un proceso puede ser
costoso y a menudo es una respuesta extrama a muchas condiciones de error.
Consecuentemente, es necesario algún mecanismo de notificación asíncrona.

Mecanismos de recuperación de errores
-------------------------------------

Mecanismos de recuperación de errores en las acciones atómicas:

* Recuperación de errores hacia atrás: Si existe un error en la acción atómica
  todas las tareas incolucradas retroceden. El mecanismo que se encarga de esta
  recuperación se llama **conversación**

  * Se guarda el contexto de los procesos al inicio de la conversación.
  * La comunicación de los procesos es solo con otros procesos internos o con el
    gestor de recursos.
  * Para abandonar la conversación todos los procesos deben haber pasado el test
    de aceptación.
  * Se puede llevar a cabo la conversación aunque no esté presente alguna tarea
    involucrada en ella.

  El principal inconveniente de las conversaciones es que el error en uno de los
  procesos produce que todos deban retroceder. Aunque tiene la ventaja de que
  puede tomar caminos alternativos ya que se recupera el estado consistente.

* Recuperación de estados hacia adelante: si se lanza una excepción en alguno de
  los procesos de las acciones atómicas se lanza la excepción en todas.

  Se presenta el problema de las excepciones concurrentes. El lanzarse dos o más
  excepciones concurrentes no se define bien cual es el manejador que se debe
  ejecutar.

  Una posible solución es un árbol de excepciones, dónde se elige la excepción
  raíz del subárbol que contiene todas las excepciones que se han lanzado.

Otro mecanismo que permite la interacción entre las tareas y la recuperación de
errores en las acciones atómicas es la **notificación asíncrona**. Permite que
una tarea llame a la otra sin tener que esperar. Surgen así las dos vías de
tratamiento de excepciones:

* Reanudación (manejo de eventos): cada proceso indica las excepciones que
  maneja. En caso de que se lanza una se avisa y se cambia el flujo de control
  del proceso al manjeador.
* Terminación: cada proceso determina la transferencia asíncrona de control que
  lo puede terminar.
