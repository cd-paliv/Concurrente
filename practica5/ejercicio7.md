Hay un sistema de reconocimiento de huellas dactilares de la policía que tiene 8 Servidores para realizar el reconocimiento, cada uno de ellos trabajando con una Base de Datos propia; a su vez hay un Especialista que utiliza indefinidamente. El sistema funciona de la siguiente manera: el Especialista toma una imagen de una huella (TEST) y se la envía a los servidores para que cada uno de ellos le devuelva el código y el valor de similitud de la huella que más se asemeja a TEST en su BD; al final del procesamiento, el especialista debe conocer el código de la huella con mayor valor de similitud entre las devueltas por los 8 servidores. Cuando ha terminado de procesar una huella comienza nuevamente todo el ciclo.
    Nota: suponga que existe una función Buscar(test, código, valor) que utiliza cada Servidor donde recibe como parámetro de entrada la huella test, y devuelve como parámetros de salida el código y el valor de similitud de la huella más parecida a test en la BD correspondiente. Maximizar la concurrencia y no generar demora innecesaria.

``` ada
Process Sistema is
    Task Empleado is
        Entry Resultado(cod: OUT int; val: OUT int);
    end Empleado;

    Task Type Servidor is
        Entry ProcesarHuella(t: IN img);
    end Servidor;
    arrS: array(1..8) of Servidor;

    Task Body Empleado is
    test: img;
    cod, val: int;
    maxVal: int;
    Begin
        maxVal=-1;
        loop
            test = TomarImagen();
            for i in 1..8 loop
                Servidor[i].ProcesarHuella(test);
            end loop;
            for i in 1..8 loop
                accept Resultado(cod, val) --{Recibo los valores en otro for para evitar esperas innecesarias}
                if(val>maxVal) then
                    maxVal=val;
                end if;
            end loop;
            print(maxVal);
        end loop;
    end Empleado;

    Task Body Servidor is
    cod, val: int;
    t: img;
    Begin
        loop
            accept ProcesarHuella(test) do null; end ProcesarHuella;
            Buscar(test, cod, val);
            Empleado.Resultado(cod, val);
        end loop;
    end Servidor;
Begin
    null;
end Sistema.
```