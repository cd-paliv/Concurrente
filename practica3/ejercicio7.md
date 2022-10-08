En un entrenamiento de fútbol hay 20 jugadores que forman 4 equipos (cada jugador conoce el equipo al cual pertenece llamando a la función DarEquipo()). Cuando un equipo está listo (han llegado los 5 jugadores que lo componen), debe enfrentarse a otro equipo que también esté listo (los dos primeros equipos en juntarse juegan en la cancha 1, y los otros dos equipos juegan en la cancha 2). Una vez que el equipo conoce la cancha en la que juega, sus jugadores se dirigen a ella. Cuando los 10 jugadores del partido llegaron a la cancha comienza el partido, juegan durante 50 minutos, y al terminar todos los jugadores del partido se retiran (no es necesario que se esperen para salir).

````C
monitor Equipo
    cond espero_cinco[4];
    int cantPersonas[4] = ([4] 0)
    int cancha[4], numCancha = 0;

    procedure EsperarCompaneros(equipo: in int)
        cantPersonas[equipo]++;
        if(cantPersonas[equipo] < 5) wait(espero_cinco[equipo])
        else{
            Push(fila, equipo)
            hayEquipo++;
            signal_all(espero_cinco[equipo])
        }
    end;

    procedure EsperoOtroEquipo(miE: in int; miCancha: out int)
        if(hayEquipo < 2) wait(otroEquipo)
        else{
            numCancha++;
            for(i=0; i<2; i++){
                Pop(fila, equipo)
                hayEquipo--;
                cancha[equipo] = numCancha;
            }
            signal(otroEquipo)
        }
        miCancha = cancha[miE]
    end;
end monitor;

monitor Cancha[id: 1..4]
    cond ePartido, ePersonas;
    cant = 0;

    procedure Jugar()
        cant++;
        if(cant = 10) signal(ePartido)
        wait(ePersonas)
    end;

    procedure IniciarPartido()
        if(cant < 10) wait(ePartido)
    end;

    procedure TerminarPartido()
        signal_all(ePersonas)
    end;
end monitor;

process Jugador[id: 1..20]
    int nroC, e = DarEquipo();
    Equipo.EsperarCompaneros(e);
    Equipo.EsperoOtroEquipo(e, nroC);
    Cancha[nroC].Jugar();
end process;

process Partido[id: 1..4]
    Cancha[id].IniciarPartido();
    delay(50 minutos);
    Cancha[id].TerminarPartido();
end process;
````