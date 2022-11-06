En una clínica existe un médico de guardia que recibe continuamente peticiones de atención de las E enfermeras que trabajan en su piso y de las P personas que llegan a la clínica ser atendidos.
Cuando una persona necesita que la atiendan espera a lo sumo 5 minutos a que el médico lo haga, si pasado ese tiempo no lo hace, espera 10 minutos y vuelve a requerir la atención del médico. Si no es atendida tres veces, se enoja y se retira de la clínica.
Cuando una enfermera requiere la atención del médico, si este no lo atiende inmediatamente le hace una nota y se la deja en el consultorio para que esta resuelva su pedido en el momento que pueda (el pedido puede ser que el médico le firme algún papel). Cuando la petición ha sido recibida por el médico o la nota ha sido dejada en el escritorio, continúa trabajando y haciendo más peticiones.
El médico atiende los pedidos dándole prioridad a los enfermos que llegan para ser atendidos. Cuando atiende un pedido, recibe la solicitud y la procesa durante un cierto tiempo. Cuando está libre aprovecha a procesar las notas dejadas por las enfermeras.

```ada
Procedure Clinica is
    Task Medico is
        Entry AtencionE(IN pedido:texto);
        Entry AtencionP();
    end Medico;

    Task Mailbox is
        Entry DejarNota(IN nota:texto);
        Entry getNotas(OUT nota:texto);
    end Mailbox;

    Task Type Enfermero;
    arrE: array(1..E) of Enfermero;
    Task Body Enfermero is
    texto p;
    Begin
        loop
            pedido = GenerarPedido();
            if ("el pedido es una nota") then
                Mailbox.DejarNota(pedido);
            else Medico.AtencionE(p);
        end loop;
    end Enfermero;

    Task Type Persona;
    arrP: array(1..P) of Persona;
    Task Body Persona is
    int cant=0;
    bool esperando=true;
    Begin
        while(esperando) loop
            SELECT
                Medico.AtencionP();
                esperando=false;
            OR DELAY(180.0) #3min
                cant++;
                if cant<3 then
                    DELAY(600.0) #10min
                else
                    esperando=false;
                end if;
            END SELECT;
        end loop;
    end Persona;

    Task Body Medico is
    texto pedido;
    Begin
        loop
            SELECT
                accept.AtencionE(pedido) do
                    pedido = Resolver();
                end AtencionE;
            OR
                when (AtencionE'count = 0) => accept.AtencionP();
                                                delay(x);
            ELSE
                Mailbox.getNotas(nota);
                if nota<>null then LeerNota();
            END SELECT;
        end loop;
    end Medico;

    Task Body Mailbox is
    texto nota;
    cola Buffer;
    Beign
        SELECT
            accept.DejarNotas(nota) do
                Push(Buffer, nota)
            end DejarNotas;
        OR
            accept.getNotas(nota) do
                if(Buffer'count=0) then nota=null;
                else nota=Pop(Buffer);
            end getNotas;
        END SELECT;
    end Mailbox;


Begin
    null;
End Clinica.
```