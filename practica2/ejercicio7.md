Suponga que se tiene un curso con 50 alumnos. Cada alumno debe realizar una tarea y
existen 10 enunciados posibles. Una vez que todos los alumnos eligieron su tarea,
comienzan a realizarla. Cada vez que un alumno termina su tarea, le avisa al profesor y se
queda esperando el puntaje del grupo, el cual está dado por todos aquellos que comparten
el mismo enunciado. Cuando un grupo terminó, el profesor les otorga un puntaje que
representa el orden en que se terminó esa tarea de las 10 posibles.
    Nota: Para elegir la tarea suponga que existe una función
    elegir que le asigna una tarea a un alumno (esta función asignará 10 tareas diferentes entre 50 alumnos, es decir, que 5
    alumnos tendrán la tarea 1, otros 5 la tarea 2 y así sucesivamente para las 10 tareas).

````C
int cantAlumnos = 0;
sem mutexCant = 1;
int calificacionDeGrupo[10] = ([10] 0);
sem barrera_alumnos[50] = ([50] 0); //barrera en la cual los alumnos esperan a que lleguen todos a rendir
sem barrera_notas[10] = ([10] 0); //barrera en la que los alumnos esperan su nota de grupo
cola examenesTerminados;
sem mutexC = 1, hayExamenes = 1;
int cantEnunciados[10] = ([10] 0);

process Alumno[id: 0..49]
{
    enunciado = Elegir();
    P(mutexCant)
    cantAlumnos++;
    if(cantAlumnos == 50){
        V(mutexCant)
        for(i=0; i<50; i++) V(barrera_alumnos[i])
    } else {
        V(mutexCant)
        P(barrera_alumnos[id])
    }
    HacerExamen();
    P(mutexC)
    Push(examenesTerminados, enunciado) //entrego mi enunciado realizado
    V(mutexC)
    V(hayExamenes) //aviso al profe que hay examenes para corregir
    P(barrera_notas[enunciado])
}

process Profesor::
{
    int enunciado, gruposTerminados = 0;
    for(i=0; i<50; i++){
        P(hayExamenes)    
        P(mutexC)
        Pop(examenesTerminados, enunciado)
        V(mutexC)
        cantEnunciados[enunciado]++;
        if(cantEnunciados[enunciado] == 5){
            gruposTerminados++:
            calificacionDeGrupo[enunciado] = gruposTerminados; //pongo nota según orden
            for(i=0; i<10; i++) V(barrera_notas[enunciado])
        }
    }
}
````