Suponga que existe un antivirus distribuido que se compone de R procesos robots Examinadores y 1 proceso Analizador. Los procesos Examinadores están buscando continuamente posibles sitios web infectados; cada vez que encuentran uno avisan la dirección y luego continúan buscando. El proceso Analizador se encarga de hacer todas las pruebas necesarias con cada uno de los sitios encontrados por los robots para determinar si están o no infectados.

a) Analice el problema y defina qué procesos, recursos y comunicaciones serán necesarios/convenientes para resolver el problema.
````
Se requerirá R procesos Examinadores, 1 Analizador y un Admin para maximizar la concurrencia. Los recursos serán una cola, variables de texto que permitan enviar/recibir una dirección posiblemente infectada.
````

b) Implemente una solución con PMS
````C
Process Examinador[id: 0..N-1]{
    texto dir;
    while(true){
        dir = BuscarDirección()
        Admin!dirección(dir)
    }
}

Process Admin{
    cola Buffer;
    texto d;
    do Examinador?dirección(d) -> push(Buffer, d)
    do not empty(Buffer);
        Mantenimiento?pedido() -> Mantenimiento!prueba(pop(Buffer))
    od
}

Process Analizador{
    texto dir;
    while(true){
        Admin!pedido()
        Admin?prueba(dir)
        dir = analizar(dir)
    }
}
````