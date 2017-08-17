Preguntas de examenes
=====================

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
