Se dispone de un sistema compuesto por 1 central y 2 procesos periféricos, que se comunican continuamente. Se requiere modelar su funcionamiento considerando las siguientes condiciones:
- La central siempre comienza su ejecución tomando una señal del proceso 1; luego toma aleatoriamente señales de cualquiera de los dos indefinidamente. Al recibir una señal de proceso 2, recibe señales del mismo proceso durante 3 minutos.
- Los procesos periféricos envían señales continuamente a la central. La señal del proceso 1 será considerada vieja (se deshecha) si en 2 minutos no fue recibida. Si la señal del proceso 2 no puede ser recibida inmediatamente, entonces espera 1 minuto y vuelve a mandarla (no se deshecha).

```
Procedure Sistema is
    Task central is
        Entry SenalUno()
        Entry SenalDos()
        Entry TimerFin()
    end central;

    Task Type proceso1
    Task Body proceso1 is
    Begin
        loop
            SELECT
                central.SenalUno()
            OR DELAY 120
                null;
            END SELECT;
        end loop;
    end proceso1;

    Task Type proceso2
    Task Body proceso2 is
    Begin
        loop
            ok = true;
            while(ok) loop
            SELECT
                central.SenalDos()
            OR
                DELAY 60
            END SELECT;
            end loop;
        end loop;
    end proceso2;

    Task Timer is
        Entry.Inicio()
    end Timer;

    Task Body central is
    Begin
        accept SenalUno();
        loop
            SELECT
                accept SenalUno()
            OR
                accept SenalDos();
                ok = true
                while (ok) loop
                    SELECT
                        accept central.Fin() is
                            ok = false;
                        end;
                    OR
                        when (TimerFin'count=0) -> accept SenalDos()
                    END SELECT;
                end loop;
            END SELECT;
        end loop;
    end central;

Begin
    null;
End Sistema;
```