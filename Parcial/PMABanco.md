Resolver con PMA: se debe simular la atencion de un banco con 3 cajas para atender a N clientes que pueden ser especiales o regulares. Cuando el cliente llega al banco se dirige a la caja con menos personas esperando y se queda ahi hasta que lo terminan de atender y le dan el comprobante de pago. Las cajas atienden a personas de acuerdo al orden de llegada pero dando prioridad a los clientes epeciales.
    Nota: maximizar la concurrencia.

``` C
chan ClienteE[3](int);
chan ClienteR[3](int);
chan Senial[3]();
chan SenialA();
chan PedirCaja(int);
chan darNum[N](int);
chan Salir();
chan Comprobante[N](texto);


Process Cliente[id: 0..N-1]{
    texto comp;
    int nroCaja;
    send PedirCaja(id);
    send SenialA();
    receive darNum[id](nroCaja);

    if (esRegular) send ClienteR[nroCaja](id);
    else send ClienteE[nroCaja](id);
    send Senial[nroCaja](true);
    receive Comprobante[id](comp);

    send Libre[nroCaja]();
    send Salir(nroCaja);
    send SenialA();
}

Process Admin{
    int idV, nroCaja, personasEnCaja[3] = ([3] 0);
    while(true){
        receive SenialA();
        if(empty(Salir) & not empty(PedirCaja)){
            receive PedirCaja(idV);
            nroCaja = minimo(personasEnCaja);
            send darNum[idV](nroCaja);
            personasEnCaja[nroCaja]++;
        } elif not empty(Salir) {
            receive Salir(nroCaja);
            personasEnCaja[nroCaja]--;
        }
    }
}

Process Caja[id:0..2]{
    receive Senial[id]();
    if(not empty(ClienteE[id])) receive ClienteE[id](idC);
    else receive ClienteR[id](idC);
    comp = GenerarComp();
    send Comprobante[idC](comp);
}
```