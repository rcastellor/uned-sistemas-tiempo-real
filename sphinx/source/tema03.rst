Excepciones y manejo de excepciones
===================================

Se continúa con el estudio realizado en el tema anterior sobre la producción de
componentes de software fiables, centrándose este tema en el manejo de las
excepciones, estudiándose tanto los modelos de terminación como los de
reanudación.

Los mecanismos de manejo de excepciones tienen que cumplir estos requisitos:

1. El mecanismo debe ser facil de comprender y utilizar.
2. El código de manejo de excepciones no debe oscurecer el código normal del
   programa.
3. El mecanismo debe diseñarse de forma que no suponga una sobrecarga en la
   ejecución. Solamente sera aceptable cierta pequeña sobrecarga cuando sea
   primordial la rapidez de recuperación al ocurrir una excepción.
4. El mecanismo debe permitir un tratamiento uniforme de las excepciones tanto
   por el entorno como por el programa.
5. El mecanismo debe permitir la programación de acciones de recuperación.

Existen métodos de control de excepciones en lenguajes tipo C implementados con
valores de retorno inusuales, normalmente distintos de cero. Otro método de
control de excepciones en este tipo de lenguajes es mediante la bifurcación
forzada, por ejemplo el uso de goto en C aunque este tipo de control de
excepciones generan un código más oscuro.

Actualmente en los lenguajes de programación modernos se tiende a incluir
funcionalidades de manejo de exepciones directamente en el lenguaje, lo que
provee un mecanismo más estructurado.

Según el retraso en la detección del error las excepciones se provocan de forma
síncrona o asíncrona, la excepción síncrona se genera como respuesta a un bloque
de código, la excepción asíncrona se genera un tiempo despues de que ocurra la
operación que da lugar a la aparición del error.

Existen cuatro tipos de excepciones:

1. Detectada por el entorno y generada síncronamente, por ejemplo una división
   por cero.
2. Detectada por la aplicación y generada síncronamente, por ejemplo un fallo en
   un aserto.
3. Detectada por el entorno y generada asíncronamente, por ejemplo un fallo de
   alimentación o un mecanismo de monitorización vital.
4. Detectada por la aplicación y generada asíncronamente, por ejemplo cuando un
   proceso verifica una condición que ocasionará que un proceso no cumpla los
   plazos o no termine correctamente.

Existen diferentes formas de declarar las excepciones síncronas:

1. Mediante un nombre constante que necesita ser declarado explícitamente.
2. Mediante un objeto de cierto tipo que podrá o no ser declarado explícitamente.

Ada precisa que las excepciones se declaren como constantes, por ejemplo las
excepciones que puede generar el entorno de ejecución se declaran en el paquete
Standard.

.. code-block:: ADA

        package Standard is
        ....
        Constraint_Error : exception;
        Program_Error : exception;
        Storage_Error : exception;
        Tasking_Error : exception;
        ...
        end Standard

Java y C++ dan una visión de las excepciones más orientada al objeto. En java
las excepciones son instancias de una subclase de la clase Throwable y podran
ser lanzadas tanto por el sistema de ejecución como por la aplicación. En C++ se
pueden lanzar excepciones de cualquier tipo de objeto sin declararlas
previamente.

Se considera que un bloque esta vigilado (guarded) cuando se ha definido un
manejador de excepciones para el, por ejemplo en Java se considerará vigilado el
bloque en una estructura try ... catch. Al definir los bloques podemos incurrir
en un problema de granularidad inadecuada cuando el bloque es demasiado grande y
no podemos definir en que instrucciones se produce la excepción. Una solución a
este problema es disminuir el tamaño del bloque y/o anidarlo. Otra alternativa
es crear procedimientos con manejadores para cada uno de los bloques anidados.
La mejor aproximación es permitir el paso de parametros junto a las excepciones.

Propagación de excepciones
--------------------------

Cuando no hay un manejador de excepciones en un bloque de código y se produce
una excepción hay dos formas de proceder:

* Entender la ausencia de manejador como un error de programación y notificarlo
  en tiempo de compilación.
* La segunda vía es buscar manejadores en el pasado de la cadena de invocación en
  tiempo de ejecución, esto se conoce como propagación de excepciones.

Modelos
-------

Existen varios modelos de excepción:

* Modelo de continuación, en el caso de que el invocador sea capaz de manejar la
  excepción propagada por el proceso invocado, este aplicará las acciones
  necesarias y continuará con su ejecución.
* Modelo de terminación, cuando el control de la excepción no se devuelve al
  invocador no existen acciones de manejo que termina la ejecución del proceso.
* Modelos híbridos, el manejador es el que decide si continuar con la ejecución
  del proceso o terminar la ejecución
