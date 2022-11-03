En una exposición aeronáutica hay un simulador de vuelo (que debe ser usado con exclusión mutua) y un empleado encargado de administrar su uso. Hay P personas que esperan a que el empleado lo deje acceder al simulador, lo usa por un rato y se retira. El empleado deja usar el simulador a las personas respetando el orden de llegada.
    Nota: cada persona usa sólo una vez el simulador.

````C
Process Persona[id: 0..P-1]{
    Encargado!llegue(id)
    Encargado?usar()
    // Usa simulador
    Encargado!listo()
}

Process Encargado{
    cola Buffer;
    int idP;
    bool libre;
    do Persona[*]?llegue(idP) -> if libre: Persona[idP]!usar();
                                            libre=false;
                                else: Push(Buffer, idP);
    do Persona[*]?listo() -> if empty(Buffer): libre=true;
                                else: Persona[Pop(Buffer)]!usar();
    od
}
````