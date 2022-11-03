Se requiere modelar un puente de un único sentido que soporta hasta 5 unidades de peso. El peso de los vehículos depende del tipo: cada auto pesa 1 unidad, cada camioneta pesa 2 unidades y cada camión 3 unidades. Suponga que hay una cantidad innumerable de vehículos (A autos, B camionetas y C camiones). Analice el problema y defina qué tareas, recursos y sincronizaciones serán necesarios/convenientes para resolver el problema.

a. Realice la solución suponiendo que todos los vehículos tienen la misma prioridad.
```
Procedure Puente is
    Task Paso is
        Entry AccesoA()
        Entry AccesoB()
        Entry AccesoC()
        Entry Salir(IN peso:int)
    end Acceso;

    Task type Auto;
    arrAuto: array(1..A) of Auto;
    Task Body Auto is
    double miPeso=...;
    Begin
        Paso.Acceso();
        Paso.Salir(miPeso);
    End Auto;

    Task type Camioneta;
    arrCamioneta: array(1..B) of Camioneta;
    Task Body Camioneta is
    double miPeso=...;
    Begin
        Paso.Acceso();
        Paso.Salir(miPeso);
    End Camioneta;

    Task type Camión;
    arrCamión: array(1..C) of Camión;
    Task Body Camión is
    double miPeso=...;
    Begin
        Paso.Acceso();
        Paso.Salir(miPeso);
    End Camión;

    Task Body Paso is
    int cant = 5;
    Begin
        loop
            SELECT 
                when (cant-1>0 AND Salir'count=0) -> accept AccesoA() do
                    cant--;
                end:
            OR
                when (cant-2>0 AND Salir'count=0) -> accept AccesoB() do
                    cant=cant-2;
                end;
            OR
                when (cant-3>0 AND Salir'count=0)) -> accept AccesoC() do
                    cant= cant-3;
                end;
            OR
                accept Salir(IN peso) do
                    cant=cant+peso;
                end;
            end select;
        end loop;
    End Paso;

Begin
    null;
End Puente;
```

b. Modifique la solución para que tengan mayor prioridad los camiones que el resto de los vehículos.
```
Procedure Puente is
    Task Paso is
        Entry AccesoA()
        Entry AccesoB()
        Entry AccesoC()
        Entry Salir(IN peso:int)
    end Acceso;

    Task type Auto;
    arrAuto: array(1..A) of Auto;
    Task Body Auto is
    double miPeso=...;
    Begin
        Paso.Acceso();
        Paso.Salir(miPeso);
    End Auto;

    Task type Camioneta;
    arrCamioneta: array(1..B) of Camioneta;
    Task Body Camioneta is
    double miPeso=...;
    Begin
        Paso.Acceso();
        Paso.Salir(miPeso);
    End Camioneta;

    Task type Camión;
    arrCamión: array(1..C) of Camión;
    Task Body Camión is
    double miPeso=...;
    Begin
        Paso.Acceso();
        Paso.Salir(miPeso);
    End Camión;

    Task Body Paso is
    int cant = 5;
    Begin
        loop
            SELECT 
                when (cant-1>0 AND Salir'count=0 AND AccesoC'count=0) -> accept AccesoA() do
                    cant--;
                end:
            OR
                when (cant-2>0 AND Salir'count=0 AND AccesoC'count=0) -> accept AccesoB() do
                    cant=cant-2;
                end;
            OR
                when (cant-3>0 AND Salir'count=0)) -> accept AccesoC() do
                    cant= cant-3;
                end;
            OR
                accept Salir(IN peso) do
                    cant=cant+peso;
                end;
            end select;
        end loop;
    End Paso;

Begin
    null;
End Puente;
```