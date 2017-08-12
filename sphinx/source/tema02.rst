Fiabilidad y tolerancia a fallos
================================

Este tema, junto con el tema 3, trata de la producción de componentes de
software fiables. Aunque se realizan consideraciones sobre la prevención de
fallos, la atención se dedica principalmente a la tolerancia a fallos. Se
condirean las técnicas de recuperación de errores hacia adelante y hacia atrás.
El manejo de excepciones se estudia en el siguiente tema.

Causas que pueden propiciar el fallo de un sistema de tiempo real:

1. Especificación inadecuada.
2. Defectos provocados por errores de diseño.
3. Defectos provocados por fallos en componentes del procesador.
4. Defectos provocados por interferencias transitorias o permanentes en el
   subsistema de comunicaciones.

Los errores relacionados con el diseño o la especificación son dificiles de
preveer mientras que los errores provocados por fallos son en cierto modo
predecibles. Los lenguajes de programación de tiempo real tienen que ser
altamente fiables.

Fiabilidad, fallos y defectos
-----------------------------

Fiabilidad: una medida del éxito con el que el sistema se ajusta a alguna
especificación definitiva de su comportamiento.

Fallo del sistema: Cuando el comportamiento de un sistema se desvía del
especificado para él, se dice que es un fallo.

Se pueden distinguir tres tipos de fallos:

* Fallos transitorios, Un fallo transitorio comienza en un instante de tiempo 
  concreto, se mantiene en el sistema durante algún periodo, y luego desaparece.
  Ejemplos de este tipo de fallos se dan en componentes hardware en los que se
  produce una reacción adversa a una interferencia externa, como la producida por
  un campo eléctrico o por radioactividad. Después de que la perturbación
  desaparece, lo hace también el fallo (aunque no necesariamente el error
  inducido). Muchos de los fallos en los sistemas de comunicación son
  transitorios.
* Fallos permanentes Los fallos permanentes comienzan en un instante determinado
  y permanecen en el sistema hasta que son reparados; es el caso, por ejemplo,
  de un cable roto o de un error de diseño de software.
* Fallos intermitentes Son fallos transitorios que ocurren de vez en cuando. Un
  ejemplo es un componente hardware sensible al calor, que funciona durante un
  rato, deja de funcionar, se enfría, y entonces comienza a funcionar de nuevo.

Modos de fallo
--------------

Se pueden identificar dos dominios generales de modos de fallo:

1. Fallos de valor, el valor asociado con el servicio es erróneo.
2. Fallos de tiempo, el servicio se completa a destiempo.

Las combinaciones de fallos de valor y de tiempo se denominan fallos
arbitrarios.

Un fallo de valor fuera del rango esperado para el servicio se denomina error de
límites, son fallos fácilmente reconocibles.

Los fallos en el dominio del tiempo se pueden englobar en:

1. Demasiado pronto, el servicio es entregado antes de lo requerido.
2. Demasiado tarde, el servicio se entrega después de lo requerido, se puede
   hablar de error de prestaciones.
3. Infinitamente tarde, el servicio nunca es entregado, fallo de omisión.

Esta clasificación se puede ampliar con fallos de encargo o improvisación cuando
el servicio es entregado sin ser esperado.

Dada la clasificación de fallos se puede definir algunas suposiciones respecto
al modo en que los sistemas pueden fallar:

1. Fallo descontrolado, un sistema que produce fallos arbitrarios tanto en el
   dominio del valor como del tiempo.
2. Fallo de retraso, un sistema produce servicios correctos en el dominio del
   valor pero no en el del tiempo.
3. Fallo de silencio, cuando el sistema falla bruscamente sin haber tenido fallos
   de valor o de tiempo, a partir del fallo todos los servicios subsiguientes
   también sufren fallos de omisión.
4. Fallo de parada, un sistema que cumple los requisitos de un fallo de silencio
   pero permite que otros sistemas detectan que ha entrado en el estado de fallo
5. Fallo controlado, un sistema falla de una forma especificada y controlada.
6. Sin fallos, un sistema produce los servicios correctos, tanto en el dominio
   del valor como del tiempo.

Estrategias para sistemas fiables
---------------------------------

Para construir sistemas mas fiables podemos destacar la prevención de fallos
limitando la introducción de fallos en la construcción del sistema con:

* Mejor hardware e interconexión de los componentes
* Aislar el hardware para la prevención de interferencias ambientales
* Especificación rigurosa del software con métodos de programación orientada a
  objetos
