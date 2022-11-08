Resolver con ADA el siguiente problema: simular la venta de entradas a un evento musical por medio de un portal web. Hay N clientes que intentan comprar una entrada para el evento; los clientes pueden ser regulares o especiales. Cada cliente especial hace un pedido al portal y espera hasta ser atendido; cada cliente regular hace un pedido y si no es atendido antes de los 5 minutos, vuelve a hacer el pedido siguiendo el mismo patron (espera a lo sumo 5 minutos y si no lo vuelve a intentar) hasta ser atendido. Despues de ser atendido, si consiguio comprar la entrada, debe imprimir el comrpobante de la compra.
El portal tiene E entradas para vender y atienden los pedidos de acuerdo al orden de llegada pero dando prioridad a los clientes especiales. Cuando atiende un pedido, si aun quedan entradas disponibles, le vende una al cliente que hizo el pedido y le entreega el comprobante.
    Nota: no debe modelarse la parte de la impresion del comprobante, solo llamar a una funcion Imprimir(comprobante) en el cliente que simulara esa parte; la cantidad E de entradas es mucho menor que la cantidad de clientes (E << C); todas las tareas deben terminar.

``` ada
Process Venta is
    Task Portal is
        Entry ComprarEspecial(comp: OUT texto);
        Entry ComprarRegular(comp: OUT texto);
        
    end Portal;

    Task Type Cliente;
    arrC: array(1..N) of Cliente;

    Task Body Portal is
    vendidas: int;
    Begin
        vendidas=0;
        for i in 1..N loop
            SELECT
                accept ComprarEspecial(comp) do
                    if vendidas < E then
                        vendidas++;
                        comp = GenerarComprobante();
                    else
                        comp='';
                    end if;
                end ComprarEspecial;
            OR
                when(ComprarEspecial;count=0) => accept ComprarRegular(comp) do
                                                    if vendidas < E then
                                                        vendidas++;
                                                        comp = GenerarComprobante();
                                                    else
                                                        comp='';
                                                    end if;
                                                end ComprarRegular;
            END SELECT;

        end loop;
    end Body Portal;

    Task Body Cliente is
    Begin
        if(tipoCliente=='Especial') then
            Portal.ComprarEspecial(comp);
        else
            espera=true;
            while(espera) loop
                SELECT
                    Portal.ComprarRegular(comp);
                    espera=false;
                OR DELAY(300.0)
                    null;
                END SELECT;
            end loop;
        end if;
        if(comp<>'') then Imprimir(comp);
    end Body Cliente;

Begin

end Venta;
```