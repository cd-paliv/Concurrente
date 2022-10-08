Existen N procesos que deben leer información de una base de datos, la cual es administrada
por un motor que admite una cantidad limitada de consultas simultáneas.

1. Analice el problema y defina qué procesos, recursos y monitores serán necesarios/convenientes, además de las posibles sincronizaciones requeridas para resolver el problema.

   ````C
    Creo requerir un monitor "Motor" que tenga los procesos SolicitarAcceso() y LiberarAcceso(), además de una base de datos como recurso.
   ````
2. Implemente el acceso a la base por parte de los procesos, sabiendo que el motor de
   base de datos puede atender a lo sumo 5 consultas de lectura simultáneas.

   ````C
    monitor Motor
        cond cola;
        int cantConsultas = 0;

        procedure SolicitarAcceso()
            if(cantConsultas == 4) wait(cola);
            cantConsultas++;
        end;

        procedure LiberarAcceso()
            cantConsultas--;
            signal(cola);
        end;
    end monitor;

    process Consultor[id: 0..N]
        Motor.SolicitarAcceso();
        RealizarConsulta();
        Motor.LiberarAcceso();
    end process;
   ````
