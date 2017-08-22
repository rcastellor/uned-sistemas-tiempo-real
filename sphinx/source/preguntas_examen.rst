Preguntas de examenes
=====================

Mecanismos cambio de contexto
-----------------------------

*Entre los mecanismos necesarios para permitir la entrada y salida dirigidas por
interrupciones están los mecanismos de cambio de contexto. En el espacio de una
cara comente estos mecanismos de cambio de contexto.*

Cuando ocurre una interrupción, debe preservarse el estado actual del procesador
y activarse la rutina de servicio correspondiente. Una vez servida la
interrupción, habrá que retomar el proceso y continuar su ejecución. Otra
posibilidad es que el planificador seleccione un nuevo proceso como consecuencia
de la interrupción. El procedimiento completo se conoce como cambio de contexto
y sus acciones pueden resumirse como sigue:

* Preservación del estado del procesador inmediatamente anterior a la aparición
  de la interrupción.
* Establecimiento del procesador en el estado adecuado para procesar la
  interrupción.
* Restauración del estado del proceso suspendido, tras completar el
  procesamiento de la interrupción.

El estado de un proceso en ejecución sobre un procesador consta de:

* La posición en memoria de la instrucción actual (o la siguiente) en la
  secuencia de ejecución.
* La información de estatus del programa (que podrá contener información
  relativa al modo de procesamiento, a la prioridad actual, a la protección de
  memoria, a las interrupciones permitidas y otras).
* Los contenidos de los registros programables.

El tipo de cambio de contexto provisto se caracteriza por la capacidad del
hardware de preservar y restaurar el estado del proceso. Se pueden distinguir
tres niveles de cambio de contexto:

* Básico: sólo se guarda y se actualiza el contador de programa
* Parcial: se guardan el contador de programa y los registros de estatus del
  programa para alojar los nuevos valores.
* Completo: se guarda el contexto completo del proceso y se aloja uno nuevo.

Dependiendo del grado necesario de preservación del estado, puede que haya que
complementar las acciones del hardware con soporte explícito de software. Por
ejemplo, puede que un cambio parcial de contexto sea adecuado para un modelo de
manejo de interrupciones que contemple al manejador como un procedimiento o
subrutina. Sin embargo, si el manejador es visto como un proceso separado con
sus propias áreas de pila y de datos, será necesario un manejador de bajo nivel
que se haga cargo completamente del cambio de contexto. Si, por otra parte, el
hardware puede con un cambio de contexto completo, dicho manejador de bajo nivel
no sera necesario. La mayoria de los procesadores sólo proporcionan un cambio
parcial de contexto. Sin embargo, el procesador ARM proporciona una
interrupción rápida, donde se guardan también algunos de los registros de
propósito general.

Hay que hacer constar que algunos procesadores modernos permiten la búsqueda,
decodificación y ejecución de instrucciones en paralelo. Algunos incluso
permiten la ejecución de instrucciones al margen de la secuencia especificada en
el programa. Este capítulo parte de la premisa de que las interrupciones son
precisas en el sentido de que, cuando se ejecuta un manejador de interrupción:

* Todas las instrucciones que ha tomado el procesador antes de la instrucción
  interrumpida han finalizado su ejecución y han modificado correctamente el
  estado del programa.
* Aquellas instrucciones tornadas por el procesador tras la instrucción
  interrumpida han sido ejecutadas y no han modificado el estado del programa.
* Si fue la misma instrucción la que causo la interrupción (por ejemplo,
  provocando una violación de memoria), dicha instrucción ha sido ejecutada
  totalmente o no se ha ejecutado en absoluto.

Si una interrupción no es precisa, se supone que la recuperación es transparente
al software manejo de la interrupción.

Fallos de temporización y valor
-------------------------------

*Explique como se puede transformar un sistema de forma que todos los fallos de
temporización se manfiesten como fallos de valor. ¿Se puede conseguir tal
conversión?*

Un fallo de temporización se define como la entrega de un servicio fuera de su
intervalo de administración definido, por lo general más alla de una fecha
límite definida. A menudo el servicio se entrega tarde debido al tiempo
necesario para construir el valor correcto para el servicio. Si el sistema se
diseño para siempre entregar un valor en el intervalo correcto, entonces la
falta de tiempo se manifestaría como un valor incorrecto. De ahí que los fallos
de sincronización y el valor no pueden ser considerados ortogonales.

