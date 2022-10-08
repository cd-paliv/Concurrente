En un corralón de materialessedebenatender a N clientes de acuerdocon el orden de llegada. Cuando un cliente es llamado para ser atendido,entregauna lista con los productos que comprará, y espera a que alguno de los empleados le entregue el comprobante de la compra realizada.

a)Resuelva considerando que el corralón tiene un único empleado.
````C
monitor Corralon
    cond cola[N], hayFila;
    colaOrdenada fila;
    int idAux, compras[N], comprobantes[N];

    procedure EsperarYEntregarLista(idP: in int; productos: in list; comp: out int)
        Push(fila, (idP, productos))
        signal(hayFila)
        wait(cola[idP])
        comp = comprobantes[idP]
    end;

    procedure Atender(idAux: out int; prod: out list)
        wait(hayFila)
        Pop(fila, (idAux, prod))
    end;

    procedure EntregarComprobante(idAux, comp: in int)
        comprobantes[idAux] = comp;
        signal(cola[idAux]);
    end;
end monitor;

process Empleado
    int idAux, comp;
    list productos;
    while(true){
        Corralon.Atender(idAux, productos)
        // Genera el comprobante con la lista de productos y lo guarda en comp
        Corralon.EntregarComprobante(idAux, comp)
    }
end process;

process Cliente[id: 0..N-1]
    list lista;
    int comprobante;
    Corralon.Pagar(id, lista, comprobante);

end process;
````

b)Resuelva considerando que el corralón tiene E empleados (E > 1).
````Creo que me quedaría igual, sólo que Empleado tendría id````