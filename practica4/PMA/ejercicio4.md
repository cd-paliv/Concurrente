Simular la atención en un locutorio con 10 cabinas telefónicas, el cual tiene un empleado que se encarga de atender a N clientes. Al llegar, cada cliente espera hasta que el empleado le indique a qué cabina ir, la usa y luego se dirige al empleado para pagarle. El empleado atiende a los clientes en el orden en que hacen los pedidos, pero siempre dando prioridad a los que terminaron de usar la cabina. A cada cliente se le entrega un ticket factura.
    Nota: maximizar la concurrencia; suponga que hay una función Cobrar() llamada por el empleado que simula que el empleado le cobra al cliente.

````C
chan Turno(int);
chan Listo(int);
chan Senial(bool)
chan Cabina[N](int);
chan Comprobante[N](int);
cola cabinasDisponibles; //Cabina esta disponible si esta en 1, ocupada si esta en 0

Process Cliente[id: 0..N-1]{
    int nroCabina;
    send Turno(id);
    send Senial(true);
    receive Cabina[id](nroCabina);
    // Usa cabina
    send Listo(id, nroCabina);
    send Senial(true);
    receive Comprobante[id](ticket);
}

Process Empleado{
    int idC, cabinaC, total;
    bool cond;
    while(true){
        receive Senial(cond);
        if(empty(Listo) & not empty(Turno) & not empty cabinasDisponibles){
            receive Turno(idC);
            cabinaC = ElegirCabinaLibre(cabinasDisponibles);
            send Cabina[idC](cabinaC);
        }else if not empty(Listo){
            receive Listo(idC, nroC);
            Push(cabinaDisponbiel, nro C)
            total = Cobrar();
            send Comprobante[idC](total);
        }
    }
}
````