Lo contrario de lo anterior también puede ser cierto. Un servicio que ha
fracasado porque el valor que ofrece es incorrecto, puede ser capaz de entregar
un valor correcto si se administra más CPU de tiempo; por lo tanto, un valor
correcto puede ser entregado pero demasiado tarde. Sin embargo, esto no es una
verdad universal, un fallo de valor (o error) puede ser debido a muchas razones
distintas de insuficiente asignación de ciclos de procesador.

Primitivas de sincronización
----------------------------

*En la cara de una hoja comente los conceptos de potencia expresiva y facilidad
de uso de las primitivas de sincronización para control de recursos.*

Bloom ha sugerido criterios para evaluar primitivas de sincronización en el
contexto de la gestión de recursos. Este análisis forma la base de esta sección,
donde se consideran la potencia expresiva y la facilidad de uso de las
primitivas de sincronización para control de recursos. Las primitivas a evaluar
son monitores/métodos sincronizados (con su uso de sincronización de condición),
servidores (con una interfaz de paso de mensajes) y recursos protegidos
(implementados como objetos protegidos). Los dos últimos utilizan guardas para
la sincronización y por ello un aspecto de este análisis es una comparación
entre sincronización de condición y sincronización de evitación.

La expresión **potencia expresiva** se utiliza para indicar la capacidad de un
lenguaje para expresar las restricciones de sincronización requeridas. La
**facilidad de uso** de una primitiva de sincronización abarca:

* La facilidad con la que expresa cada una de esas restricciones de
  sincronización.
* La facilidad con la que se permite combinar las restricciones para conseguir
  esquemas de sincronización más complejos.

En el contexto del control de recursos, la información necesaria para expresar
estas restricciones se puede clasificar como sigue:

* Tipo de petición de servicio.
* Orden en el que llegan las solicitudes.
* Estado del servidor y de todos los objetos que gestiona.
* Parámetros de una solicitud.

El conjunto original de restricciones incluía la **historia del objeto** (esto
es, la secuencia de todas las solicitudes previas). Aquí se supone que el estado
del objeto se puede extender para incluir la información histórica. Además, se
añade a la lista:

* La prioridad del cliente

Se considera la prioridad como medida de la importancia del proceso.

Hay en general dos aproximaciones lingüisticas a la restricción del acceso para
un servicio. La primera es la espera condicional, se aceptan todas las
solicitudes pero cualquier proceso cuya solicitud no se puede atender se
suspende en una cola. El monitor convencional tipifica esta aproximación: un
proceso cuya solicitud no se puede atender es encolado en una variable de
condición y reanudado cuando se puede atender la solicitud. La segunda
aproximación es evitación: las solicitudes no se aceptan a menos que puedan ser
satisfechas. Las condiciones bajo las que se puede aceptar una solicitud de
forma segura se expresan como una guarda en una acción de aceptación.

Tecnicas de detección de errores
--------------------------------

*La efectividad de cualquier sistema tolerante a fallos depende de la
efectividad de sus técnicas de detección de errores. Describa las dos clases de
técnicas de detección de errores.*

* **DETECCIÓN EN EL ENTORNO:** Los errores se detectan en el entorno en el cual
  se ejecuta el programa. Se incluye aquellos detectados por el hardware, como
  los de *ejecución de instrucción ilegal*, *desbordamiento aritmético*, o
  *violación de protección*. También son considerados los errores detectados en
  tiempo de ejecución por el sistema soporte del lenguaje de programación de
  tiempo real; por ejemplo, los de *error en los límites del array*, *referencia
  a apuntador nulo*, o *valor fuera de rango*.
