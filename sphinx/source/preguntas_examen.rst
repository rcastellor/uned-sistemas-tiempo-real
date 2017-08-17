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

