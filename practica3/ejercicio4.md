Existen N vehículos que deben pasar por un puente de acuerdo con el orden de llegada. Considere que el puente no soporta más de 50000kg y que cada vehículo cuenta con su propio peso (ningún vehículo supera el peso soportado por el puente).

````C
monitor Puente
    cond cola;
    int espera = 0, pesoPuente = ..., pesoAux;
    bool libre = true;
    
    procedure EntrarPuente(pesoAuto: in int)
        if(not libre | (pesoPuente+pesoAuto) > 50000){
            Push(fila, pesoAuto)
            espera++;
            wait(cola);
        }
        libre = false;
        pesoPuente =+ pesoAuto;
    end;

    procedure SalirPuente(pesoAuto: in int)
        pesoPuente =- pesoAuto;
        if(espera > 0){
            Pop(fila, pesoAux)
            esperando--;
            signal(cola);
        }
        else libre = true;
    end;
end monitor;

process Vehículo[id: 0..N-1]
    Puente.EntrarPuente(peso);
    PasarPuente();
    Puente.SalirPuente(peso);
end process;
````