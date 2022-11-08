Resolver con ADA el siguiente problema: simular la atencion en una ofician donde atiende un empleado. Hay N personas que deben hacer UN tramite cada uno. Cuando una persona llega le da los datos al empleado y espera a que este resuelva el tramite y le entregue un comprobante. Por otra lado esta el director de la oficina, el cual necesita 5 informes del empleado; cada vez que necesita un informe se lo pide al empleado y si no lo atiende inmediatamente sigue trabajando durante 10 minutos y repite el mismo patron. El empleado atiende los pedidos de acuerdo al orden de llegada pero siempre dando prioridad a los pedidos de las personas.
    Nota: todas las tareas deben terminar.

``` ada
Process Oficina is
    Task Empleado is
        Entry AtencionP(datos: IN texto; comp: OUT texto);
        Entry AtencionD(informe: OUT texto);
    end Empleado;

    Task Type Persona;
    arrP: array[1..N] of Persona;

    Task Body Persona is
    datos:texto := ...;
    comp: texto;
    Begin
        Empleado.AtencionP(datos, comp);
    end Persona;

    Task Body Director is
    informe: texto;
    cant: int:=0;
    Begin
        while(cant<5) loop
            SELECT
                Empleado.AtencionD(informe);
                cant++;
            ELSE DELAY(600.0)
                null;
            END SELECT;
        end loop;
    end Director;

    Task Body Empleado is
    tramite: texto;
    Begin
        for i in 1..N+5 loop
            SELECT
                accept AtencionP(datos, informe) do
                    informe = LlenarInforme(datos);
                end AtencionP;
            OR
                when(AtencionP;count=0) => accept AtencionD(informe) do
                                                informe = ...;
                                            end AtencionD;
            END SELECT;
        end loop;
    end Empleado;

Begin
    null;
end Oficina.
```