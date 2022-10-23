Suponga que N clientes llegan a la cola de un banco y que serán atendidos por sus empleados. Analice el problema y defina qué procesos, recursos y comunicaciones serán necesarios/convenientes para resolver el problema. Luego, resuelva considerando las siguientes situaciones:

a. Existe un único empleado, el cual atiende por orden de llegada.
````C
chan Turno(int);
chan Comprobate[N](texto);

Process Cliente[id: 0..N-1]{
    texto Comp;
    send Turno(id);
    receive Comprobante[id](Comp);
}

Process Empleado{
    int idAux;
    texto comp;
    while(true){
        receive Turno(idAux);
        comp = GenerarComprobante();
        send Comprobante[idAux](comp);
    }
}
````

b. Ídem a) pero considerando que hay 2 empleados para atender, ¿qué debe modificarse en la solución anterior?
No es necesario modificar nada mas que la cantidad de empleados.
````C
chan Turno(int);
chan Comprobate[N](texto);

Process Cliente[id: 0..N-1]{
    texto Comp;
    send Turno(id);
    receive Comprobante[id](Comp);
}

Process Empleado[id: 0..1]{
    int idAux;
    texto comp;
    while(true){
        receive Turno(idAux);
        comp = GenerarComprobante();
        send Comprobante[idAux](comp);
    }
}
````

c. Ídem c) pero considerando que, si no hay clientes para atender, los empleados realizan tareas administrativas durante 15 minutos. ¿Se puede resolver sin usar procesos adicionales? ¿Qué consecuencias implicaría?
Se necesitaria un proceso coordinador que reciba los turnos por un unico canal y que los empleados le pidan a ese proceso el siguiente turno. De esta forma, no se genera busy waiting y no seria necesario que cada cliente decida por que empleado ser atendido.
````C
chan Turno(int);
chan Siguiente[2](int);
chan Pedido(int);
chan Comprobate[N](texto);

Process Cliente[id: 0..N-1]{
    texto Comp;
    send Turno(id);
    receive Comprobante[id](Comp);
}

Process Empleado[id: 0..1]{
    int idAux;
    texto comp;
    while(true){
        send Pedido(id);
        receive Siguiente[id](idAux);
        if(idAux <> -1) {comp = GenerarComprobante();
            send Comprobante[idAux](comp);
        }
        delay (15 minutos);
    }
}

Process Coordinador{
    int idCliente, idEmp;
    while(true){
        receive Pedido(idEmp);
        if(empty(Turno)) idCliente = -1;
        else receive Turno(idCliente);
        send Siguiente[idEmp](idCliente);
    }
}
````