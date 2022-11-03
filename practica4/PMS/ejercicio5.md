En un estadio de fútbol hay una máquina expendedora de gaseosas que debe ser usada por E Espectadores de acuerdo al orden de llegada. Cuando el espectador accede a la máquina en su turno usa la máquina y luego se retira para dejar al siguiente.
    Nota: cada Espectador una sólo una vez la máquina.

````C
Process Espectador[id: 0..E-1]{
    Admin!(id)
    Admin?turno()
    // usa máquina
    Admin!listo()
}

Process Admin{
    cola Buffer;
    int idE;
    bool libre=true;
    do Espectador[*]?(idE) -> if libre: Espectador[idE]!turno();
                                        libre=false;
                                else: Push(Buffer, idE);
    do Espectador[*]?listo() -> if empty(Buffer): libre=true;
                                else: Espectador[Pop(Buffer)]!turno()
    od
}
````