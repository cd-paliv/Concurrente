# Pasaje de Mensajes Sincrónicos (PMS)

Los programas se componen *sólo* de procesos, es decir, no existen las variables compartidas.
- Los canales son de tipo *link* y sincrónicos, donde hay un único emisor y un único receptor. Son estructuras implícitas y no se deben declarar.
- Los procesos interactúan entre ellos únicamente por medio del envío de mensajes.

No se requiere sincronizacion por exclusion mutua ya que no existen variables compartidas.

### Sintaxis
- Sentencias de comunicación: no existen las estructuras de canales, por lo que la comunicación se realiza nombrando al proceso con el cual se realiza la comunicación.
    - Envío (*!*): la operación es bloqueante y sincrónica, se demora hasta que la recepción haya terminado.
        - Un envío: `destino!port(mensaje)`
        - Un arreglo de envíos: `destino[i]!(mensaje)`
    - Recepción (*?*): la operación es bloqueante y sincrónica.
        - Una recepción: `origen?port(variable)`
        - Un arreglo de recepciones: `origen[i]?port(variable)`
        - Un arreglo de recepciones que recibe un dato de cualquier proceso del arreglo origen: `origen[*]?port(variable)`
- Guardas: cada alternativa es una guarda con la forma: *B; C -> S*
    - B: condición booleana opcional(default=True), indica si el proceso está o no en condiciones de procesar el mensaje recibido en C.
    - C: sentencia de recepción que seguro debe estar.
    - S: conjunto de sentencias que se ejecutarán en caso de ser elegida la guarda.
- Evaluación de una guarda:
    - Éxito: la codición booleana es verdadera y la comunicación se puede realizar sin producir demora.
    - Fallo: la condición booleana es falsa, sin importar lo que ocurra con la sentencia de comunicación.
    - Bloqueo: la condición booleana es verdadera pero la comunicación NO se puede realizar sin producir demora.
- Comunicación Guardada (If y Do): permite seleccionar de forma no determinística entre varias alternativas de comunicación en base a las condiciones del proceso y los mensajes que están listos para ser recibidos.
    - If "guardado": se evalúan todas las guarda y en base a eso:
        - Si una o más son exitosas se selecciona una de ellas en forma NO DETERMINÍSTICA, se ejecuta la sentencia de recepción C que forma parte de la guarda y posteriormente el conjunto de sentencias asociadas a la guarda S.
        - Si todas las guardas fallan no se selecciona ninguna y se sale del IF sin realizar ninguna acción.
        - Si no hay ninguna guarda exitosa pero hay una o más con estado bloqueo entonces el proceso se demora en el IF hasta que haya una guarda exitosa. En ese momento se ejecutar igual que el primer caso-
    - Do "guardado": funciona de la misma manera que el IF, con la única diferencia que en lugar de hacerlo una vez, repite el mecanismo hasta que todas las guardan FALLAN.