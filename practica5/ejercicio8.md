Una empresa de limpieza se encarga de recolectar residuos en una ciudad por medio de 3 camiones. Hay P personas que hacen continuos reclamos hasta que uno de los camiones pase por su casa. Cada persona hace un reclamo, espera a lo sumo 15 minutos a que llegue un camión y si no vuelve a hacer el reclamo y a esperar a lo sumo 15 minutos a que llegue un camión y así sucesivamente hasta que el camión llegue y recolecte los residuos; en ese momento deja de hacer reclamos y se va. Cuando un camión está libre la empresa lo envía a la casa de la persona que más reclamos ha hecho sin ser atendido.
    Nota: maximizar la concurrencia.

``` ada
Process Empresa is
    Task Camion is
        Entry Reclamo();
    end Camion;

    Task Type Persona;
    arrP: array(1..P) of Persona;
    Task Body Persona is
    espera: bool;
    Begin
        espera=true;
        while(espera) loop
            SELECT
                Camion.Reclamo();
                espera=false;
            ELSE DELAY(900.0)
            END SELECT;
        end loop;
    end Persona;

    Task Body Camion is
    Begin
        accept Reclamo() do
            RecolectarResiduos();
        end Reclamo;
    end Camion;

Begin
    null;
end Empresa.
```