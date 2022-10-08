En un vacunatorio hay un empleado de salud para vacunar a 50 personas. El empleado de salud atiende a las personas de acuerdo con el orden de llegada y de a 5 personas a la vez. Es decir, que cuando está libre debe esperar a que haya al menos 5 personas esperando, luego vacuna a las 5 primeras personas, y al terminar las deja ir para esperar por otras 5. Cuando ha atendido a las 50 personas el empleado de salud se retira.
    Nota: todos los procesos deben terminar su ejecución; asegurarse de no realizar Busy Waiting; suponga que el empleado tienen una función VacunarPersona() que simula que el empleado está vacunando a UNA persona.

````C
cola esperaEnfermero;
sem mutexC = 1;
sem esperaLlamado[50] = ([50] 0)
sem esperaE = 0;

process Persona [id: 0..49]
{
    P(mutexC)
    Push(esperaEnfermero, id)
    V(mutexC)
    V(esperaE) //Aviso que llegué
    P(esperaLlamado[id]) //Espero a que seamos cinco
    P(esperaVacuna[id])
}

process Empleado
{
    int idPersona, cant=0;
    while(cant<50)
    {
        for(i=0; i<5; i++) P(esperaE) //espero a los cinco
        for(i=0; i<5; i++)
        {
            P(mutexC)
            Pop(esperaEnfermero, idPersona) //puedo popear de a 5 para no trabar la cola
            V(mutexC)
            V(esperaLlamado[idPersona])
            VacunarPersona(idPersona);
            cant++;
            V(esperaVacuna[idPersona])
        }
    }
}
````