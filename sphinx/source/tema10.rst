Planificación
=============

Tras el tema anterior de capacidades de tiempo real, hay que incluir estas
capacidades en la planificación de tareas, introduciéndose también la noción de
prioridad junto con el análisis de la planificabilidad para sistemas con
desalojo basado en prioridad.

Vamos a barajar tres modelos de planificación:

* Modelo simple
* Ejecutivo cíclico
* Planificación basada en procesos

Modelo simple
-------------

Tenemos un modelo que permite describir algunos esquemas de planificación
estándar:

* La aplicación está compuesta por un conjunto fijo de procesos
* Los procesos son periódicos, con periodos conocidos
* Los procesos son independientes entre sí
* El instante crítico es cuando todos los procesos son ejecutados a la vez
* Las sobrecargas del sistema, tiempos de cambio de contexto y demás se suponen
  con coste cero
* Los tiempos límite de los procesos son iguales a sus periodos
* Los procesos tienen tiempo de ejecución constante en el peor caso

Al considerar que hay independencia entre procesos se puede suponer que en algun
instante de tiempo todos los procesos son ejecutados a la vez, esto representara
la carga máxima para el procesador y se conocerá como *instante crítico*.

Para definir el modelo de proceso simple definimos las siguientes variables:

========    =============================================================
Notación    Descripción
========    =============================================================
 B          Tiempo de bloqueo en el peor caso
 C          Tiempo de ejecución del proceso en el peor caso (WCET)
 D          Tiempo límite del proceso
 I          Tiempo de interferencia del proceso
 J          Fluctuación en la ejecución del proceso
 N          Número de procesos en el sistema
 P          Prioridad asignada al proceso
 T          Tiempo mínimo entre ejecuciones del proceso (periodo)
 Y          Utilización de cada proceso (C/T)
 a-z        Nombre del proceso
========    =============================================================

Ejecutivo cíclico
-----------------

Una forma común de la implementación de sistemas de tiempo real es el uso de un
ejecutivo cíclico.


El ejecutivo cíclico es una tabla de llamadas a procedimientos, donde cada
procedimiento representa parte del código de un proceso. A la tabla completa se
le conoce como *ciclo principal* y habitualmente consta de cierto número
de *ciclos secundarios*, cada uno de ellos de duración fija. De esta
forma, por ejemplo, cuatro ciclos secundarios de 25 ms de duración conforman un
ciclo principal de 100 ms. Durante la ejecución, una interrupción de reloj cada
25 ms premite que el planificador realice rondas entre los cuatro ciclos
secundarios.

Existe una tabla de llamadas a procedimiento con un ciclo principal y ciclos
secundarios de duración fija.

Las propiedades del ejecutivo cíclico son:

* Los procesos no existen en tiempo de ejecución, solamente existe el proceso
  principal
* Los procedimientos comparten un espacio de direcciones, pueden compartir datos
  sin necesidad de protección
* Los periodos deben ser múltiplos del tiempo de ciclo secundario 

El ejecutivo cíclico presenta los siguientes inconvenientes:

* Dificultad para incorporar procesos esporádicos
* Dificultad para incorporar procesos con periodos grandes, el tiempo del ciclo
  mayor es el único que se puede usar sin replanificación
* Dificultad para construir el ejecutivo cíclico
* Procesos con tiempo notable tendrán que ser divididos en procedimientos de
  tamaño fijo, es propenso a errores

Si puede construirse un ejecutivo cíclico no será necesario ningún test de
planificabilidad más. El problema que plantea el ejecutivo cíclico es que a
partir de cierto tamaño se convierte en irresoluble, para sistemas periódicos
simples se mantiene como una estrategia de implementación apropiada.

Planificación basada en procesos
--------------------------------

Una alternativa al enfoque del ejecutivo cíclico(la ejecución consiste en una
secuencia de llamadas a procedimientos) es soportar de forma directa la
ejecución de procesos y determinar cual es el proceso que deberá ejecutarse en
cada instante de tiempo mediante uno o más atributos de planificación. Un
proceso está limitado a estar en uno de los posibles estados siguientes:

* Ejecutable
* Suspendido en espera de un evento temporizado (apropiado para procesos
  periódicos)
* Suspendido en espera de un evento no temporizado (apropiado para procesos
  esporádicos)

Alternativas de planificación
-----------------------------

Existe un gran número de aproximaciones distintas a la planificación:

* Planificación de prioridad estática o fija: FPS
* Primero el tiempo límite más temprano: EDF
* Planificación basada en el valor: VBS

Respecto a la apropiación de procesos, por derecho preferente existen estas
opciones:

* Esquema apropiativo, Cambio de procesos inmediato
* Esquema no apropiativo, se esperará a la finalización del proceso de menor
  prioridad
* Esquema de apropiación diferida o de distribución cooperativa, tiempo limitado
  para el proceso de menor prioridad

