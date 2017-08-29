Sincronización y comunicación basada en variables compartidas
=============================================================

Este tema, junto con el siguiente, se centra en el estudio de la comunicación
entre tareas. En concreto en este primer tema se escriben los métodos de
variables compartidas, incluyendo la utilización de semáforos, monitories,
variables compartidas y objetos protegidos.


El comportamiento correcto de un programa concurrente depende estrechamente de
la sincronización y la comunicación entre procesos. Sincronizar es satisfacer
las restricciones en el entrelazado de las acciones de diferentes procesos.
Algunas formas de comunicación requieren sincronización y la sincronización
puede ser considerada como comunicación sin contenido.


La comunicación entre procesos se basa normalmente o en el uso de variables
compartidas o en el paso de mensajes. Las variables compartidas son objetos a
los que puede acceder mas de un proceso, la comunicación puede realizarse
referenciando en cada proceso dichas variables cuando sea apropiado. El paso de
mensajes implica el intercambio explícito de datos entre dos procesos mediante
un mensaje que pasa de un proceso a otro siguiendo algún mecanismo. Hay que
señalar que la elección entre variables compartidas y paso de mensajes deben
realizarla los diseñadores de los lenguajes o de los sistemas operativos y no
implica que deba utilizarse un método particular de implementación. Las
variables compartidas son más fáciles de soportar si hay memoria compartida
entre procesos, pero pueden ser utilizadas incluso si el hardware incorpora un
medio de comunicación. Este tema se centrará en las primitivas de sincronización
y comunicación basadas en memoria compartida. En particular se verán los
conceptos de espera ocupada, semáforos, regiones criticas condicionales,
monitores, tipos protegidos y métodos sincronizados.


Una secuencia de sentencias que deben aparecer como ejecutada indivisiblemente
se denomina :index:`sección crítica`. La sincronización que se precisa para
proteger una sección crítica se conoce como :index:`exclusión mutua`. La
exclusión mutua no es la única sincronización importante. La sincronización
condicionada, o de condición, es otro requisito significativo, y es necesaria
cuando un proceso desea realizar una operación que sólo puede ser realizada
adecuadamente, o de forma segura, si otro proceso ha realizado alguna acción o
está en algún estado definido.

Espera ocupada
--------------

Una forma de implementar la sincronización es comprobar las variables
compartidas que actuan como indicadores en un conjunto de procesos. Esta
aproximación sirve razonablemente bien para implementar sincronización de
condición, pero no hay un método simple para la exclusión mutua. Para indicar
una condición, un proceso activa el valor de un indicador; para esperar por esta
condición, otro proceso comprueba este indicador y sólo continúa cuando se lee
el valor apropiado:

.. code-block:: ADA

        process P1; // proceso esperando
	        ...
	        while indicador = abajo do
	        	null
	        end;
	        ...
        end P1;
        process P2; // proceso indicando
        	...
	        indicador := arriba;
	        ...
        end P2;

Si la condición no es aún correcta, P1 no tiene elección y deberá continuar en
el bucle para volver a comprobar el indicador. Esto se conoce como *espera
ocupada*, y también como *giro* -y a los indicadores como *cerrojos de giro* 
(spin locks)-.

Los algoritmos de espera presentan algunas dificultades que pueden resumirse
como:

* Los protocolos de espera ocupada son difíciles de diseñar y comprender, y es
  complicado probar su corrección.
* Los programas de prueba pueden ignorar entrelazamientos raros que rompen la
  exclusión mutua o llevan a un interbloqueo activo.
* Los bucles de espera ocupada son ineficientes.
* Una tarea no fiable que utilice falsamente las variables compartidas,
  corromperá el sistema completo.

Ningún lenguaje de programación concurrente se basa completamente en espera
ocupada y variables compartidas, hay otros métodos y primitivas, los semáforos y
monitores se describen en las siguientes secciones.

Suspender y reanudar
--------------------

