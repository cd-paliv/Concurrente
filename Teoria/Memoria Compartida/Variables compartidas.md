
# Variables compartidas: dos implementaciones:
- Grano grueso: Sincronizacion(sentencias await).
  - Await general: `<await (s>0) s=s-1;>`
  - Await para exclusion mutua: `<x=x+1; y=y+1;>`
- Grano fino: tiene como objetivo hacer atomico el await de grano grueso. Se implementa de dos maneras: Locks (implementacion de acciones atomicas en software) y Barreras (punto de sincronizacion que todos los procesos deben alcanzar para que cualquier proceso pueda continuar).
  - Spin Locks: los procesos se quedan iterando mientras esperan que se limpie lock.
    - Test-and-Set (TS): escribe siempre en lock aunque el valor no cambie.
      ```
        bool lock=false;
        process SC[i=1 to n]
        { while (true)
            { while (TS(lock)) skip ;
                sección crítica;
                lock = false;
                sección no crítica;
            }
        }
      ```
    - Tie-Braker: tiene una variable que indica que el proceso comenzo a ejecutar su protocolo de entrada a la seccion critica y una variable de acceso restringido que indica que proceso es el proximo a entrar en su seccion critica.
        - Solucion "Grano Fino":
        ```
          bool in1 = false, in2 = false;
          int ultimo = 1;
          process SC1
          { while (true)
            { in1 = true; ultimo = 1;
                while (in2 and ultimo == 1) skip;
                sección crítica;
                in1 = false;
                sección no crítica;
            }
          }
          process SC2
          { while (true)
            { in2 = true; ultimo = 2;
                while (in1 and ultimo == 2) skip;
                sección crítica;
                in2 = false;
                sección no crítica;
            }
          }
        ```
        - Solucion "Fair":
        ```
          int in[1:n] = ([n] 0), ultimo[1:n] = ([n] 0);
          process SC[i = 1 to n]
          { while (true) 
            { for [j = 1 to n] # protocolo de entrada
                # el proceso i está en la etapa j y es el último
                { in[i] = j; ultimo[j] = i;
                    for [k = 1 to n st i <> k]
                    { # espera si el proceso k está en una etapa más alta
                        # y el proceso i fue el último en entrar a la etapa j
                        while (in[k] >= in[i] and ultimo[j]==i) skip;
                    }
                }
                sección crítica;
                in[i] = 0;
                sección no crítica;
            }
          }
        ```
    - Ticket: se reparten numeros y se espera a que sea el turno. Los procesos toman un numero mayor que el de cualquier otro que espera ser atendido; luego esperan hasta que todos los procesos con numero mas chico hayan sido atendidos.
      ```
        FA(var,incr): <temp=var; var=var+incr; return(temp)>
        
        int numero = 1, proximo = 1, turno[1:n] = ( [n] 0 );
        process SC [i: 1..n]
            { while (true)
                { turno[i] = FA (numero, 1);
                    while (turno[i] <> proximo) skip;
                    sección crítica;
                    proximo = proximo + 1;
                    sección no crítica;
            }
        }
      ```
    - Bakery: cada proceso que trata de ingresar recorre los numeros de los demas y se auto-asigna uno mayor. Luego esprea a que su numero sea el menor de los que esperan. Los procesos se chequean entre ellos y no contra un global.
      ```python
        int turno[1:n] = ([n] 0);
        process SC[i = 1 to n]
        { while (true)
            { turno[i] = 1; //indica que comenzó el protocolo de entrada
                turno[i] = max(turno[1:n]) + 1;
                for [j = 1 to n st j != i] //espera su turno
                    while (turno[j] != 0) and ( (turno[i],i) > (turno[j],j) ) → skip;
                sección crítica
                turno[i] = 0;
                sección no crítica
            }
        }
      ```
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