* **DETECCIÓN EN LA APLICACIÓN:** Los errores se detectan por la aplicación
  misma. La mayoría de las técnicas que se pueden utilizar en la aplicación
  corresponden a alguna de las siguientes categorías:
  
  * Comprobación de réplicas. Se ha demostrado que la programación de
    N-Versiones puede ser utilizada para tolerar fallos software, y también como
    técnica para la detección de errores (utilizando una redundancia de
    2-Versiones)
  * Comprobaciones temporales. Existen dos tipos de comprobaciones temporales.
    El primer tipo implica un proceso temporizador guardián, que sino es puesto
    a cero por un cierto componente dentro de un cierto periodo de tiempo, se
    supone que dicho componente esta en un estado de error. En los sistemas
    embebidos, donde los tiempos de respuesta son importantes, se necesita un
    segundo tipo de comprobación. De esta manera se detectan fallos asociados
    con el incumplimiento de tiempos límite.
  * Comprobaciones inversas. Estas son posibles en componentes donde exista una
    relación uno a uno entre la entrada y la salida.
  * Códigos de comprobación. Los códigos de comprobación se utilizan para
    comprobar la corrupción de los datos.
  * Comprobaciones de racionalidad. Se basan en el conocimiento del diseño y de
    la construcción del sistema. Comprueban que el estado de los datos o el
    valor de un objeto es razonable basandose en su supuesto uso.
  * Comprobaciones estructurales. Las comprobaciones estructurales son
    utilizadas para comprobar la integridad de los objetos de datos tales como
    listas o colas. Podrían consistir en contar el número de elementos en el
    objeto, en apuntadores redudantes o en información extra sobre su estatus.
  * Comprobaciones de racionalidad dinámica. En la salida producida por algunos
    controladores digitales, habitualmente existe una relación entre
    cualesquiera dos salidas consecutivas. Por lo tanto, se podrá detectar un
    error si el valor de una salida nueva difiere considerablemente del valor de
    la salida anterior.

Acciones atómicas
-----------------

*Distinguir entre una acción atómica y una transacción atómica, ¿Cuál es la
relación entre una transacción atómica y una conversación?*

La expresión transacciones atómicas se ha utilizado frecuentemente en el marco
conceptual de los sistemas operativos y las bases de datos. Una transacción
atómica tiene todas las propiedades de una acción atómica, más la característica
adicional de que su ejecución puede tener éxito o fallar (no éxito). Por fallo
se entienede la ocurrencia de un error del que la transacción no puede
recuperarse, por ejemplo, un fallo de procesador. Si falla una acción atómica,
los componentes del sistema que están siendo manipulados por la acción pueden
terminar en un estado inconsistente.

Ante un fallo una transacción atómica garantiza que los componentes son
devueltos a su estado original, esto es, al estado en el que estaban antes de
comenzar la transacción, las transacciones atómicas a veces se conocen coon el
nombre de acciones recuperables, aunque desafortunadamente, se tiende a
confundir los términos acción atómica y transacción atómica.

Las dos propiedades distintivas de las transacciones atómicas son:

* Atomicidad de fallos, lo que significa que la transacción debe o bien ser
  completada con éxito, o (en el caso de fallar) no tener efecto.
* Atomicidad de sincronización, o aislamiento, lo que significa que la
  transacción es indivisible, en el sentido de que su ejecución parcial no puede
  ser observada por ninguna transacción que se esté ejecutando concurrentemente.

A pesar de que las transacciones atómicas son útiles para las aplicaciones que
implican la manipulacióon de bases de datos, no son adecuadas para la
programación de sistemas tolerantes a falloes per se. Esto se debe a que
precisan de algún tipo de mecanismo de recuperación proporcionado por el
sistema. Este mecanismo viene preestablecido, sin que el programador tenga
control sobre su operativa. A pesar de que las transacciones atómicas
proporcionan una forma de recuperación de errores hacia atrás, no permiten la
escritura de procedimientos de recuperación. Independientemente de lo anterior,
las transacciones atómicas tienen su sitio en la protección de la integridad en
los sistemas de bases de datos de tiempo real.

(No esta bien contestada)

Manejo de excepciones
---------------------

*En los mecanismos de manejo de excepciones, si el manejador resolviera el
problema que causó la generación de la excepción, sería posible que reanudara su
trabajo. Esto se conoce como modelo de reanudación, describa como funciona dicho
modelo*

