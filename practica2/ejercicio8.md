Una fábrica de piezas metálicas debe producir T piezas por día. Para eso, cuenta con E empleados que se ocupan de producir las piezas de a una por vez (se asume T>E). La fábrica empieza a producir una vez que todos los empleados llegaron. Mientras haya piezas por fabricar, los empleados tomarán una y la realizarán. Cada empleado puede tardar distinto tiempo en fabricar una pieza. Al finalizar el día, se le da un premio al empleado que más piezas fabricó.

````C
int cantPiezas = 0, cantEmpleados = 0;
sem mutexP = 1, mutexE = 1;
sem barrera_empleados[E] = ([E] 0);
sem piezasFinalizadas = 0, esperoGanador = 0;
int cantPiezasEmpleados[E] = ([E) 0)

process Empleado[id: 1..E]
{
    P(mutexE)
    if(cantEmpleados == E)
    {
        V(mutexE)    
        for(i=1; i<E; i++) V(barrera_empleados[i]) //libero a todos los empleados
    } else {
        V(mutexE)
        P(barrera_empleados[id]) //espero a que lleguen todos los empleados
    }
    P(mutexP)
    while(cantPiezas < T)
    {
        pieza = ...;
        cantPiezas++;
        V(mutexP)
        FabricarP();
        cantPiezasEmpleados[id]++;
        P(mutexP)
    }
    V(mutexP)
    V(piezasFinalizadas)
    P(esperoGanador)
}

process Premiador::
{
    int nroE, max = -1;
    for(i=0; i<E; i++) P(piezasFinalizadas)
    for(i=0; i<E; i++)
    {
        if(max < cantPiezasEmpleados[i])
        {
            max = cantPiezasEmpleados[i];
            nroE = i;
        }
    }
    NotificoGanador(nroE, max);
    for(i=0; i<E; i++) V(esperoGanador)
}
````