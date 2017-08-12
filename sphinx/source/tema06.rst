Sincronización y comunicación basada en mensajes
================================================

Este tema es la continuación del anterior y resalta la importancia que tienen en
los lenguajes modernos los métodos basados en mensajes para la comunicación y
sincronización.

La sincronización basada en mensajes se basa en tres fases y/o clasificaciones:

* Modelo de sincronización: las variaciones en el modelo de sincronización
  dependen de la semántica de envío.

  * Envío asíncrono, el emisor envía un mensaje y continua inmediatamente con sus
    acciones después de enviarlo. Requiere el uso de buffers.
  * Envío síncrono, el emisor envía el mensaje y no continua hasta que recibe el
    acuse de recibo del receptor.
  * Invocación remota, el emisor espera a la elaboración de la respuesta por
    parte del receptor. La espera es más larga que en el envío síncrono.

* Nombrado de procesos:

  * Nombrado directo: se hace referencia explicita al receptor
  * Nombrado indirecto: se usa una entidad intermedia para el envío (canal,
    tubería, etc...)
  * Nombrado simétrico: el emisor y el receptor se envían en mensajes entre sí.
    Especificado en todo momento los nombres.
  * Nombrado asimétrico, no se nombra la fuente especifica de recepción 
    (Cliente/Servidor)

* Estructura de mensajes: algunos lenguajes de programación concurrentes
  restringen la estructura de sus mensajes.

En la sincronización los procesos deben estar preparados para la comunicación,
si uno esta preparado el otro deberá esperar a que lo esté.

Espera selectiva, el receptor(servidor) tiene que esperar a que quede libre el
canal de comunicación. Podemos permitir esperar a varios procesos realizando una
selección en el servidor, mediante el uso de guardas se elige el procedimiento
que se ejecuta en el servidor.

Se puede dar el caso de que varias guardas se evalúen como verdadera, en este
caso la ejecución será indeterminista, es decir, se elegirá la opción
aleatoriamente.

Se produce una cita cuando un cliente invoca a un servicio de un servidor y este
acepta.

