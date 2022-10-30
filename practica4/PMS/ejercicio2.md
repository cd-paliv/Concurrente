En un laboratorio de genética veterinaria hay 3 empleados. El primero de ellos continuamente prepara las muestras de ADN; cada vez que termina, se la envía al segundo empleado y vuelve a su trabajo. El segundo empleado toma cada muestra de ADN preparada, arma el set de análisis que se deben realizar con ella y espera el resultado para archivarlo. Por último, el tercer empleado se encarga de realizar el análisis y devolverle el resultado al segundo empleado.

````C
Process Empleado1{
    texto muestra;
    while(true){
        muestra = PrepararMuestra();
        AdminPreparación!(muestra)
    }
}

Process AdminPreparación{
    cola Buffer;
    texto m;
    do Empleado1?(m) -> Push(Buffer, m)
    do not empty(Buffer);
        Empleado2?pedido() -> Empleado2!(Pop(Buffer))
    od
}

Process Empleado2{
    texto m, res;
    while(true){
        AdminPreparación!pedido()
        AdminPreparación?(m)
        set = ArmarSet(m)
        AdminAnálisis!(set)
        Empleado3?(res)
        // Archiva res
    }
}

Process AdminAnálisis{
    cola Buffer;
    texto set, res;
    do Empleado2?(set) -> Push(Buffer, set)
    do not empty(Buffer);
        Empleado3?pedido() -> Empleado3!(Pop(Buffer))
    od
}

Process Empleado3{
    texto r;
    while(true){
        AdminAnálisis!pedido();
        AdminAnálisis?(r);
        r = RealizarAnálisis()
        Empleado2!(r)
    }
}
````