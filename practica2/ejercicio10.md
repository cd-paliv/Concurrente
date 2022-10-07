````
sem lugares = 7;
sem turnoT = 5, turnoM = 5;

process Trigo[id: 1..T]
{
    P(turnoT);
    P(lugares);
    Descarga();
    V(lugares);
    V(turnoT);
}

process Maiz[id: 1..M]
{
    P(turnoT);
    P(lugares);
    Descarga();
    V(lugares);
    V(turnoM);
}
````