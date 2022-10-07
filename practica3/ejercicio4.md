Existen N vehículos que deben pasar por un puente de acuerdo con el orden de llegada. Considere que el puente no soporta más de 50000kg y que cada vehículo cuenta con su propio peso (ningún vehículo supera el peso soportado por el puente).

````
monitor Puente
    cond cola;
    int espera = 0;
    bool libre = true;
    
    procedure EntrarPuente()
        if(peso < 50000){
            if(not libre){espera++; wait(cola)}
            else libre = false;
        }
    end;

    procedure SalirPuente()
        if(espera > 0) {esperando--; signal(cola)}
        else libre = true;
    end;
end monitor;

process Vehículo[id: 0..N-1]
    Puente.EntrarPuente(peso);
    PasarPuente();
    Puente.SalirPuente();
end process;
````