Para ilustrar el modelo de reanudación, considere tres procedimientos (P, Q y
R). El procedimiento P invoca Q, que a su vez invoca R. El procedimiento R
genera una excepción (r) que es manejada por Q, asumiendo que no hay un
manejador local en R. El manejador para (r) es Hr, mientras maneja (r), Hr
genera la excepción q que es manejada por Hq en el procedimiento P. Una vez
manejada q, Hr continua su ejecución, y despues continua R.

El modelo de reanudación se entiende mucho mejor si contemplamos el manejador
como un procedimiento que se invoca implícitamente al generar la excepción.

El problema de esta aproximación es la dificultad a la hora de reparar errores
generados por el entorno de ejecución. Por ejemplo, un desbordamiento aritmético
en medio de una secuencia compleja de expresiones podría ocasionar que varios de
los registros contuvieran evaluaciones parciales. Al llamar al manejador, es
probable que se sobreescriban dichos registros.

Tanto el lenguaje Perl como el lenguaje Mesa proporcionan un mecanismo que
permite al manejador volver al contexto donde se genero la excepción. Ambos
lenguajes soportan también el modelo de terminación.

Cuando realmente se nota la ventaja del modelo de reanudación es cuando la
excepción ha sido generada asincronamente y por lo tanto tiene poco que ver con
la ejecución actual del proceso.

Interbloqueos
-------------

*Estudie si este sistema se encuentra en una situación de interbloqueo, y
explique sus razones.*

Considérese un sistema que tiene cinco recursos (P1, P2, P3, P4, P5) y siete
recursos (R1, R2, R3, R4, R5, R6, R7). Hay una instancia de los recursos 2, 5
y 7, y dos instancias de los recursos 1, 3, 4 y 6. El proceso 1 tiene asignada
una instancia de R1 y requiere una de R7.  El proceso 2 tiene asignadas una
instancia de R1, R2 y R3 y requiere una de R5. El proceso 3 tiene asignada una
instancia de R3 y R4 y requiere una de R1. El proceso 4 tiene asignada R4 y R5
y requiere una de R2. El proceso 5 tiene una de R7.

Para que el proceso se encuentre en una situación de interbloqueo debe cumplir
cuatro condiciones:

* *Condición* de exclusión mutua, existe un recurso compartido por los procesos
  al que solo puede acceder un proceso simultaneamente, los recursos 2, 5 y 7
  cumplen esta condición.
* *Condición de retención y espera*, al menos un proceso P ha adquirido un
  recurso R y lo retiene mientras espera un recurso R2 que ha sido asignado a
  otro proceso. El proceso 4 tiene adquirido el recurso R5 mientras espera el
  recurso R2 que esta bloqueado por el proceso 2
* *Condición de no expropiación*, los recursos no pueden ser expropiados por
  otros procesos, tienen que ser liberados por sus propietarios.
* *Condición de espera circular*, el proceso P2 tiene bloqueada la unica
  instancia del recurso R2, requiere a su vez la instancia del recurso R5 que
  esta bloqueado por el proceso 4 que a su vez requiere el recurso R2 por lo
  que se produce una situación de retención y espera.

Protocolos de acotación de prioridad
------------------------------------

*Sobre los protocolos de acotación de prioridad (priority ceiling protocols),
responder a las siguientes cuestiones:*

a) ¿Qué cuestiones abordan los protocolos de acotación de la prioridad?
b) ¿Qué forma toma el protocolo original de acotación de la prioridad?
c) ¿Cómo se define el protocolo inmediato de acotación de la prioridad?
d) Aunque el comportamiento en el peor de los casos de los dos esquemas de
   acotación es idéntico (desde el punto de vista de la planificación), existen
   diferencias, indicar cuales son.


