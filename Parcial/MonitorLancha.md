Resolver con MONITORES el siguiente problema. En un Crucero por el Mediterráneo hay 200 personas que deben subir al barco por medio de 10 lanchas con 20 lugares  cada una. Cada persona sube a la lancha que le corresponde. Cuando en una lancha han subido sus 20 personas durante 5 minutos navega hasta el barco. Recién cuando han llegado las 10 lanchas al barco se les permite a las 200 personas subir al barco. 
    Nota: suponga que cada persona llama a la función int NúmeroDeLanca () que le devuelve un valor entre 0 y 9 indicando la lancha a la que debe subir. Maximizar la  concurrencia.

````C
monitor Lancha[id: 0..9]{
    cond cola;
    int cantPersonas = 0;

    procedure Subir(){
        cantPersonas++:
        if(cantPersonas == 20) signal_all(cola)
        else wait(cola);
    }
}

monitor Barco{
    cond cola;
    int cantL = 0, cantPersonas[10] = ([10] 0);
    
    procedure Embarcar(idLancha: in int){
        cantPersonas[idLancha]++;
        if(cantPersonas[idLancha] == 20) cantL++;
        if(cantL == 10) signal_all(listo);
        else wait(listo);
    }
}

process Persona[id: 0..199]{
    int nro = NumeroDeLancha();
    Lancha[nro].Subir();
    delay(5 minutos);
    Barco.Embarcar(nro);
}
````