Existe una comisión de 50 alumnos que deben realizar tareas de a pares, las cuales son corregidas por un JTP. Cuando los alumnos llegan, forman una fila. Una vez que están todos en fila, el JTP les asigna un número de grupo a cada uno. Para ello, suponga que existe una función AsignarNroGrupo() que retorna un número “aleatorio” del 1 al 25. Cuando un alumno ha recibido su número de grupo, comienza a realizar su tarea. Al terminarla, el alumno le avisa al JTP y espera por su nota. Cuando los dos alumnos del grupo completaron la tarea, el JTP les asigna un puntaje (el primer grupo en terminar tendrá como nota 25, el segundo 24, y así sucesivamente hasta el último que tendrá nota 1).
    Nota: el JTP no guarda el número de grupo que le asigna a cada alumno.

````C
monitor Fila
    cond profesor, cola[50];
    int cantAlumnos = 0;
    int grupo[50];

    procedure Entrar(idP: in int; g: out int)
        cantAlumnos++;
        if(cantAlumnos == 50) signal(profesor)
        wait(cola[idP])
        g = grupo[idP]
    end;

    procedure Liberar()
        if(cantAlumnos == 50) wait(profesor)
        for(i=0; i<50; i++){
            grupo[i] = AsignarNroGrupo(); //???
            signal(cola[i])
        }
    end;
end monitor;

monitor Tarea
    cond esperando_notas[50];
    cola examenes;
    int notas[25], cantExamenes = 0;

    process Realizar(idP: in int; nroTarea: in int; nota: out int)
        // Realiza tarea
        Push(examenes, nroTarea)
        if(cantExamenes == 0) signal(termino)
        cantExamenes++;
        wait(esperando_notas[nroTarea])
        nota = notas[nroTarea]
    end;

    process Corregir()
        int puntaje = 25;
        while(puntaje > 0){
            int enunciado;
            if(cantExamenes == 0) wait(termino)
            Pop(examenes, enunciado);
            cantExamenes--;
            notas[enunciado]++;
            if(notas[enunciado] == 2){
                puntaje--;
                notas[enunciado] = puntaje;
                signal_all(esperando_notas[enunciado])
            }
        }
    end;
end;

process JTP
    Fila.Liberar();
    Tarea.Corregir()
end process;

process Alumno[id: 0..49]
    int grupo, nota;
    Fila.Entrar(id, grupo);
    Tarea.Realizar(id, grupo, nota);
end process;
````