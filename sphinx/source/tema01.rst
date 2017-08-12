Introducción a los Sistemas de Tiempo Real
====================================================

Definición sistema en tiempo real
---------------------------------

Cualquier sistema en el que el tiempo en el que se produce la salida es 
significativo. Esto generalmente es porque la entrada corresponde a algún 
movimiento en el mundo físico, y la salida esta relacionada con dicho 
movimiento. El intervalo entre el tiempo de entrada y el de salida debe ser
lo suficientemente pequeño para una temporalidad aceptable.

Cualquier actividad o sistema de proceso de información que tiene que responder
a un estimulo de entrada generado externamente con un retardo finito y
especificado.

La correción de un sistema en tiempo real no depende sólo del resultado 
lógico de la computación, sino también del tiempo en el que se producen los 
resultados.

Tipos de sistemas en tiempo real
--------------------------------

* Hard (estrictos) 
        Son los sistemas en los que es absolutamente imperativo
        que las respuestas se produzcan dentro del tiempo límite
        especificado.
* Soft (no estrictos)
        Los tiempos de respuesta son importantes pero el sistema
        seguirá funcionando correctamente aunque los tiempos límite
        no se cumplan ocasionalmente.

Ejemplos de sistemas en tiempo real
-----------------------------------

Características de sistemas en tiempo real
------------------------------------------

Los sistemas en tiempo real tienen unas características especificas que los
definen.

1. Funcionalidades en tiempo real
2. Control concurrente de sistemas separados
3. Programación de bajo nivel
4. Soporte a computación númerica
5. Grandes y complejos
6. Extremadamente fiables y seguros
7. Implementación eficiente y entorno de ejecución
