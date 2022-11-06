En una playa hay 5 equipos de 4 personas cada uno (en total son 20 personas donde cada una conoce previamente a que equipo pertenece). Cuando las personas van llegando esperan con los de su equipo hasta que el mismo esté completo (hayan llegado los 4 integrantes), a partir de ese momento el equipo comienza a jugar. El juego consiste en que cada integrante del grupo junta 15 monedas de a una en una playa (las monedas pueden ser de 1, 2 o 5 pesos) y se suman los montos de las 60 monedas conseguidas en el grupo. Al finalizar cada persona debe conocer el grupo que más dinero junto.
    Nota: maximizar la concurrencia. Suponga que para simular la búsqueda de una moneda por parte de una persona existe una función Moneda() que retorna el valor de la moneda encontrada.

```ada
Process Playa is
    Task Juego is
        Entry Finalizar(monto: IN int; idE: IN int);
        Entry ObtenerMaxGrupo(idE: OUT int);
    end Juego;

    Task Type Equipo is
        Entry Llegue();
        Entry JuntarMonedas(valor: IN int);
        Entry Comenzar();
    end Equipo;
  
    Task Type Integrante;
    arrI: array(1..20) of Integrante;
    Task Body Integrante is
    miE, cant, idEMax, total: int;
    Begin
        miE=...;
        cant=0;
        Equipo[miE].Llegue();
        Equipo[miE].Comenzar();
        while(cant<15) loop
            --{Busca moneda}
            total += Moneda();
        end loop;
	Equipo[miE].JuntarMonedas(total);
        Juego.ObtenerMaxGrupo(idEMax);
    end Integrante;
  
    arrE: array(1..5) of Equipo;
    Task Body Equipo is
    montoTotal, cant: int;
    Begin
        cant=0;
        for i in 1..4 loop
            accept Llegue();
        end loop;
        for i in 1..4 loop
            accept Comenzar();
        end loop;
        for i in 1..4 loop
            accept JuntarMonedas(valor) do
                montoTotal += valor;
            end JuntarMonedas;
            cant++;
        end loop;
        Juego.Finalizar(montoTotal, id);
    end Equipo;

    Task Body Juego is
    arr: array(1..5) of int;
    montoTotal, idE, idMax: int;
    Begin
        for i in 1..5 loop
            accept Finalizar(montoTotal, idE) do
                arr[idE] = montoTotal;
            end Finalizar;
        end loop;
        CalcularGrupoMax(arr, idMax);
        for i in 1..20 loop
            accept ObtenerMaxGrupo(idMax);
        end loop;
    end Juego;
  
Begin
    null; 
end Playa.
```
