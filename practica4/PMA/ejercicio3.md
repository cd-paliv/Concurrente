Se debe modelar el funcionamiento de una casa de comida rápida, en la cual trabajan 2 cocineros y 3 vendedores, y que debe atender a C clientes. El modelado debe considerar que:
    - Cada cliente realiza un pedido y luego espera a que se lo entreguen.
    - Los pedidos que hacen los clientes son tomados por cualquiera de los vendedores y se lo pasan a los cocineros para que realicen el plato. Cuando no hay pedidos para atender, los vendedores aprovechan para reponer un pack de bebidas de la heladera (tardan entre 1 y 3 minutos para hacer esto).
    - Repetidamente cada cocinero toma un pedido pendiente dejado por los vendedores, lo cocina y se lo entrega directamente al cliente correspondiente.
Nota: maximizar la concurrencia.

````C
chan Pedidos(int, texto);
chan Atencion(int);
chan Siguiente[3](int, texto);
chan Comanda(int, texto);
chan Entrega[C](int);


Process Cliente[id: 0..C-1]{
    texto miPedido = ...;
    send Pedidos(id, miPedido);
    receive Entrega[id](miPedido);
}

Process Coordinador{
    int idCliente, idVendedor;
    texto p;
    while(true){
        receive Atencion(idVendedor);
        if(empty(Pedidos)) {
            idCliente = -1;
            p = '';
        } else receive Pedidos(idCliente, p);
        send Siguiente[idVendedor](idCliente, p);
    }
}

Process Vendedor[id: 0..2]{
    int idC;
    texto pedido;
    while(true){
        send Atencion(id);
        receive Siguiente[id](idC, pedido);
        if(idC <> -1){
            send Comanda(idC, pedido);
        }
        delay(1 a 3 minutos);
    }
}

Process Cocinero[id: 0..1]{
    int idC;
    texto p;
    while(true){
        receive Comanda(idC, p);
        // Cocina el pedido
        send Entrega[idC](p);
    }
}
````