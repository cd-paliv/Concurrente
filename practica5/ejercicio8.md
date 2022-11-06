Una empresa de limpieza se encarga de recolectar residuos en una ciudad por medio de 3 camiones. Hay P personas que hacen continuos reclamos hasta que uno de los camiones pase por su casa. Cada persona hace un reclamo, espera a lo sumo 15 minutos a que llegue un camión y si no vuelve a hacer el reclamo y a esperar a lo sumo 15 minutos a que llegue un camión y así sucesivamente hasta que el camión llegue y recolecte los residuos; en ese momento deja de hacer reclamos y se va. Cuando un camión está libre la empresa lo envía a la casa de la persona que más reclamos ha hecho sin ser atendido.
    Nota: maximizar la concurrencia.

``` ada
Process Recolectora is
    Task Empresa is
        Entry Reclamo(idP: IN int);
        Entry CamionLibre(idCamion: IN int);
    end Empresa;

    Task Camion is
        Entry iden(id: IN int);
    end Camion;
    arrC: array(1..3) of Camion;

    Task Persona is
        Entry iden(id: IN int);
        Entry VinoCamion();
        Entry TerminoCamion();
    end Persona;
    arrP: array(1..P) of Persona;

    Task Body Empresa is
    reclamos: array(1..P) of int;
    cantP: int;
    Begin
        cantP=0;
        loop
            SELECT
                accept Reclamo(idP) do
                    if reclamos[idP] > -1 then --{si reclamos[idP]=-1 entonces el reclamo de esa persona ya fue atendido}
                        reclamos[idP]++;
                        cantP++;
                    end if;
                end Reclamo;
            OR
                when (cantP'count > 0) => accept CamionLibre(idMax) do
                                                idMax=max(reclamos);
                                                cantP--;
                                                reclamos[idMax] = -1;
                                          end CamionLibre;
            END SELECT
        end loop;
    end Empresa;

    Task Body Camion is
    idP: int;
    Begin
        loop
            Empresa.CamionLibre(idP);
            Persona[idP].VinoCamion();
            delay(x);
            Persona[idP].TerminoCamion();
        end loop;
    end Camion;

    Task Body Persona is
    id: int;
    espera: bool;
    Begin
        espera=true;
        accept iden(id) do
            id=id;
        end iden;
        while(espera) loop
            Empresa.Reclamo(id)
            SELECT
                accept VinoCamion();
                espera=false;
            OR DELAY(900.0)
                null;
            END SELECT;
            if(not espera) then
                accept TerminoCamion();
            end if;
        end loop;
    end Persona;

Begin
    for i in 1..P loop
        Persona[i].iden(i);
    end loop;
end Empresa.
```