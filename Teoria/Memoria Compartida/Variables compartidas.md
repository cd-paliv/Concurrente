
# Variables compartidas: dos implementaciones:
- Grano grueso: Sincronizacion(sentencias await).
  - Await general: <await (s>0) s=s-1;>
  - Await para exclusion mutua: <x=x+1; y=y+1;>
- Grano fino: tiene como objetivo hacer atomico el await de grano grueso. Se implementa de dos maneras: Locks (implementacion de acciones atomicas en software) y Barreras (punto de sincronizacion que todos los procesos deben alcanzar para que cualquier proceso pueda continuar).
  - Spin Locks: los procesos se quedan iterando mientras esperan que se limpie lock.
    - Test-and-Set (TS): escribe siempre en lock aunque el valor no cambie.
    - Tie-Braker: tiene una variable que indica que el proceso comenzo a ejecutar su protocolo de entrada a la seccion critica y una variable de acceso restringido que indica que proceso es el proximo a entrar en su seccion critica.
    - Ticket: se reparten numeros y se espera a que sea el turno. Los procesos toman un numero mayor que el de cualquier otro que espera ser atendido; luego esperan hasta que todos los procesos con numero mas chico hayan sido atendidos.
    - Bakery: cada proceso que trata de ingresar recorre los numeros de los demas y se auto-asigna uno mayor. Luego esprea a que su numero sea el menor de los que esperan. Los procesos se chequean entre ellos y no contra un global.
  - Sincronizacion Barrier: una barrera es un punto de demora a la que deben llegar todos los procesos antes de permitirles pasar y continuar su ejecucion.
    - Contador compartido: todos los procesos incrementan una variable compartida al llegar a la barrera; cuando dicha variable es n (siendo n la cantidad de procesos), los procesos pueden pasar la barrera.
    ```
    int arribo[1:n] = ([n] 0), continuar[1:n] = ([n] 0);
    process Worker[i=1 to n]
    { while (true)
        { código para implementar la tarea i;
            arribo[i] = 1;
            while (continuar[i] == 0) skip;
            continuar[i] = 0;
        }
    }
    process Coordinador
    { while (true)
        { for [i = 1 to n]
            { while (arribo[i] == 0) skip;
                arribo[i] = 0;
            }
            for [i = 1 to n] continuar[i] = 1;
        }
    }
    ```
    -