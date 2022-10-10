Se debe simular una maratón con C corredores donde en la llegada hay UNA máquina expendedoras de agua con capacidad para 20 botellas. Además, existe un repositor encargado de reponer las botellas de la máquina. Cuando los C corredores han llegado al inicio comienza la carrera. Cuando un corredor termina la carrera se dirigen a la máquina expendedora, espera su turno (respetando el orden de llegada), saca una botella y se retira. Si encuentra la máquina sin botellas, le avisa al repositor para que cargue nuevamente la máquina con 20 botellas; espera a que se haga la recarga; saca una botella y se retira.
    Nota: mientras se reponen las botellas se debe permitir que otros corredores se encolen.

````C
monitor Carrera
    cond comenzar;

    procedure Correr()
        if(cantC == C) signal_all(comenzar);
        else wait(comenzar);
    end;
end monitor;

monitor MaquinaExpendedora
    cond esperaBotella, repo;
    int cantBotellas = 20;

    procedure AgarrarBotella(idC: in int)
        if(cantBotellas == 0){
            signal(repo)
            wait(esperaBotella)
        }
        cantBotellas--;
    end;

    procedure ReponerBotellas
        while(true){
            if(cantBotellas < 0) wait(repo)
            // reponer botellas
            cantBotellas = 20
            signal(esperaBotella)
        }
    end;
end monitor;

monitor Cola
    cond siguiente[C];
    colaOrdenada cola;
    int cant = 1, espera = 0;

    procedure Encolar(idC: in int)
        if(cant == 0){
            Push(cola, idC);
            espera++;
            wait(siguiente[idC])
        } else cant--;
    end;  

    procedure Desencolar()
        if(espera == 0) cant++;
        else{
            int idAux;
            Pop(cola, idAux);
            espera--;
            signal(siguiente[idAux]);
        }

    end;
end monitor;

process Corredor[id: 0..C]
    Carrera.Correr();
    Cola.Encolar(id);
    MaquinaExpendedora.AgarraBotella()
    Cola.Desencolar();
end process;

process Repositor
    MaquinaExpendedora.ReponerBotellas()
end process;
````