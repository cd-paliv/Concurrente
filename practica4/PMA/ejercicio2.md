Se desea modelar el funcionamiento de un banco en el cual existen 5 cajas para realizar pagos. Existen P clientes que desean hacer un pago. Para esto, cada una selecciona la caja donde hay menos personas esperando; una vez seleccionada, espera a ser atendido. En cada caja, los clientes son atendidos por orden de llegada por los cajeros. Luego del pago, se les entrega un comprobante.
    Nota: maximizando la concurrencia.

````C
chan Turno[5](int);
chan Comprobante[P](texto);
int cantPersonasCaja[5] = ([5] 0);

Proccess Cajero[id: 0..4]{
    int idC;
    texto comp;
    while(true){
        receive Turno[id](idC);
        comp = GenerarComprobante();
        send Comprobante[idC](comp);
    }
}

Process Cliente[id: 0..P-1]{
    texto comp;
    int nroCaja = Minimo();
    cantPersonasCaja[nroCaja]++;
    send Turno[nroCaja](id);
    receive Comprobante[id](comp);
}
````