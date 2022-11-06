En un sistema para acreditar carreras universitarias, hay UN Servidor que atiende pedidos de U Usuarios de a uno a la vez y de acuerdo con el orden en que se hacen los pedidos. Cada usuario trabaja en el documento a presentar, y luego lo envía al servidor; espera la respuesta de este que le indica si está todo bien o hay algún error. Mientras haya algún error, vuelve a trabajar con el documento y a enviarlo al servidor. Cuando el servidor le responde que está todo bien, el usuario se retira. Cuando un usuario envía un pedido espera a lo sumo 2 minutos a que sea recibido por el servidor, pasado ese tiempo espera un minuto y vuelve a intentarlo (usando el mismo documento).

``` ada
Process Sistema is
    Task Servidor is
        Entry PresentarPedido(IN pedido:texto; OUT res:bool);
    end Servidor;

    Task Type Usuario;
    arrU: array(1..U) of Usuario;
    Task Body Usuario is
    texto p;
    bool espera=true, res;
    Begin
        p = TrabajarEnDocumento();
        while(espera) loop
            SELECT
                Servidor.PresentarPedido(p, res);
                if(res) then
                    espera=false;
                else
                    CorregirDocumento(p);
                end if;
            OR DELAY(120.0)
                delay(60.0);
            END SELECT;
        end loop;
    end Usuario;

    Task Body Servidor is
    texto pedido;
    bool res;
    Begin
        loop
            accept PresentarPedido(pedido, res) do
                VerificarDocumento(pedido, res);
            end PresentarPedido;
        end loop;
    end Servidor;

Begin
    null;
end Sistema.
```