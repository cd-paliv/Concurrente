Resolver con ADA el siguiente problema. Un programador contrató a 5 estudiantes para testear los sistemas desarrollados por él. Cada estudiante tiene que trabajar con un sistema diferente y debe encontrar 10 errores (suponga que seguro existen 10 errores en cada sistema) que pueden ser: urgentes, importantes, secundarios. Cada vez que encuentra un error se lo reporta al programador y espera hasta que lo resuelva para continuar. El programador atiende los reportes de acuerdo al orden de llegada pero teniendo en cuenta las siguientes prioridades: primero los urgentes, luego los importantes y por último los secundarios; si en un cierto momento no hay reportes para atender durante 5 minutos trabaja en un nuevo sistema que está desarrollando. Después de resolver los 50 reportes de error (10 por cada estudiante) el programador termina su ejecución. Nota: todas las tareas deben terminar.

``` ada
Process parcial is
    Task Type Estudiante is
        Entry iden(id: IN int);
        Entry setSistema(s: IN int);
        Entry errorResuelto();
    end Estudiante;
    arrE: array(1..5) of Estudiante;

    Trask Programador is
        Entry errorUEncontrado(error: IN int; id: IN int);
        Entry errorIEncontrado(error: IN int; id: IN int);
        Entry errorSEncontrado(error: IN int; id: IN int);
    end Programador;

    Task Body Estudiante is
    contErrores: int;
    sis,error: texto;
    Begin
        accept iden(miID) do
            id=miID;
        end iden;

        contErrores = 0;
        Programador.setSistema(sis);
        while(contErrores<10) loop
            error = BuscarError();
            if(error == 'Urgente'){
                Programador.errorUEncontrado(error, id);
            } else if (error == 'Importante'){
                Programador.errorIEncontrado(error, id);
            } else if (error =='Secundario'){
                Programador.errorSEncontrado(error, id);
            }
            contErrores++;
            accept errorResuelto();
        end loop;
    end Body Estudiante;

    Task Body Programador is
    sistemas; array(1..5) of texto;
    contErrores, idE: int;
    e: texto;
    Begin
        contErrores = 0;
        while(contErrores < 50) loop
            SELECT
                accept errorUEncontrado(e, idE) do
                    e = solucionarError();
                    contErrores++;
                    send Estudiante[id].errorResuelto();
                end errorUEncontrado;
            OR
                when(errorUEncontrado;count=0) => accept errorIEncontrado(e, idE) do
                                                        e = solucionarError();
                                                        contErrores++;
                                                        send Estudiante[id].errorResuelto();
                                                    end errorIEncontrado;
            OR
                when(errorUEncontrado;count=0 & errorIEncontrado:count=0) accept errorSEncontrado(e, idE) do
                                                                                e = solucionarError();
                                                                                contErrores++;
                                                                                send Estudiante[id].errorResuelto();
                                                                            end errorSEncontrado;
            ELSE
                delay(300.0);
                --trabaja en otra cosa
            END SELECT;
        end loop;
    end Programador;

Begin
    for i in 1..5 loop
        Estudiante[i].iden(i);
    end loop;
End Parcial.
```