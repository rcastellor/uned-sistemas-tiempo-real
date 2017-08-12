Problemas de planificabilidad
=============================

Algoritmo de planificación de tasa monotónica
---------------------------------------------

Tres procesos lógicos (P, Q y S) tienen las siguientes características:

* P: periodo 3, tiempo de ejecución necesario 1.
* Q: periodo 6, tiempo de ejecución necesario 2.
* S: periodo 18, tiempo de ejecución necesario 5.

Explique el algoritmo de planificación de :index:`tasa monotónica` (rate monotonic
scheduling algorithm) y muestre cómo pueden planificarse estos procesos utilizando
el algoritmo de planificación de tasa monotónica.

El algoritmo de planificación de tasa monotónica se basa en asignar la prioridad
en función del periodo del proceso, de forma que los procesos con menor periodo
tendran la mayor prioridad.

De esta forma en el caso del encunciado del problema la tabla de prioridades
quedara de la forma:

============   ===========   =======================   ============
   Proceso     Periodo (T)   Tiempo de ejecución (C)   Prioridad(P)
============   ===========   =======================   ============
 *P*            3            1                         3
 *Q*            6            2                         2
 *S*            18           5                         1 
============   ===========   =======================   ============

Una vez asignada la prioridad a los procesos escribimos en una tabla las
fracciones de tiempo asignadas a cada proceso

=== === === === === === === === === === === === === === === === === === === ===
 I   0   1   2   3   4   5   6   7   8   9   10  11  12  13  14  15  16  17  18
=== === === === === === === === === === === === === === === === === === === ===
*P*  X           X           X           X           X           X           X
*Q*      X   X                   X   X                   X   X
*S*                  X   X                   X   X                   X
=== === === === === === === === === === === === === === === === === === === ===

Como observamos en la tabla en un fragmento de 18 segmentos de tiempo los tres
procesos se han podido planificar por lo que podemos deducir que este conjunto
de procesos es planificable.

Test simple de planificabilidad para FPS
----------------------------------------

¿Es planificable el conjunto de procesos mostrado en la tabla? (utilice el test
simple de planificabilidad para FPS)

========= =============  =========================
 Proceso   Periodo (T)    Tiempo de Ejecución (C)
========= =============  =========================
  a          50               10
  b          40               10
  c          30                9
========= =============  =========================

El primer paso es establecer la prioridad de los procesos en base al periodo
(menor periodo, mayor prioridad):

============   ===========   =======================   =============
   Proceso     Periodo (T)   Tiempo de ejecución (C)   Prioridad (P)
============   ===========   =======================   =============
 *a*            50            10                         3
 *b*            40            10                         2
 *c*            30             9                         1 
============   ===========   =======================   =============

Una vez asignada la prioridad a los procesos  calculamos el tiempo de uso de CPU
de cada proceso:

============   =====  ======  =========      ============================
   Proceso       T      C         P           Tiempo de uso de CPU (C/T)
============   =====  ======  =========      ============================
 *a*            50      10       3              20%
 *b*            40      10       2              25%
 *c*            30       9       1              30%
============   =====  ======  =========      ============================

Para realizar el :index:`test simple de planificabilidad` utilizamos la siguiente
formula, siendo N el número de procesos a planificar:

.. math::
        U \equiv \sum_{i=1}^N \frac{C_i}{T_i} <  N*(2^{1/N} - 1)

De esta forma para el numero de procesos del ejercicio (3) tenemos el valor:

.. math::
        3*(2^{1/3} - 1) = 0,78

Sumando el tiempo de uso de los tres procesos tenemos el valor 0,75, como
:math:`0,78 > 0,75` podemos afirmar que el conjunto de procesos es planificable.

Estudio de planificación
------------------------

Estudie la planificación del siguiente conjunto de procesos:

========= =============  =========================
 Proceso   Periodo (T)    Tiempo de Ejecución (C)
========= =============  =========================
  A          75               35
  B          40               10
  C          20                5
========= =============  =========================

El primer paso es establecer la prioridad de los procesos en base al periodo
(menor periodo, mayor prioridad) y el tiempo de utilización:

============   =====  ======  =========      ============================
   Proceso       T      C         P           Tiempo de uso de CPU (C/T)
============   =====  ======  =========      ============================
 *A*            75      35       1              46,6%
 *B*            40      10       2              25%
 *C*            20       5       3              25%
============   =====  ======  =========      ============================

Para realizar el test simple de planificabilidad utilizamos la siguiente
formula, siendo N el número de procesos a planificar:

.. math::
        N*(2^{1/N} - 1)

De esta forma para el numero de procesos del ejercicio (3) tenemos el valor:

.. math::
        3*(2^{1/3} - 1) = 0,78

El caso mostrado no supera el test basado en la utilización ya que 
:math:`0,78 < 0,96`, el umbral de utilización es muy superior al tiempo limite
de :math:`0,78` por lo cual no podemos afirmar que el conjunto de procesos sea
planificable en función del test de planificabilidad, aunque esto no es
condición suficiente para afirmar que el conjunto de procesos no es
planificable.

Si optamos por analizar los :index:`tiempos de respuesta para FPS`, el proceso de
mayor prioridad tiene un tiempo de respuesta:

.. math::
        R_i = C_i

        R_C = 5   

El resto de procesos sufren interferencias:

.. math::
        R_i = C_i + I_i

El valor máximo de la interferencia viene dado por: 
:math:`\sum_{j\in hp(i)}[\frac{R_i}{T_j}]C_j`

:math:`T_j` es el periodo de aquellos procesos j con mayor prioridad que i.

.. math::
        R_c = C_c = 5

        w_b^0 = C_b = 10
        
        w_b^1 = C_b + [\frac{w_b^0}{T_c}]C_c = 10 + [\frac{10}{20}]5 = 15

        w_b^2 = C_b + [\frac{w_b^1}{T_c}]C_c = 10 + [\frac{15}{20}]5 = 15

        R_b = 15

        w_a^0 = C_a = 35

        w_a^1 = C_a + [\frac{w_a^0}{T_c}]C_c + [\frac{w_a^0}{T_b}]C_b 
        = 35 + [\frac{35}{20}]5 + [\frac{35}{40}]10 = 55

        w_a^2 = C_a + [\frac{w_a^1}{T_c}]C_c + [\frac{w_a^1}{T_b}]C_b
        = 35 + [\frac{55}{20}]5 + [\frac{55}{40}]10 = 70

        w_a^3 = C_a + [\frac{w_a^2}{T_c}]C_c + [\frac{w_a^2}{T_b}]C_b
        = 35 + [\frac{70}{20}]5 + [\frac{70}{40}]10 = 75

        w_a^4 = C_a + [\frac{w_a^3}{T_c}]C_c + [\frac{w_a^3}{T_b}]C_b
        = 35 + [\frac{75}{20}]5 + [\frac{75}{40}]10 = 75

        R_a = 75

Para sacar los valores se redondea el contenido de la fracción al entero más
cercano, en el momento en que encontramos los valores de :math:`w_a^3 = w_a^4`
es cuando podemos comprobar si el tiempo de respuesta cumple los requerimientos.

En el caso de estudio el problema es planificable. Una ventaja de este método es
que el calculo de los tiempos de respuesta es condición suficiente y necesaria
para decidir si el conjunto de procesos es planificable.
