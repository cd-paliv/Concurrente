# Pasaje de Mensajes Asincronicos (PMA)

Los programas se componen *solo* de procesos y canales, es decir, no existen las variables compartidas.
 - Los canales actuan como colas de mensajes enviados y no recibidos. Son de tipo mailbox. Todos los procesos los pueden usar para enviar o recibir mensajes.
 - Los procesos interactuan entre ellos unicamente por medio del envio de mensajes.
No se requiere sincronizacion por exclusion mutua ya que no existen variables compartidas.

### Sintaxis
- Canales, indicando la estructura de datos de los mensajes. Puede ser:
    - Un canal: `chan *nombrecanal*(tipoDato)`
    - Un arreglo de canales: `chan *nombrearreglo*[1..m](tipoDato)`
- Sentencias de comunicacion(*send*/*receive*): el uso de cada canal es atomico, por lo que no se harian al mismo tiempo 2 operaciones de comunicacion sobre el mismo canal.
     - Envio (send): la operacion es no bloqueante, deposita el mensaje al final del canal y continua su ejecucion; `send nombrecanal(mensaje)`
     - Recepcion (receive): la operacion es bloqueante, si el canal esta vacio demora hasta que haya al menos un mensaje en el, luego saca el primer mensaje del canal (el mas viejo); `receive nombrecanal(variables para el mensaje)`
- Consultas por mensajes pendientes (empty): funcion que retorna boolean que indica si el canal esta o no vacio. Hay que utilizarlo con cuidado cuando el canal tiene mas de un posible receptor: `empty(nombrecanal)`
- Uso de sentencias de alternativa multiple (IF no deterministico) y alternativas iterativa multiple (DO no deterministico). Puede generar Busy Waiting, que en PMA esta permitido pero hay que tratar de evitarlo.