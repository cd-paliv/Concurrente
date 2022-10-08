Existen N personas que deben fotocopiar un documento. La fotocopiadora sólo puede ser usada por una persona a la vez. Analice el problema y defina qué procesos, recursos y monitores serán necesarios/convenientes, además de las posibles sincronizaciones requeridas para resolver el problema. Luego, resuelva considerando las siguientes situaciones:
1. Implemente una solución suponiendo no importa el orden de uso. Existe una función Fotocopiar() que simula el uso de la fotocopiadora.
    ````C
    monitor Fotocopiadora
        cond cola;
        int cant = 1;

        procedure Acceso()
            if(cant == 0) wait(cola); //Si no hay nadie usando la fotocopiadora, la uso yo
            cant--;
        end;

        procedure Liberar()
            cant++;
            signal(cola);
        end;
    end monitor;
    
    process Persona[id: 0..N-1]
        Fotocopiadora.Acceso();
        Fotocopiar();
        Fotocopiadora.Liberar();
    end process;
    ````

2. Modifique la solución de (1) para el caso en que se deba respetar el orden de llegada.
    ````C
    monitor Fotocopiadora
        cond cola;
        int cant = 1, espera = 0;

        procedure Acceso()
            if(cant == 0) {espera++; wait(cola);}
            else cant--;
        end;

        procedure Liberar()
            if(espera == 0) cant++; //Si no hay alguien esperando, libero la fotocopiadora
            else {espera--; signal(cola);}
        end;
    end monitor;
    
    process Persona[id: 0..N-1]
        Fotocopiadora.Acceso();
        Fotocopiar();
        Fotocopiadora.Liberar();
    end process;
    ````

3. Modifique la solución de (2) para el caso en que se deba dar prioridad de acuerdo con la edad de cada persona (cuando la fotocopiadora está libre la debe usar la persona de mayor edad entre las que estén esperando para usarla).
    ````C
    monitor Fotocopiadora
        cond cola[N];
        int cant = 1, espera = 0, idAux;
        colaOrdenada fila;

        procedure Acceso(idP, edad: in int)
            if(cant == 0) {
                InsertarOrdenadoPorMayor(fila, idP, edad)
                espera++;
                wait(cola[idP]);
            }
            else cant--;
        end;

        procedure Liberar()
            if(espera == 0) cant++;
            else {
                espera--;
                Pop(fila, idAux)
                signal(cola[idAux]);
            }
        end;
    end monitor;
    
    process Persona[id: 0..N-1]
        Fotocopiadora.Acceso(id, edad);
        Fotocopiar();
        Fotocopiadora.Liberar();
    end process;
    ````

4. Modifique la solución de (1) para el caso en que se deba respetar estrictamente el orden dado por el identificador del proceso (la persona X no puede usar la fotocopiadora hasta que no haya terminado de usarla la persona X-1).
    ````C
    monitor Fotocopiadora
        cond cola[N];
        int idAux = 0;

        procedure Acceso(idP: in int)
            if(idAux <> idP) wait(cola[idP]);
        end;

        procedure Liberar()
            idAux++;
            if(idAux < N) signal(cola[idAux]);
        end;
    end monitor;
    
    process Persona[id: 0..N-1]
        Fotocopiadora.Acceso();
        Fotocopiar();
        Fotocopiadora.Liberar();
    end process;
    ````

5. Modifique la solución de (2) para el caso en que además haya un Empleado que le indica a cada persona cuando debe usar la fotocopiadora.
    ````C
    monitor Fotocopiadora
        colaOrdenada fila;
        int idAux, hayFila = 0;
        cond espera[N], hayPersonas;

        procedure Acceso(idP: in int)
            Push(fila, idP)
            signal(hayPersonas)
            wait(espera[idP])
        end;

        procedure Siguiente()
            wait(hayPersonas)
            Pop(fila, idAux)
            signal(espera[idAux])
        end;
    end monitor;
    
    process Empleado
        while(true) Fotocopiadora.Siguiente();
    end process;

    process Persona[id: 0..N-1]
        Fotocopiadora.Acceso(id);
        Fotocopiar();
    end process;
    ````

6. Modificar la solución (5) para el caso en que sean 10 fotocopiadoras. El empleado le indica a la persona cuál fotocopiadora usar y cuándo hacerlo.
    ````C
    monitor Fotocopiadora
        colaOrdenada fila, filaImp;
        int idAux, hayFila = 0, impresora[N], imp;
        cond espera[N], hayPersonas, hayImpresora;

        procedure Acceso(idP: in int, miImp: out int)
            Push(fila, idP)
            signal(hayPersonas)
            wait(espera[idP])
            imp = impresora[id];
        end;

        procedure Siguiente()
            wait(hayPersonas)
            Pop(fila, idAux)
            wait(hayImpresora)
            impresora[idAux] = Pop(filaImp, imp)
            signal(espera[idAux])
        end;

        procedure Liberar(miImp: in int)
            Push(filaImp, miImp)
            signal(hayImpresora)
        end;
    end monitor;
    
    process Empleado
        while(true) Fotocopiadora.Siguiente();
    end process;

    process Persona[id: 0..N-1]
        int impresora;
        Fotocopiadora.Acceso(id, impresora);
        Fotocopiar();
        Fotocopiadora.Liberar();
    end process;
    ````