a) Aunque el protocolo estándar de herencia da un límite superior para el número
   de bloqueos con los que se puede encontrar un proceso de prioridad alta, este
   límite puede todavía conducir a un cálculo del peor caso inaceptablemente
   pesismista. Esto se debe a la posibilidad de desarrollar cadenas de bloqueos
   (bloqueos transitivos), es decir, situaciones en las que el prceso c es
   bloqueado por el proceso b, el cual está bloqueado por el proceso a, y así
   sucesivamente. Como los datos compartidos son un recurso del sistema, desde
   el punto de vista del gestor de recursos no sólo se debe minimizar el
   bloqueo, sino que las condiciones de fallo (como los interbloqueos) deben ser
   eliminadas. Los protocolos de acotación de la prioridad abordan todas estas
   cuestiones. Consideramos dos de ellos:

   * Protocolo original de acotación de la prioridad.
   * Protocolo inmediato de acotación de la prioridad
   
   Cuando se utiliza cualquiera de estos protocolos en un sistema
   monoprocesador:

   * Un proceso de alta prioridad puede ser bloqueado por procesos de prioridad
     baja en una sola ocasión como máximo durante su ejecución.
   * Se previenen los bloqueos mutuos (interbloqueos).
   * Se previenen los bloqueos transitivos.
   * Se aseguran (por el protocolo mismo) los accesos mutuamente excluyentes a
     los recursos.
   
   La mejor manera de describir los protocolos de acotacióon de la prioridad es
   en relación con los recursos protegidos por secciones críticas. En esencia,
   el protocolo asegura que si un recurso esta bloqueado por cierto proceso a, y
   esto conduce a que se bloquee un proceso de mayor prioridad b, entonces no se
   permite que ningún otro recurso que pueda bloquear a b sea bloqueado más que
   por un a. Un proceso, por lo tanto, puede ser retardado no sólo mientras está
   intentando bloquear un recurso previamente bloqueado, sino también cuando ese
   bloqueo pudiera producir un bloqueo múltiple de procesos de mayor prioridad.

b) El procotolo original toma la siguiente forma:
   
   1. Cada proceso tiene asignada una prioridad estática por defecto (quizás
      según el esquema monotónico de tiempo límite).
   2. Cada recurso tiene definido un valor cota estático, que es la prioridad
      máxima de los procesos que lo estan utilizando.
   3. Un proceso tiene una prioridad dinámica que es el máximo de su prioridad
      estática y de cualquiera de las que herede debido a que bloquea procesos
      de mayor prioridad.

   Se permite el bloqueo del primer recurso del sistema. El efecto del protocolo
   es asegurar que el segundo recurso solo pueda ser bloqueado si no existe un
   proceso de mayor prioridad que utilice ambos recursos.

c) El algoritmo inmediato toma un enfoque más sencillo, fija la prioridad de un
   proceso tan pronto bloquea un recurso, el protocolo se define como sigue:

   1. Cada proceso tiene asignada una prioridad por defecto.
   2. Cada recurso tiene definido un valor cota estático, que es la prioridad
      máxima de los procesos que lo utilizan.
   3. Un proceso tiene una prioridad dinámica, que es el máximo entre su propia
      prioridad estática y los valores techo de cualquier recurso que tenga
      bloqueado.

   Como consecuencia de esta última regla, un proceso sólo podrá ser bloqueado
   al principio de su ejecución. Una vez comience a ejecutarse, todos los
   recursos necesarios debería estar libres, si no lo estuvieran algun proceso
   tendría una prioridad mayor o igual y la ejecución del proceso deberá ser
   pospuesta.

d) Existen estas diferencias:

   1. ICPP es más sencillo de implementar que el original(OCPP), no hay que
      monitorizar las relaciones de bloqueo.
   2. ICPP produce menos cambios de contexto, el bloqueo es previo a la primera
      ejecución.
   3. ICPP requiere más cambios de prioridad, estos se producen con todas las
      utilizaciones de recursos, OCPP modifica la prioridad sólo si se
      producen bloqueos.

Semántica del reencolado
------------------------

El concepto que está detrás del reencolado es el de mover la tarea (que estaba
tras una guardia o barrera) detrás de otra guarda. Considérese una persona
esperando para entrar en una habitación. Una vez dentro, la persona puede ser
arrojada (reencolada) de la habitación y ser colocada de nuevo detrás de una
puerta(potencialmente cerrada).

Ada permite reencolados entre entradas de tarea y entradas de objetos
protegidos. Un reencolado puede ser para un mismo entry, para otro entry de la
misma unidad, o para otra unidad. Se permiten los reencolados de entradas de
tarea(y viceversa). Sin embargo, el principal uso del reencolado es enviar la
tarea invocadora a un entry diferente de la misma unidad en la que se ejecuto el
reencolado.

