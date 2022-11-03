Se requiere modelar un puente de un único sentido que soporta hasta 5 unidades de peso. El peso de los vehículos depende del tipo: cada auto pesa 1 unidad, cada camioneta pesa 2 unidades y cada camión 3 unidades. Suponga que hay una cantidad innumerable de vehículos (A autos, B camionetas y C camiones). Analice el problema y defina qué tareas, recursos y sincronizaciones serán necesarios/convenientes para resolver el problema.

a. Realice la solución suponiendo que todos los vehículos tienen la misma prioridad.
```
Procedure Puente is
    Task Paso is
        Entry Acceso()
    end Acceso;

    Task type Auto;
    arrAuto: array(1..A) of Auto;
    Task Body Auto is
    Begin
        Paso.Acceso();
    End Auto;

    Task type Camioneta;
    arrCamioneta: array(1..B) of Camioneta;
    Task Body Camioneta is
    Begin
        Paso.Acceso();
    End Camioneta;

    Task type Camión;
    arrCamión: array(1..C) of Camión;
    Task Body Camión is
    Begin
        Paso.Acceso();
    End Camión;

    Task Body Paso is
    Begin
        loop
            accept Acceso();
        end loop;
    End Paso;

Begin
    null;
End Puente;
```

b. Modifique la solución para que tengan mayor prioridad los camiones que el resto de los vehículos.
```

```