Un método alternativo a la espera ocupada es suspender (eliminar del conjunto de
procesos ejecutables) al proceso solicitante si la condición por la que espera
no es cierta. Por ejemplo

.. code-block:: ADA

        process P1; //proceso que espera
	        ...
	        if indicador = abajo do
		        suspend;
	        end;
	        indicador := abajo;
	        ...
        end P1;
        process P2; // signaling process
	        ...
	        indicador := arriba;
	        resume P1; // no tiene efecto si P1 no ha sido suspendido 
	        ...
        end P2;


El ejemplo anterior se puede representar en Java así:

.. code-block:: java

        boolean indicador;
        final boolean arriba = true;
        final boolean abajo = false;

        class PrimerHilo extends Thread {
	        public void run() {
		        ...
		        if(indicador == abajo) {
			        suspend();
		        }
		        indicador = abajo;
		        ...
	        }
        }

        class SegundoHilo extends Thread {
	        PrimerHilo H1;

	        public SegundoHilo(PrimerHilo H){
		        super();
		        H1 = H;
	        }

	        public void run() {
		        ...
		        indicador = arriba;
		        H1.resume();
		        ...
	        }
        }


Esta aproximación padece lo que se conoce como una :index:`condición de 
carrera`.
El hilo H1 podría comprobar el indicador y el soporte de ejecución decidir
desalojarlo y ejecutar H2. H2 activa el indicador y resume H1. H1 no está
suspendido por lo que resume no tiene efecto. Ahora cuando H1 vuelva a
ejecutarse, piensa que indicador está abajo y se suspende el mismo.

La razón de este problema es que el indicador es un recurso compartido que está
siendo comprobado, y una acción que va a ser tomada depende de sus estatus. Esta
comprobación y suspensión no es una acción atómica y por tanto puede ser
interferenciada por otros procesos. Java da por obsoletos estos métodos.

Para resolver los problemas de condición de carrera se opta por realizar una
*suspensión en dos etapas*. P1 debe anunciar que plantea suspenderse
próximamente, cualquier operación de reanudación que encuentre con que P1 no
está suspendido verá aplazado su efecto y cuando P1 se suspenda, será reiniciado
inmediatamente, es decir, anulará la suspensión.

Semáforos
---------

Los semáforos son un mecanismo sencillo para la programación de la exclusión
mutua y la sincronización de condición. Diseñados originalmente por Dijkstra
presentan los siguientes beneficios:

* Simplifican los protocolos para la sincronización.
* Eliminan la necesidad de bucles de espera ocupados.

Un *semáforo* es una variable entera no negativa que, aparte de la
inicialización, sólo puede ser manejada por dos procedimientos. Estos
procedimientos fueron llamados por Dijkstra P y V, pero en estos apuntes los
denominaremos *wait* y *signal*. La semántica de wait y signal es como sigue:

* wait(S) Si el valor del semáforo, S, es mayor que cero, entonces decrementa su
  valor en uno; en caso contrario, demora el proceso hasta que S sea mayor que
  cero (y entonces decrementa su valor).
* signal(S) Incrementa el valor del semáforo, S, en uno.

Estas operaciones son atómicas.

Regiones condicionales críticas
-------------------------------

En estas regiones se agrupan las variables protegidas y sentencias indivisibles.
El acceso a la región condicional critica esta protegido por una variable
booleana denominada guarda.

Monitores
---------

Los monitores aparecen como mecanismos que intentan solucionar estos problemas.
En ellos las regiones críticas se escriben como procedimientos y por tanto las
variables protegidas estan ocultas.

El programador no se debe preocupar por la exclusión mutua en la llamada de
procedimientos de monitor ya que estas están serializadas.

Existe un tipo de monitores que garantiza la indivisibilidad de las tareas
internas de estos, el llamado **monitor de Hoore**. En el existen dos señales:
wait y signal, la primera bloquea el proceso que la llama y la segunda libera un
proceso de la cola de procesos bloqueados, sino existe ningún proceso en esta
cola la llamada signal no tiene efecto.
