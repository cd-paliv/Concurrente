a) Si, funciona correctamente ya que ningún auto podrá pasar al mismo tiempo que otro.

b) Podría mejorarse de forma que en lugar de utilizar un while se utilice un if, ya que cant va a ser mayor que 0 *SÓLO* cuando un auto esté pasando por el puente, y ese mismo despertará al siguiente proceso, no es necesario volver a chequear la condición muchas veces.
Tampoco es necesario enviarle parámetros al proceso del monitor.

```C
    Monitor Puente
        cond cola;
        int cant= 0;
        Procedure entrarPuente()
            if(cant > 0) wait(cola);
            cant = cant + 1;
        end;
        Procedure salirPuente()
            cant = cant – 1;
            signal(cola);
        end;
    End Monitor;

    Process Auto [a:1..M]
        Puente. entrarPuente();
        “el auto cruza el puente”
        Puente. salirPuente();
    End Process;
```

c) Si, aunque se podría mejorar. La técnica para respetar el orden es Passing the Condition.
```C
    Monitor Puente
        cond cola;
        int cant= 1, espera= 0;
        Procedure entrarPuente()
            if(cant == 0){espera++; wait(cola);} //si hay alguien pasando
            else cant--;
        end;
        Procedure salirPuente()
            if(espera == 0) cant++;
            else{espera--; signal(cola);} //si hay alguien esperando, lo despierto
        end;
    End Monitor;

    Process Auto [a:1..M]
        Puente. entrarPuente();
        “el auto cruza el puente”
        Puente. salirPuente();
    End Process;
```