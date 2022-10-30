En un estadio de fútbol hay una máquina expendedora de gaseosas que debe ser usada por E Espectadores de acuerdo al orden de llegada. Cuando el espectador accede a la máquina en su turno usa la máquina y luego se retira para dejar al siguiente.
    Nota: cada Espectador una sólo una vez la máquina.

````C
Process Máquina{
    while(true){
        Máquina!libre() //Pongo primero libre porque sino cuando llegue el primer espectador no la va a poder usar
        Máquina?ocupada()
    }
}

Process Espectador[id: 0..E-1]{
    Admin!(id)
    Admin?turno()
    // usa máquina
    Máquina!ocupada()
}

Process Admin{
    int idE;
    do Espectador[*]?(idE) -> Push(Buffer, idE);
    do not empty(Buffer);
        Máquina?libre() -> Espectador[Pop(buffer)]!turno();
    od
}
````