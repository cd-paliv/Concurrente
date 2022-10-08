Resolver el funcionamiento en una fábrica de ventanas con 7 empleados (4 carpinteros, 1 vidriero y 2 armadores) que trabajan de la siguiente manera:
    • Los carpinteros continuamente hacen marcos (cada marco es armando por un único carpintero) y los deja en un depósito con capacidad de almacenar 30 marcos.
    • El vidriero continuamente hace vidrios y los deja en otro depósito con capacidad para 50 vidrios.
    • Los armadores continuamente toman un marco y un vidrio (en ese orden) de los depósitos correspondientes y arman la ventana (cada ventana es armada por un único armador)

````C
sem mutexM = 1, mutexV = 1; //semáforos para contadores
sem hayMarco = 0, hayVidrio = 0; //semáforo para saber si hay marcos o vidrios
int cantM = 0, cantV = 0;
sem esperaDepositoM = 30, esperaDepositoV = 50;

process Carpintero[id: 0..3]
{
    while(true)    
    {
        P(mutexM)
        if(cantM < 30)
        {
            HacerMarco();
            cantM++;
        }
        V(mutexM)
        V(hayMarco)
        P(esperaDepositoM) //si el depósito está lleno, espero
    }
    
}

process Vidriero::
{
    while(true)
    {
        P(mutexV)
        if(cantV < 30)
        {
            HacerVidrio();
            cantV++;
        }
        V(mutexV)
        V(hayVidrio)
        P(esperaDepositoV) //si el depósito está lleno, espero
    }
}

process Armador[0..1]
{
    while(true)
    {
        P(hayMarco)
        P(mutexM)
        cantM--;
        V(mutexM)
        V(esperaDepositoM)
        P(hayVidrio)
        P(mutexV)
        cantV--;
        V(mutexV)
        V(esperaDepositoV)
        ArmarVentana();
    }
}
````