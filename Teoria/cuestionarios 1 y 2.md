1. Mencione al menos 3 ejemplos donde pueda encontrarse concurrencia.<br />
   La concurrencia es la capacidad de ejecutar multiples actividades en paralelo o simultaneamente. Se puede encontrar:
   - Cuando varios navegadores acceden a la misma pagina.
   - Cuando el telefono avisa recepcion de llamada mientras se habla.
   - Videojuegos.

2. Escriba una definición de concurrencia. Diferencie procesamiento secuencial, concurrente y paralelo. <br />
La concurrenia permite a distintos objetos actuar al mismo tiempo. <br />
Un programa secuencial tiene un unico flujo de control que ejecuta una instruccion y cuando esta finaliza ejecuta la siguiente.<br />
Un programa concurrente especifica dos o mas programas secuenciales que pueden ejecutarse concurrentemente en el tiempo como tareas o procesos.<br />
Un programa paralelo se asocia con la ejecucion concurrente en multiples procesadores con el objetivo principal de reducir el tiempo de ejecucion.<br />

3. Describa el concepto de deadlock y qué condiciones deben darse para que ocurra. <br />
Deadlock se da cuando dos o mas procesos se quedan esperando que otro libere un recurso compartido. Las 4 propiedades necesarias y suficientes para que exista deadlock son:
   - Recursos reusables serialmente: los procesos comparten recursos que pueden usar con exclusion mutua.
   - Adquisicion incremental: los procesos mantienen los recursos que poseen mientras esperan adquirir recursos adicionales.
   - No-preemption: una vez que son adquiridos por un proceso, los recursos no pueden quitarse de manera forzada sino que solo son liberados voluntariamente.
   - Espera ciclica: existe una cadena circular (ciclo) de procesos tal que cada uno tiene un recurso que su sucesor en el ciclo esta esperando adquirir.

4.  Defina inanición. Ejemplifique. <br />
La inanicion se da cuando un proceso no logra acceder a un recurso compartido porque otro proceso siempre le gana. El ejemplo de inanicion mas utilizado es el de La Cena de Los Filosofos, la cual presenta el caso de cinco filosofos sentados en una mesa que pueden pensar y requieren de dos tenedores para comer; el problema es que cada uno tiene un unico tenedor a su izquierda, es decir, debera tomar el tenedor de otro filosofo para comer.<br />
Si los filosofos toman todos primero el tenedor de la izquierda y luego el de la derecha, siempre quedara uno esperando que otro suelte el tenedor, para poder comenzar a comer.

5. ¿Qué entiende por no determinismo? ¿Cómo se aplica este concepto a la ejecución concurrente? <br />
El no determinismo indica que no hay un orden establecido para le ejecucion. Al ejecutar un programa puede dar un resultado, y al ejecutarlo de vuelta puede dar otro distinto.

6. Defina comunicación. Explique los mecanismos de comunicación que conozca. <br />
La comunicacion entre procesos indica el modo en que se organizan y transmiten datos entre tareas concurrentes. Esta organizacion requiere especificar protocolos para controlar el progreso y la correccion. Los procesos se comunican por:
   - Memoria compartida: los procesos intercambian informacion sobre la memoria compartida o actuan coordinadamente sobre datos residentes en ella. No pueden operar simultaneamente sobre la memoria compartida, lo que obliga a bloquear y liberar el acceso a la memoria. Semaforo.
   - Pasaje de mensajes: es necesario establecer un canal (logico o fisico) y un protocolo para transmitir informacion entre procesos. Para que la comunicación sea efectiva los procesos deben saber cuando tienen mensajes para leer y cuando deben transmitir mensajes.

7. a. Defina sincronización. Explique los mecanismos de sincronización que conozca. <br />
La sincronización es la posesion de informacion acerca de otro proceso para coordinar actividades. Los procesos se sincronizan por:
   - Exclusion mutua: busca asegurar que un solo proceso tenga acceso a un recurso compartido en un instante de tiempo. Si el programa tiene secciones criticas que pueden compartir mas de un proceso, la exclusion mutua evita que dos o mas procesos puedan encontrarse en la misma seccion critica al mismo tiempo.
   - Condicion: permite bloquear la ejecución de un proceso hasta que se cumpla una condicion dada.
