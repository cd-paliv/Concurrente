Se quiere modelar el funcionamiento de un banco, al cual llegan clientes que deben realizar un pago y retirar un comprobante. Existe un único empleado en el banco, el cual atiende de acuerdo con el orden de llegada. Los clientes llegan y si esperan más de 10 minutos se retiran sin realizar el pago

``` ada
Procedure Banco is
    Task empleado is
        Entry Pedido(P: IN texto, Comp: OUT texto)
    end empleado;

    Task Type cliente;
    arrClientes: array(1..N) of cliente;

    Task Body cliente is
        Comp: texto;
    Begin
        SELECT
            Empleado.Pedido(pagar, Comp);
        OR DELAY 600.0
            null;
        END SELECT;
    end cliente;

    Task Body empleado is
    Begin
        loop
            accept Pedido(IN P; OUT Comp) do
                Comp = GenerarComprobante(P);
            end Pedido;
        end loop;
    end empleado;
Begin
    null;
End Banco;
```