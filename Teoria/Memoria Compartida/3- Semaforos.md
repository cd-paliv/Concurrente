# Semáforos

Es una instancia de un tipo de datos abstracto (o un objeto) con sólo 2 operaciones atómicas:
- V -> señala la ocurrencia de un evento (incrementa).
- P -> se usa para demorar un proceso hasta que ocurra un evento (decrementa).
Los semáforos SI O SI se deben inicializar, no es posible ver ni modificar el valor de un semáforo en otro momento que no sea la declaración. Se pueden declarar en variables o en arreglos (sem fork[5] = ([5] 1), donde se inicializan las cinco posiciones del arreglo con valor 1).

#### Hay dos tipos de semáforos:
- Semáforo general: el valor interno que toma es mayor o igual que 0 sin una cota superior.
  - P(s): <await(s > 0) s=s-1;>
  - V(s): <s=s+1>
- Semáforo binario: sólo pueden tomar los valores 0 o 1.
  - P(b): <await(b > 0) b=b-1;>
  - V(b): <await(b < 0) b=b+1;>

##### Técnicas básicas para resolver problemas repetitivos de concurrencia con semáforos:
- Exclusión mutua: se espera a que la sección crítica esté libre.
      ´´´
      sem free = 1;
      process SC[i=1 to n]
      {
        while(true)
        { P(free);
          sección crítica;
          V(free);
          sección no crítica;
        }
      }
      ´´´
  - Exclusión mutua selectiva: exclusion mutua entre procesos que compiten por el acceso a conjuntos superpuestos de variables compartidas: Problema de los filósofos.
- Barreas: se tiene un semáforo para cada flag de sincronización. Un proceso setea el flah ejecutando V (se encuentra en la barrera), y espera a que un flah sea seteado y luego lo limpia ejecutando P (pasó la barrera).
      ´´´
      sem llega1=0, llega2=0;
      process Worker1
      { ...
        V(llega1); P(llega2);
        ...
      }
      process Worker2
      { ...
        V(llega2); P(llega1);
        ...
      }
      ´´´
- Semáforo binario dividido (Split Binary Semaphore): cada semáforo es divido en n partes (la suma de los semáforos involucradas nunca es mayor que 1). Por ejemplo, en el caso de productores y consumidores:
      ´´´
      typeT buf; sem vacio = 1, lleno = 0;
      process Productor [i = 1 to M]
      { while(true)
        { ...
          producir mensaje datos
          P(vacio); buf = datos; V(lleno); #depositar
        }
      }
      process Consumidor[j = 1 to N]
      { while(true)
        { P(lleno); resultado = buf; V(vacio); #retirar
          consumir mensaje resultado
          ...
        }
      }
      ´´´
- Contadores de Recursos: se utiliza un vector de manera circular; si está posicionado en la última posición del vector, la próxima posición es la primera. Cada semáforo cuenta el número de unidades libres de un recurso determinado. Esta forma de utilización es adecuada cuando los procesos compiten por recursos de múltiples unidades.
    ```
      typeT buf[n]; int ocupado = 0, libre = 0;
      sem vacio = n, lleno = 0;
      sem mutexD = 1, mutexR = 1;
      process Productor [i = 1..M]
      { while(true)
        { //producir mensaje datos
          P(vacio);
          P(mutexD); buf[libre] = datos; libre = (libre+1) mod n; V(mutexD);
          V(lleno);
        }
      }
      process Consumidor [i = 1..N]
      { while(true)
        { P(lleno);
          P(mutexR); resultado = buf[ocupado]; ocupado = (ocupado+1) mod n; V(mutexR);
          V(vacio);
          //consumir mensaje resultado
        }
      }
    ```
- Passing de Baton: técnica general para implementar sentencias await. Cuando un proceso está dentro de una SC mantiene el baton, que significa permiso para ejecutar. Cuando el proceso llega a una SIGNAL (es decir, sale de la SC), pasa el baton (control) a otro proceso. Si ningún proceso está esperando el baton, este se libera para que lo tome el próximo que lo necesite.
      ´´´
      
      ´´´
- 