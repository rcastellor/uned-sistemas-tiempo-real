Control de recursos
===================

Como continuación del tema anterior, en este tema se tratan los procesos
competitivos. Un tema importante en este caso es la distinción entre
sincronización condicional y sincronización evitable en el modelo de
concurrencia.

Control de los recursos
-----------------------

Aunque los procesos son independientes entre sí, el acto de asignación de
recursos puede provocar problemas si existe algún error en los procesos
(inanición o interbloqueo).

La comunicación es necesaria para la asignación de recursos. En los lenguajes de
programación los recursos deberían encapsularse y se debe acceder a ellos a
través de interfaces de alto nivel.

El monitor representa una buena opción para regular las condiciones de
comunicación para la asignación de recursos.

**Aproximaciones lingüisticas para acceso a un servicio o recurso:**

* Espera condicional: se aceptan todas las solicitudes, las que no se pueden
  ejecutar se ponen en una cola de espera (monitor).
* Evitación: solo se aceptan aquellas peticiones que se pueden satisfacer,
  comprobable mediante guardas. Un problema que presentan los monitores en esta
  opción es que la cola de espera se maneja de forma arbitraria.

Puede haber procesos que se queden siempre en la cola de espera y por no llegar
a obtener el recurso nunca se ejecuten. **Inanición**

Interbloqueo o bloqueo mutuo
----------------------------

Tambien conocido como deadlock se trata del bloqueo permanente de un conjunto de
procesos o hilos de ejecucion que compiten o se comunican entre ellos en un
sistema concurrente. No existe una solución general para los interbloqueos.

Es posible representar bloqueos mutuos mediante grafos dirigidos, el proceso es
representado por un cuadrado y el recurso por un circulo. Cuando un proceso
solicita un recurso se dibuja una flecha dirigida desde el el proceso al
recurso, cuando el recurso esta asignado a un proceso la flecha esta dirigida
desde el recurso al proceso.

Existen cuatro condiciones necesarias para que se de un interbloqueo, estas son:

* Condición de exclusión mutua, existe un recurso compartido por los procesos al
  que solo puede acceder un proceso simultaneamente.
* Condición de retención y espera, al menos un proceso P ha adquirido un recurso
  R y lo retiene mientras espera un recurso R2 que ha sido asignado a otro
  proceso.
* Condición de no expropiación, los recursos no pueden ser expropiados por otros
  procesos, tienen que ser liberados por sus propietarios.
* Condición de espera circular, dado un conjunto de proceso P0..PM, P0 esta
  esperando un recurso adquirido por P1, que esta esperando un recurso adquirido
  por P2, ..., que esta esperando un recurso adquirido por PM, que esta 
  esperando un recurso adquirido por P0. Esta condición implica la condición de
  retención y espera.

Hay tres perspectivas posibles para tratar con los interbloqueos:

* La prevención.
* Evitación.
* Detección y recuperación.