Planificación de prioridad de tasa monotómica
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A cada proceso se le asigna una prioridad en función de su periodo, los 
procesos con menor periodo tienen la mayor prioridad.

Test de planificabilidad basada en la utilización
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

El sumatorio de los tiempos de uso de CPU de cada proceso tiene que ser menor
que el valor de la ecuación del test de planificabilidad, lo cual asegura que
el conjunto de procesos es planificable, que sea mayor no implica que el
conjunto de procesos no sea planificable.

El test supone una condición suficiente pero no necesaria.

.. math::

	U \equiv \sum_{i=1}^{N}\frac{C_{i}}{T_{i}}<=N(2^{\frac{1}{N}}-1)

N=3 U<=0.78

Test de utilización para EDF
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Primero el proceso con tiempo límite más cercano.

* Es más simple

  .. math::

	\sum_{i=1}^{N}\frac{C_{i}}{T_{i}}<=1

* Es más sencillo de implementar
* Más sencillo incorporar procesos sin tiempos límites en FPS
* Es más fácil trabajar con prioridades (FPS) que con deadlines (EDF)
* FPS es más predecible en situaciones de sobrecargas


Protocolos de acotación de la prioridad
---------------------------------------

Se trata de unos protocolos diseñados para minimizar las situaciones de cadenas
de bloqueo y eliminar condiciones de fallo.

Existen dos tipos:

* Protocolo original de acotación de la prioridad: OCPP
* Protocolo inmediato de acotación de la prioridad: ICPP

Cuando se utilizan estos protocolos en un sistema monoprocesador se cumplen
las siguientes propiedades:

* Un proceso de alta prioridad puede ser bloqueado por procesos de prioridad
  baja en una sola ocasión como máximo.
* Se previenen los bloqueos mutuos
* Se previenen los bloqueos transitivos
* Se aseguran los accesos mutuamente excluyentes a recursos

El procolo asegura que si un recurso está bloqueado por cierto proceso a y esto
conduce a que se bloquee un proceso de mayor prioridad, b, entonces no se
permite que ningún otro recurso que pueda bloquear a b sea bloqueado más que por
a. Un proceso puede ser retardado no sólo mientras esta intentando bloquear un
recurso previamente bloqueado, sino también cuando ese bloqueo pudiera producir
un bloqueo múltiple de procesos de mayor prioridad.

Protocolo original de acotación de la prioridad
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

El protocolo original toma la siguiente forma:

* Cada proceso tiene asignada una prioridad estática por defecto
* Cada recurso tiene definido un valor cota estático, que es la prioridad máxima
  de los procesos que lo están utilizando
* Un proceso tiene una prioridad dinámica que es el máximo de su prioridad
  estática y de cualquiera de las que herede debido a bloqueos
* Un proceso sólo puede bloquear un recurso si su prioridad dinámica es mayor
  que la cota máxima de cualquier recurso actualmente bloqueado (excluyendo los
  bloqueados por él)

Se permite el bloqueo del primer recurso del sistema. El efecto del protocolo es
asegurar que el segundo recurso sólo pueda ser bloqueado si no existe un proceso
de mayor prioridad que utilice ambos recursos. Consecuentemente, la cantidad
máxima de tiempo que un proceso puede ser bloqueado es igual al tiempo de
ejecución de la sección crítica más larga en cualquiera de los procesos de menor
prioridad que son accedidos por procesos de alta prioridad.

Protocolo inmediato de acotación de la prioridad
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

El algoritmo inmediato de acotación de la prioridad (ICPP) toma un enfoque más
sencillo, y fija la prioridad de un proceso tan pronto bloquea un recurso (en
lugar de hacerlo cuando realmente bloquea a un proceso de mayor prioridad). El
protocolo se define como sigue:

* Cada proceso tiene asignada una prioridad por defecto (quizás mediante el
  esquema monotónico de tiempo límite).
* Cada recurso tiene definido un valor cota estático, que es la prioridad máxima
  de los procesos que lo utilizan.
* Un proceso tiene una prioridad dinámica, que es el máximo entre su propia
  prioridad estática y los valores techo de cualquier recurso que tenga bloqueado.

Como consecuencia de esta última regla, un proceso sólo podrá ser bloqueado al
principi de su ejecución. Una vez que el proceso comience a ejecutarse, todos
los recursos necesarios debería estar libres, si no lo estuvieran, entonces
algún proceso tendría una prioridad mayor o igual, y la ejecución del proceso
deberá ser pospuesta.

Aunque el comportamiento en el peor caso de los dos esquemas de acotación es
idéntico existen algunas diferencias:

* ICCP es más sencillo de implementar que el original (OCPP), ya que no hay que
  monitorizar las relaciones de bloqueo.
* ICPP produce menos cambios de contexto, ya que el bloqueo es previo a la
  primera ejecución.
* ICPP requiere más cambios de prioridad, ya que éstos se producen con todas las
  utilizaciones de recursos, OCPP modifica la prioridad sólo si se producen
  bloqueos.
