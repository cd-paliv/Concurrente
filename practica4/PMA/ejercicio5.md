Resolver la administración de las impresoras de una oficina. Hay 3 impresoras, N usuarios y 1 director. Los usuarios y el director están continuamente trabajando y cada tanto envían documentos a imprimir. Cada impresora, cuando está libre, toma un documento y lo imprime, de acuerdo con el orden de llegada, pero siempre dando prioridad a los pedidos del director.
    Nota: los usuarios y el director no deben esperar a que se imprima el documento.

````C
chan ImprimirU(texto);
chan ImprimirD(texto);
chan Senial(bool);
chan Pedido(texto);

Process Usuario[id: 0..N-1]{
    texto documento;
    while(true){
        //Trabaja duro duro
        send ImprimirU(documento);
        send Senial(true);
    }
}

Process Director{
    texto documentoMasImportante;
    while(true){
        // No trabaja nada nada
        send ImprimirD(documentoMasImportante);
        send Senial(true);
    }
}

// Usar 'coordinador' cuando hay N a N y se verifica por empty
Process Coordinador{
    bool cond;
    while(true){
        receive Senial(cond);
        if(empty(ImprimirD) & not empty(ImprimirU)){
            receive ImprimirU(doc);
        } else receive ImprimirD(doc);
        send Pedido(doc);
    }
}

Process Impresora[id: 0..2]{
    texto doc;
    while(true){
        receive Pedido(doc);
        //Imprime
    }
}
````