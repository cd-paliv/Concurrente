Simular la atención en una Terminal de Micros que posee 3 puestos para hisopar a 150 pasajeros. En cada puesto hay una Enfermera que atiende a los pasajeros de acuerdo con el orden de llegada al mismo. Cuando llega un pasajero se dirige al puesto que tenga menos gente esperando. Espera a que la enfermera correspondiente lo llame para hisoparlo, y luego se retira.
    Nota: sólo deben usar procesos Pasajero y Enfermera. Además, suponer que existe una función Hisopar() que simula la atención del pasajero por parte de la enfermera correspondiente

````C
cola listaDeEspera[3]; //una cola para cada enfermera
sem esperaLlamadoEnfermera[3] = ([3] 0);
sem mutex[3] = ([3] 1);
sem hayP[3] = ([3] 0);
int cantPersonasPuesto[3] = ([3] 0);
sem mutexPuesto[3] = ([3] 1);


process Pasajero [id: 0..149]
{
    int puesto = ElegirMinimoPuesto(); //implementar minimo puesto
    P(mutex[puesto])
    Push(listaDeEspera[puesto], id)
    V(mutex[puesto])
    V(hayP[puesto]) //aviso que hay pasajeros esperando en el puesto
    P(esperaLlamadoEnfermera[id])
    P(esperaHisopado[id])
}

Process Enfermera [id: 0..2]
{
    int idPasajero;
    while(true)
    {
        P(hayP[id])
        P(mutex[id])
        Pop(listaDeEspera[id], idPasajero)
        V(mutex[id])
        V(esperaLlamadoEnfermera[idPasajero])
        Hisopar(idPasajero)
        V(esperaHisopado[idPasajero])
    }
}

//NO ES NECESARIO IMPLEMENTARLO
procedure int ElegirMinimoPuesto(puesto)
{
    int min =9999;
    for(i=0; i<3< i++){
        P(mutexPuesto[i])
        if(puesto[i] < min){
            min = puesto[i]
        }
        V(mutexPuesto[i])
    }
    P(mutexPuesto[min])
    puesto[min]
    V(mutexPuesto[min])
}
````