* Uso de herramientas especializadas del diseño del software

Tambén podemos enfocarnos en la eliminación de fallos aunque a pesar de las
pruebas realizadas para descubrir fallos nunca podemos demostrar su ausencia,
las pruebas no pueden ser exhaustivas.

Nivel de tolerancia a fallos
----------------------------

El nivel de tolerancia a fallos lo podemos describir con este esquema:

* Tolerancia total: el sistema continua funcionando después de un fallo por un
  tiempo limitado, en este tiempo no se afecta ni la funcionalidad ni el
  rendimiento del sistema.
* Caída suave: el sistema sigue funcionando después de un fallo pero con aspectos
  de funcionalidad deteriorados
* Fallo seguro: se establece una parada segura que no produce efectos en la
  tolerancia respecto a otros componentes del sistema

Las técnicas de tolerancia a fallos se basan en la adición al sistema de
elementos que permiten la recuperación, estos elementos se llaman redundantes en
el sentido de que su uso no es necesario en caso del funcionamiento normal del
sistema.

Respecto al hardware existen dos estrategias de tolerancia a fallos:

* Estática (enmascaramiento): módulos redundantes hardware que realizan la misma
  función, el sistema debe comparar los diferentes resultados para ser capaz de
  desechar los resultados incorrectos.
* Dinámica, la redundancia se produce dentro del propio componente, el mismo
  componente informa del error en la salida.

También podemos hablar de tolerancia a fallos en el software:

* Estático, :index:`Programación N-versiones`, se trata de introducir diferentes
  componentes software que sean capaces de producir cálculos alternativos de la
  misma función y proporcionar resultados a comparar para asegurar que están
  libres de errores.

  Los diferentes modelos deberían ser programados por personas, lenguajes y
  compiladores diferentes.
		
  Los programas son ejecutados por un programa director que los ejecuta
  concurrentemente y serán comparados por el mismo. Los resultados (votos) deben
  ser idénticos, si alguno es diferente este resultado será considerado como
  erróneo.

  Un problema que puede aparecer en los sistemas de medición de valores reales es
  la precisión de estos valores, necesitaremos entonces técnicas de comparación
  inexactas.

  El éxito de la programación de N-versiones depende de:

        * Correcta especificación inicial: la especificación del diseño provocará
          que los diferentes componentes software funcionen de forma correcta.
        * Independiencia en el diseño: es importante que se utilicen diferentes
          personas y herramientas para la implementación de los diferentes
          componentes.
        * Presupuesto adecuado para desarrollar las tres versiones

* Redundancia dinámica

  El error es informado por el propio módulo, que debe detectarlo.
  La detección puede ser por el entorno o por la apliación.

  El módulo debe ser capaz de confinar el error y valorar sus daños. El 
  confinamiento consiste en establecer cortafuegos que delimiten el alcance del
  efecto de los errores. Se pueden realizar por:

  * Descomposición modular: delimita la difusión del error en el módulo.
  * Acciones atómica: en caso de error devuelve el sistema a un estado de
    consistencia

  Recuperación de errores, hay que tratar de volver al estado de operación normal
  o de degradación en la funcionalidad. Existen dos tipos de recuperación:

  * Recuperación hacia adelante, se continua con la ejecución del sistema
    haciendo correcciones selectivas de su estado
  * Recuperación de errores hacia atrás, en caso de error el sistema se restaura
    a un estado seguro previo y se ejecuta desde ese punto - checkpoint

  Una ventaja de la recuperación de errores hacia atrás es que el sistema no
  necesita localizar el error.

  Como inconvenientes:

  * No se puede recuperar de errores del entorno
  * Efecto dominó, se retorna a puntos de recuperación debido a posibles datos
    incorrectos en la comunicación entre varios procesos.
  * El error puede volver a ocurrir, debe ser eliminado en dos etapas: 
    localización y reparación.

* Bloques de recuperación

  Se trata de bloques normales de programación con un punto de recuperación al
  principio y un test de aceptación al final que comprueba que el sistema se
  encuentra en un estado aceptable al finalizar la ejecución del bloque. Si
  existe un fallo se restaura el estado de ejecución al inicio del bloque
  continuando con un procedimiento alternativo. Si todas las alternativas
  presentan error el bloque mostrará un estado de error.

  Es el test de aceptación el que se encarga de la detección de el error lo que
  puede suponer una sobrecarga. Por eso se introduce cierto margen de tolerancia
  para la aceptación.
