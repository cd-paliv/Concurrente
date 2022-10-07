# Monitores

Un monitor es un módulo de programa con más estructura que un semáforo, y que pueden ser implementados tan eficientemente como éstos.
Utilizan el mecanismo de abstracción de datos:
- Encapsulan las repeticiones de recursos.
- Brindan un conjunto de operaciones que son los únicos medios para manipular esos recursos.

En sí, un monitor agrupa la representación y la implementación de un recurso compartido. Tiene:
1. La *interfaz* especifica operaciones que brinda el recurso.
2. El *cuerpo* tiene variables que representan el estado del recurso y procedures que implementan las operaciones de la interfaz.

La sincronización por condición (<await>) es programada explícitamente con variables condición -> cond *cv*;
El valor asociado a cv es una cola de procesos demorados, no visible al programador. Se pueden aplicar distintas operaciones sobre las variables de condición:
- wait(cv): el proceso se demora al final con la cola de cv y deja el acceso exclusivo al monitor.
- signal(cv): despierta al proceso que está al frente de la cola (si hay alguno) y lo saca de ella. _El proceso despertado recién podrá ejecutar cuando readquiera el acceso exclusivo al monitor._
- signal_all(cv): despierta todos los procesos demorados en cv, quedando vacía la cola asociada a cv.
- empty(cv): retorna true si la cola controlada por cv está vacía
- wait(cv, rank): el proceso se demora en la cola de cv en orden ascendente de acuerdo al parámetro rank y deja el acceso exclusivo al monitor.
- minrank(cv): función que retorna el mínimo ranking de demora.

Signal and Continued: el proceso que hace el signal continua usando el monitor, y el proceso despertado pasa a competir por acceder nuevamente al monitor para continuar con su ejecución (en la instrucción que lógicamente le sigue al wait).
Signal and Wait: el proceso que hace el signal pasa a competir por acceder nuevamente al monitor, mientras que el proceso despertado pasa a ejecutar dentro del monitor a partir de instrucción que lógicamente le sigue al wait.

<table width="300">
    <th>Wait</th>
    <th>P</th>
    <tr>
        <td> El proceso siempre se<br> duerme. </td>
        <td> El proceso sólo se<br>duerme si el semáforo es 0. </td>
    </tr>
    <th>Signal</th>
    <th>V</th>
    <tr>
        <td> Si hay procesos dormidos despierta al<br> primero de ellos. En caso contrario no<br> tiene efecto posterior. </td>
        <td> Incrementa el semáforo para que un<br> proceso dormido o que hará un P continue.<br> No sigue ningún orden al<br> despertarlos. </td>
    </tr>
</table>

## Técnicas de Sincronización

- Passing the Conditions
    ````
    monitor Semaforo
    { int s = 1, espera = 0; cond pos;
        procedure P ()
        { if (s == 0) { espera ++; wait(pos);}
            else s = s-1;
        };
        procedure V ()
        { if (espera == 0 ) s = s+1
            else { espera --; signal(pos);}
        };
    };
    ````

- Alocación SJN
    ````
    monitor Shortest_Job_Next
    { bool libre = true;
        cond turno[N];
        cola espera;
        procedure request (int id, int tiempo)
        { if (libre) libre = false
            else { insertar_ordenado(espera, id, tiempo);
                wait (turno[id]);
            };
        };
        procedure release ()
        { if (empty(espera)) libre = true
            else { sacar(espera, id);
                signal(turno[id]);
            };
        };
    }
    ````

- Buffer Limitado
    ````
    monitor Buffer_Limitado
    { typeT buf[n];
        int ocupado = 0, libre = 0; cantidad = 0;
        cond not_lleno, not_vacio;
        procedure depositar(typeT datos)
        { while (cantidad == n) wait (not_lleno);
            buf[libre] = datos;
            libre = (libre+1) mod n;
            cantidad++;
            signal(not_vacio);
        }
        procedure retirar(typeT &resultado)
        { while (cantidad == 0) wait(not_vacio);
            resultado=buf[ocupado];
            ocupado=(ocupado+1) mod n;
            cantidad--;
            signal(not_lleno);
        }
    }
    ````

- Lectores y Escritores
    ````
    monitor Controlador_RW
    { int nr = 0, nw = 0;
        cond ok_leer, ok_escribir
        procedure pedido_leer( )
        { while (nw > 0) wait (ok_leer);
            nr = nr + 1;
        }
        procedure libera_leer( )
        { nr = nr - 1;
            if (nr == 0) signal (ok_escribir);
        }
        procedure pedido_escribir( )
        { while (nr>0 OR nw>0) wait (ok_escribir);
            nw = nw + 1;
        }
        procedure libera_escribir( )
        { nw = nw - 1;
            signal (ok_escribir);
            signal_all (ok_leer);
        }
    }
    ````

- Diseño de reloj lógico
    ````
    monitor Timer
    { int hora_actual = 0;
        cond espera[N];
        colaOrdenada dormidos;
        procedure demorar(int intervalo, int id)
        { int hora_de_despertar;
            hora_de_despertar = hora_actual + intervalo;
            Insertar(dormidos, id, hora_de_despertar);
            wait(espera[id]);
        }
        procedure tick( )
        { int aux, idAux;
            hora_actual = hora_actual + 1;
            aux = verPrimero (dormidos);
            while (aux <= hora_actual)
            { sacar (dormidos, idAux)
                signal (espera[idAux]);
                aux = verPrimero (dormidos);
            }
        }
    }
    ````

- Rendezvous
    ````
    monitor Peluqueria {
        int peluquero = 0, silla = 0, abierto = 0;
        cond peluquero_disponible, silla_ocupada, puerta_abierta, salio_cliente;
        procedure corte_de_pelo() {
            while (peluquero == 0) wait (peluquero_disponible);
            peluquero = peluquero - 1;
            signal (silla_ocupada);
            wait (puerta_abierta);
            signal (salio_cliente);
        }
        procedure proximo_cliente(){
            peluquero = peluquero + 1;
            signal(peluquero_disponible);
            wait(silla_ocupada);
        }
        procedure corte_terminado() {
            signal(puerta_abierta);
            wait(salio_cliente);
        }
    }
    ````