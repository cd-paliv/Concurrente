Resolver con PASAJE DE MENSAJES ASINCRÓNICOS (PMA) el siguiente problema. Se debe simular la atención en un peaje con 7 cabinas para atender a N vehículos (algunos de ellos son ambulancias). Cuando el vehículo llega al peaje se dirige a la cabina con menos vehículos esperando y se queda ahí hasta que lo terminan de atender y le dan el ticket de pago. Las cabinas atienden a los vehículos que van a ella de acuerdo al orden de llegada pero dando prioridad a las ambulancias; cuando terminan de atender a un vehículo le dan el ticket de pago. Nota: maximizar la concurrencia.

``` C
chan PeajeV[7](int);
chan PeajeA[7](int);
chan Comp[N](texto);
chan Senial(bool);
int cantVehiculosEsperando[7] = ([7] 0);

Process Vehiculo[id: 0..N-1]{
    int peaje;   
    texto miComp;
    texto tipo = ...;
    peaje = getPeajeConMenosVehiculos();
    cantVehiculosEsperando[id]++;
    if(tipo == 'Ambulancia'){
        send PeajeA[peaje](id);
    } else{
        send PeajeV[peaje](id);
    }
    send Senial[peaje](true);
    receive Comp[id](miComp);
}

Process Empleado[id: 0..6]{
    bool cond;
    int idV;
    texto compAct;
    while(true){
        receive Senial[id](cond);
        if(empty(PeajeA) & not empty(PeajeV)){
            receive PeajeV[id](idV);
            compAct = AtenderYGenerarComprobante();
            send Comp[idV](compAct);
        } else if (not empty(PeajeA)){
            receive PeajeA[id](idV);
            compAct = AtenderYGenerarComprobante();
            send Comp[idA](compAct);
        }
        cantVehiculosEsperando[id]--;
    }
}



```