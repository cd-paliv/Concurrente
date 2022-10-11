Resolver con MONITORES la siguiente situación. En la guardia de traumatología de un hospital trabajan 5 médicos y una enfermera. A la guardia acuden P Pacientes que al llegar se dirigen a la enfermera para que le indique a que médico se debe dirigir y cuál es su gravedad (entero entre 1 y 10); cuando tiene estos datos se dirige al médico correspondiente y espera hasta que lo termine de atender para retirarse. Cada médico atiende a sus pacientes en orden de acuerdo a la gravedad de cada uno.
    Nota: maximizar la concurrencia.

````C
monitor Fila{
    cond esperaNro[P], hayCola, hayPaciente[5];
    int cant = 1, espera = 0, medico[P];
    colaOrdenada colaIngreso, colaPaciente[5];

    procedure SolicitarAtencion(idP: in int, m: out int){
        Push(colaIngreso, idP);
        signal(hayCola);
        wait(esperaNro[idP]);
        m = medico[idP]
    }

    procedure IndicarMedico(){
        int idAux, gravedad;
        wait(hayCola);
        Pop(colaIngreso, idAux);
        medico[idAux] = ...;
        gravedad = ...;
        InsertarOrdenadoPorNivelDeGravedad(colaPaciente[medico[idAux]], idAux, gravedad)
        signal(esperaNro[idAux]);
        signal(hayPaciente[medico[idAux]]);
    }

    procedure SiguientePaciente(idM: in int, idP: out int){
        wait(hayPaciente[idM]);
        Pop(colaPaciente[idM], idP);
    }
}

monitor Sala[id: 0..4]{
    cond atencion[P];

    prcedure EsperarTurno(idP: in int){
        wait(atencion[idP]);
    }

    procedure FinalizarAtencion(idP: in int){
        signal(atencion[idP])
    }
}

process Paciente[id: 0..P]{
    int medico;
    Fila.SolicitarAtencion(id, medico);
    Sala[medico].EsperarTurno(id);
}

process Medico[id: 0..4]{
    int idPacienteActual;
    while(true){
        Fila.SiguientePaciente(id, idPacienteActual);
        Atender(idPacienteActual);
        Sala[id].FinalizarAtencion(idPacienteActual);
    }
}

process Enfermera{
    while(true){ Fila.IndicarMedico() }
}
````