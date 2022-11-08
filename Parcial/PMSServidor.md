Resolver con PMS el siguiente problema: Se debe administrar el acceso para usar en determinado Servidor donde no se permite más de 10 usuarios trabajando al mismo tiempo, por cuestiones de rendimiento. Existen N usuarios que solicitan acceder al Servidor, esperan hasta que se les de acceso para trabajar en él y luego salen del mismo.
    Nota: suponga que existe una función TrabajarEnServidor() que llaman los usuarios para representar que están trabajando en el Servidor

```C
Process Usuario[id: 0..N-1]{
    Servidor!Solicitar(id);
    Servidor?Acceso();
    //usa servidor
    Servidor!Listo();
}

Process Servidor{
    cola Buffer;
    int cant=0, idU;
    bool libre;
    do Usuario?Solicitar(idU) ->  if cant<10: Usuario[idU]!Acceso();
                                        cant++;
                                 else: Push(Buffer, idU);
    do Usuario?Listo() -> if empty(Buffer): cant=0;
                            else: Usuario[Pop(Buffer)]!Acceso();
}
```