b. ¿En un programa concurrente pueden estar presentes más de un mecanismo de sincronización? En caso afirmativo, ejemplifique<br />
   Si. Un ejemplo de dos mecanismos de sincronizacion en un problema es un buffer limitado con productores y consumidores.

8.  ¿Qué significa el problema de “interferencia” en programación concurrente? ¿Cómo puede evitarse? <br />
La interferencia se da cuando un proceso toma una acción que invalida las suposiciones hechas por otro proceso.

9. ¿En qué consiste la propiedad de “A lo sumo una vez” y qué efecto tiene sobre las sentencias de un programa concurrente? De ejemplos de sentencias que cumplan y de sentencias que no cumplan con ASV. <br />
Una sentencia de asignación x=e satisface la propiedad de "A lo sumo una vez" si:
   1) e contiene a lo sumo una referencia critica y x no es referenciada por otro proceso.
   2) e no contiene referencias criticas, en cuyo caso x puede ser leída por otro proceso.
Al mismo tiempo, una expresión e no esta en una sentencia de asignación, satisface la propiedad si no contiene más de una referencia critica.<br />
Si una sentencia de asignación cumple con la propiedad ASV, entonces su ejecución parece atómica, pues la variable compartida será leída o escrita solo una vez. Si una expresión o asignación no satisface ASV con frecuencia, es necesario ejecutarla atómicamente. En general, es necesario ejecutar sentencias de sentencias como una única acción atómica (sincronización por exclusión mutua).<br />

10. Dado el siguiente programa concurrente:
    ```
    x = 2; y = 4; z = 3;
    co
      x = y - z // z = x * 2 // y = y - 1
    oc
    ```
a. ¿Cuáles de las asignaciones dentro de la sentencia co cumplen con ASV?. Justifique claramente. <br />
   Ninguna. <br />
   x contiene dos referencias criticas. <br />
   z contiene la referencia critica x que es referenciada por otro proceso. <br />
   y contiene la referencia critica y que es referenciada por otro proceso.<br />
b.  Indique los resultados posibles de la ejecución <br />
Nota 1: las instrucciones NO SON atómicas. <br />
Nota 2: no es necesario que liste TODOS los resultados, pero si los que sean representativos de las diferentes situaciones que pueden darse. <br />

11.  Defina acciones atómicas condicionales e incondicionales. Ejemplifique.<br />
Las sentencias await permiten generar dos tipos de acciones atomicas:<br />
   - Incondicionales: aquellas acciones atomicas que no utilizan condicion o la espera para que una condicion se cumpla. Es decir, aquellas que justamente no utilizan el await. Por ejemplo el await para exclusion mutua.
   - Condicionales: aquellas acciones atomicas que bloquean la ejecucion de un proceso hasta que se cumpla una condicion. Por ejemplo el await para sincronizacion por condicion.

12.  Defina propiedad de seguridad y propiedad de vida.<br />
Una propiedad de un programa concurrente es un atributo verdadero en cualquiera de las historias de ejeucion del mismo. Toda propiedad puede ser formulada en terminos de dos clases:
   - Seguridad (safety): asegura estados consistentes. Ejemplos: exclusion mutua, ausencia de interferencia entre procesos, partial correctness.
   - Vida (liveness): progresa, no hay deadlocks. Ejemplos: terminacion, asegurar que un pedido de servicio sera atendido, que un mensaje llega a destino, etc. Estos dependen de las politicas de scheduling.

13.  ¿Qué es una política de scheduling? Relacione con fairness. ¿Qué tipos de fairnes conoce?<br />
Una politica de scheduling determina cual sera la proxima accion atomica a ejecutar. Fairness trata de garantizar que los procesos tengan chance de avanzar, sin importar lo que hagan los demas. Hay dos tipos de fairness:
   - Fairness incondicional: una politica de scheduling es incondicionalmente fair si toda accion atomica incondicional que es elegible es eventualmente ejecutada. Ejemplo: RR en monoprocesador, ejecucion paralela en multiprocesador.
   - Fariness debil: una politica de scheduling es debilmente fair si:
      - Es incondicionalmente fair y
      - Toda accion atomica condicional que se vuelve elegible eventualmente es ejecutada, asumiendo que su condicion se vuelve truey permanece true hasta que es vista por el proceso que ejecuta dicha accion atomica condicional.