Semántica: Es importante apreciar que reencolado no es una simple llamada. Si el
procedimiento P llama al procedimiento Q, entonces, una vez que Q ha finalizado,
el control se pasa de nuevo a P. Pero si el entry X reencola en la entry y, el
control no se pasa de nuevo a X. Una vez que se ha completado Y, el control pasa
de nuevo al objeto que llamo a X. Por tanto, cuando un cuerpo entry o accept
ejecuta un reencolado, ese cuerpo se completa.

Una consecuencia de esto es que, cuando se realiza un reencolado desde un objeto
protegido a otro, entonces la exclusión mutua sobre el objeto original se
abandona una vez que se ha encolado la tarea. Otras tareas que esperan para
entrar en el primer objeto serán capaces de hacerlo. Sin embargo, un reencolado
sobre el mismo objeto protegido mantendrá el bloqueo de exlcusión mutua (si el
entry objetivo esta abierto).

Ejecución concurrente de procesos
---------------------------------

*Describa los tres mecanismos básicos de representación de la ejecución
concurrente de procesos(Task representation concurrent exception).*

Hay tres mecanismos básicos para representar la ejecución concurrente: fork y
join, cobegin y la declaración explícita de procesos. A veces se incluyen
también las corrutinas como mecanismo para expresar la ejecución concurrente.

La instrucción fork(bifurca) indica que cierta rutina deberá comenzar a
ejecutarse concurrentemente con quien ha invocado el fork. La instrucción
join(reúne) permite al que invoca detenerse y por ende sincronizarse hasta la
terminación de la rutina invocada.

Cobegin(o parbegin, o par) es una forma estructurada de denotar la ejecución
concurrente de un conjunto de instrucciones. La instrucción cobegin termina
cuando han terminado todas las instrucciones concurrentes. Cada instrucción S
puede ser cualquiera de las construcciones permitidas en el lenguaje, incluyendo
las asignaciones sencillas o las llamadas a procedimientos. De invocar algún
procedimiento, podrán pasarse datos a los procesos invocados mediante los
parámetros de la llamada. La instrucción cobegin podría incluir, a su vez, una
secuencia de instrucciones donde apareciera cobegin y así conseguir una
jerarquía de procesos.

Las corrutinas son como subrutinas, salvo que permite el paso explícito de
control entre ellas de una forma simetrica en vez de estrictamente jerárquica.
El contro se transfiere de una corrutina a otra mediante una sentencia reanuda
que incluye el nombre de la corrutina con la que se continúa. Cuando una
corrutina realiza una reanudación deja de ejecutarse, pero guarda información
del estado local, de forma que si, posteriormente otra corrutina la hace
reanudar podrá retomar su ejecución.

Seguridad, fiabilidad y confiabilidad
-------------------------------------

*En el contexto de la fiabilidad y tolerancia a fallos, describa y compare los
terminos: Seguridad, fiabilidad y confiabilidad(Safety, Reliability and
Dependibility)*

La seguridad software se considera a menudo en términos de percances. Un
percance es un evento no planeado o secuencias de eventos que pueden producir
muerte, lesión, enfermedad laboral, daño de equipos o propiedades o nocividad en
el medio ambiente. Aunque la fiabilidad y la seguridad suelen considerarse como
sinónimos, existe una diferencia en el énfasis. La fiabilidad ha sido definida
como la medida del éxisto con el cual un sistema se ajusta a la especificación
de su comportamiento. La fiabilidad ha sido definida como la medida del éxito
con el cual un sistema se ajusta a la especificación de su comportamiento. Esto
se expresa habitualmente en términos de probabilidad. La seguridad, sin embargo,
es la improbabilidad de que se den las condiciones que conducen al percance,
independientemente de si realiza la función prevista. Estas dos definiciones
pueden entrar en conflicto.

De la misma manera que sucede con la fiabilidad, para cumplir con los requisitos
de seguridad de un sistema embebido se debe realizar un análisis de seguridad a
largo de todas las etapas de desarrollo de su ciclo de vida.

La noción de confiabilidad de un sistema es la propiedad del sistema que permite
calificar, justificadamente, como fiable al servicio que proporciona. La
confiabilidad por lo tanto incluye como casos especiales las nociones de
fiabilidad y seguridad.
