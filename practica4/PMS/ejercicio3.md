En un examen final hay N alumnos y P profesores. Cada alumno resuelve su examen, lo entrega y espera a que alguno de los profesores lo corrija y le indique la nota. Los profesores corrigen los exámenes respetando el orden en que los alumnos van entregando.
    Nota: maximizar la concurrencia y no generar demora innecesaria.

a) Considerando que P=1.
````C
Process Alumno[id: 0..N-1]{
    texto e = ...;
    int nota;
    e = ResolverExamen()
    AdminExamenes!(e, id)
    Profesor?(nota)
}

Process AdminExamenes{
    cola Buffer;
    texto e; int idA;
    do Alumno[*]?(e) -> Push(Buffer, (e, idA))
    do not empty(Buffer);
        Profesor?() -> Profesor!(Pop(Buffer))
    od
}

Process Profesor{
    texto ex; int idA, nota;
    while(true){
        AdminExamenes!()
        AdminExamenes?(ex, idA)
        nota = Corregir(ex)
        Alumno[idA]!(nota)
    }
}
````

b) Considerando que P>1.
````C
Process Alumno[id: 0..N-1]{
    texto e = ...;
    int nota;
    e = ResolverExamen()
    AdminExamenes!(e, id)
    Profesor[*]?(nota)
}

Process AdminExamenes{
    cola Buffer;
    texto e; int idA;
    do Alumno[*]?(e) -> Push(Buffer, (e, idA))
    do not empty(Buffer);
        Profesor[*]?(idP) -> Profesor[idP]!(Pop(Buffer))
    od
}

Process Profesor[id: 0..P-1]{
    texto ex; int idA, nota;
    while(true){
        AdminExamenes!(id)
        AdminExamenes?(ex, idA)
        nota = Corregir(ex)
        Alumno[idA]!(nota)
    }
}
````

c) Ídem b) pero considerando que los alumnos no comienzan a realizar su examen hasta que todos hayan llegado al aula.
````C
Process Barrera{
    cola Buffer;
    cantA = 0;
    do Alumno?listo() -> cantA++;
    do cantA == N;
        for i=0 to N-1: Alumno[i]!comenzar()
    od
}

Process Alumno[id: 0..N-1]{
    texto e = ...;
    int nota;
    Barrera!listo()     //Avisa que llegó
    Barrera?comenzar()  //Espera al resto de los alumnos
    e = ResolverExamen()
    AdminExamenes!(e, id)
    Profesor[*]?(nota)
}

Process AdminExamenes{
    cola Buffer;
    texto e; int idA;
    do Alumno[*]?(e) -> Push(Buffer, (e, idA))
    do not empty(Buffer);
        Profesor[*]?(idP) -> Profesor[idP]!(Pop(Buffer))
    od
}

Process Profesor[id: 0..P-1]{
    texto ex; int idA, nota;
    while(true){
        AdminExamenes!(id)
        AdminExamenes?(ex, idA)
        nota = Corregir(ex)
        Alumno[idA]!(nota)
    }
}
````