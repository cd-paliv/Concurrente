// ESTE EJERCICIO ESTÁ MAL

a)
````C
sem acceso = 1;
process Persona[id: 1..N]
{ while(true)
    {   P(acceso)
        Imprimir(documento)
        V(acceso)
    }
}
````

b)
process PPseudocodigo[id: 1..N]
{
    si el recurso no se esta usando
        miro el recurso como ocupado
        uso el recurso
    sino
        me pongo en la cola
        pongo un p en el pos del sem
    
    si hay alguien en la cola
        le cedo el lugar
    sino
        me voy y marco el recurso como libre.
}

````C
c=cola;
bool libre=true:
sem espera([N] 1), acceso=1;


process Persona[id: 1..N]
{
    P(acceso)
    if(libre)
    [
        libre=false;
        V(acceso)
    ] else
    {
        Push(c,id);
        V(acceso) //libero bool
        P(espera[id]) //espero recurso
    }

    Imprimir(documento)

    <if(! cola.empty())
    [
        Pop(c, id);
        V(espera[id]);
    ] else
    [
        libre=true;
    ]>
}
````

c)
````C
c=cola;
bool libre=true:
sem espera([N] 1), acceso=1;

process Persona[id: 1..N]
{
    P(acceso)
    if(libre)
    [
        libre=false;
        V(acceso)
    ] else
    {
        PushConPrioridad(c, id);
        V(acceso) //libero bool
        P(espera[id]) //espero recurso
    }

    Imprimir(documento)

    <if(! cola.empty())
    [
        Pop(c, id);
        V(espera[id]);
    ] else
    [
        libre=true;
    ]>
}
````

d)
````C
sem llega=1, espera([N] 1), ok=0;

process Persona[id: 1..N]
{
    <PushConPrioridad(c, id)>;
    V(llega)
    P(espera[id]);
    Imprimir(documento);
    V(ok);
}

process Coordinador
{
    while(true)
    {
        P(llega)
        <Pop(c, id)>;
        V(espera[id]);
        DarTurno() //le da el turno al proceso
        P(ok);
    }
}
````

e)
````C
sem llega=1, ok=4, espera([N] 1), mutex=1, mutexCola=1;
c=cola; int impresora[N];

process Persona[id: 1..N]
{
    P(mutexCola);
    PushConPrioridad(c, id);
    P(mutexCola);
    V(llega)
    P(espera[id]);
    imp = impresora[id];
    Imprimir(documento, imp);
    P(mutexCola);
    Push(c, impresora[id]);
    V(mutexCola);
    V(ok);
}

process Coordinador
{
    while(true)
    {
        P(llega) 
        P(mutexCola)
        Pop(c, id);
        V(mutexCola)
        P(mutex)
        impresora[id] = Pop(c, imp)
        V(mutex)
        V(espera[id]);
        DarTurno() //le da el turno al proceso
        P(ok); // se queda esperando las impresoras en caso de que no haya ninguna disponible
    }
}
````