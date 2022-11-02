# Lenguaje ADA (Rendezvous)

Una aplicación en ADA es un único programa con un cuerpo principal, y para que se trabaje concurrentemente se desarrollan TASKS (tareas) dentro de ese programa que pueden ejecutar independientemente y contienen primitivas de sincronización/comunicación. Se pueden implementar directamente TASK (única instancia) o declarar un TASK TYPE para crear varias instancias iguales de ese tipo (arreglo, puntero, instancia simple). Cada TASK o TASK TYPE tienen una especificación donde se declaran las operaciones exportadas (llamadas ENTRYs) y un BODY donde se implementa la tarea.
- Las tareas pueden exportar operaciones o no, si lo hacen deben definir los ENTRYs correspondientes en la especificación.
    - El cuerpo de las tareas es donde se implementa la tarea. En esta parte no hay diferencia entre las TASK y los TASK TYPE.
- Los ENTRYs pueden o no tener parámetros, si los tiene se debe declarar el nombre, el tipo de datos y el tipo de parámetro (IN, OUT o IN OUT).

### Sintaxis
- *Rendezvous* es el principal mecanismo de sincronización de ADA y también es el mecanismo de comunicación primario. La comunicación entre dos tareas se realiza por medio de un ENTRY CALL por parte del proceso que realiza el pedido y un ACCEPT por parte del que resuelve ese pedido. La comunicación es bidireccional, y es fundamental que esta característica se aproveche al resolver los problemas en ADA.
- En cada *Entry* existe en la tarea una cola implícita de entry calls pendientes. Cada entry tiene asociado un atributo que puede ser consultado sólo por la tarea a la que pertenece el entry que indica la cantidad de entry call pendientes. Este atributo puede ser usado en los when, por ejemplo, para dar prioridad a un entry sobre otro.
    - Hay 3 formas de EntryCall que se diferencian por el tiempo de demora.
        - Entry Call simple: la ejecución demora al llamador hasta que la otra tarea termine de ejecutar el accept correspondiente.
        - Entry Call condicional (select - else): no espera a que le acepten el pedido, si la otra tarea no está lista para realizar el accept a su pedido inmediatamente, entonces lo cancela y realiza otra cosa.
        - Entry Call temporal (select - or delay): espera a lo sumo un tiempo a que la otra tarea realice el accept a su pedido, pasado el tiempo cancela el entry call y realiza otra cosa.
- El *Accept* demora la tarea hasta que haya al menos un entry call en la cola implícita asociada; saca al primero de ellos y:
    - Si no tiene cuerpo: termina inmediatamente el accept y ambas tareas continúan con su ejecución.
    - Si tiene cuerpo: copia los parámetros reales del llamado en los parámetros formales; ejecuta las sentencias que están en el cuerpo; cuando termina los parámetros formales de salida son copiados a los parámetros reales. Luego ambas tareas continúan con su ejecución.
- ADA brinda la posibilidad de implementar wait selectivos por medio de comunicación: *Select para los accept*. Permite tener varias alternativas de ACCEPT que pueden o no tener una condición booleana asociada utilizando la cláusula when.
    ```
    SELECT
        ACCEPT nombreEntry1
        sentencias a realizar sólo si se completó el accept nombreEntry1.
    OR
        WHEN (condición) -> ACCEPT nombreE2 IS
                                sentencias que forman el cuerpo del accept.
                            END nombreE2;
                            sentencias a realizar sólo si se completó el accept nombreE2.
    END SELECT;
    ```
    - A su vez, se puede agregar al final un ELSE o un OR DELAY que actúa de forma similar al de los entry call:
    ```
    SELECT
        ACCEPT nombreEntry1
        sentencias a realizar sólo si se completó el accept nombreEntry1.
    OR
        WHEN (condición) -> ACCEPT nombreE2 IS
                                sentencias que forman el cuerpo del accept.
                            END nombreE2;
                            sentencias a realizar sólo si se completó el accept nombreE2.
    OR DELAY tiempo
        sentencias a realizar si pasó tiempo sin poder aceptar ninguna alternativa.
    END SELECT;
    ```