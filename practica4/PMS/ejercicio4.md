En una exposición aeronáutica hay un simulador de vuelo (que debe ser usado con exclusión mutua) y un empleado encargado de administrar su uso. Hay P personas que esperan a que el empleado lo deje acceder al simulador, lo usa por un rato y se retira. El empleado deja usar el simulador a las personas respetando el orden de llegada.
    Nota: cada persona usa sólo una vez el simulador.

````C
Process Simulador{
    while(true){
        Persona?listo()
        Encargado!listo()
    }
}

Process Persona[id: 0..P-1]{
    Admin!(id)
    Encargado?usar()
    // Usa simulador
    Simulador!listo()
}

Process Admin{
    cola Buffer;
    int idP;
    do Persona[*]!(idP) -> Push(Buffer, idP)
    do not empty(Buffer);
        Encargado?pedido() -> Encargado!siguiente(Pop(Buffer))
    od
}

Process Encargado{
    int idP;
    while(true){
        Admin!pedido()
        Admin?siguiente(idP)
        Persona[idP]!usar()
        Simulador?listo()
    }
}
````