# Laboratorio 3: Planificación


## Primera Parte: Estudiando el planificador de xv6

### 1.a)
El planificador de xv6 es de tipo Round Robin (RR). Cada CPU luego de iniciarse llama a la funcion scheduler. El planificador nunca retorna nada, realiza el siguiente loop infinito:
- Selecciona un proceso para correr.
- Utiliza una funcion swtch para comenzar a correr el proceso seleccionado. 
- Eventualmente ese proceso transfiere el control a traves de una funcion swtch back, nuevamente hacia la funcion scheduler, comenzando nuevamente el ciclo.

### 2.a)
El valor de un quantum en el scheduler de xv6 es de 10Mhz.

### 2.b)
Una forma es reduciendo el valor del quantum, asi se acorta el tiempo de ejecucion de un proceso.
Otra manera es dandole mas cantidad de ticks especificamente a un determinado proceso.


## Segunda Parte: Viendo cómo el planificador afecta a los procesos

Graficos en archivo EXCEL y un archivo de texto de con los datos obtenidos en las mediciones.
En los graficos representamos los resultados mediante un coeficiente, el cual es la division entre el valor de KFLOPT del cpu, y el valor IOP250T.
Realizamos los testeos en el mismo cpu para que no haya variaciones en los resultados. 

#### Con Quantum de 10 millones y 250 ticks:
En el grafico de RR se puede ver una estabilidad razonable, pero se nota una reduccion abrupta en la ejecucion de 2 cpu y 2 io.

#### Con Quantum de 1 millon y 2500 ticks:
En este caso, cuantos mas io hayan el coeficiente es mayor, tambien puede verse una estabilidad creciente en los resultados.


![graficos](/so20lab3g26/Grafico.jpeg)
 

## Tercera Parte: Rastreando la prioridad de los procesos

### 3.a)
Para asignar la prioridad más alta a un proceso (0 la mayor y 2 la menor), asignamos 0 a la prioridad del proceso en allocproc, para que cada vez que inicie un proceso lo haga con la máxima prioridad.
En la funcion yield (llamada desde trap.c), reducimos la prioridad de un proceso cuando utilizó todo un quantum.
Luego desde scheduler comparamos si al ejecutar un proceso, éste terminó o no, con la misma prioridad y, en tal caso, la aumentamos.

### 3.b)
Hicimos un printeo paralelo al de ptable, donde imprimimos las tres colas de prioridades con los respectivos procesos. Cada proceso impreso incluye pid, estado, nombre y prioridad del mismo.


## Cuarta Parte: Implementando MLFQ

### 4.a)
La implementacion del scheduler, para cumplir todas las reglas dichas, es de la siguiente manera:
  En la estructura ptable: agregamos 3 colas ("queue" una por cada prioridad), con capacidad para 64 procesos, esto nos da libertad para acceder a cualquier proceso dentro de la misma.
    Tambien agregamos un entero "count_proc" en el que llevamos la cuenta de la cantidad de procesos que hay en cada cola.

  En scheduler: hace un RR en la cola cero, si ejecutó algun proceso en esta cola, seguirá recorriendola hasta no encontrar mas procesos RUNNABLE para ejecutar.
  Por otro lado, si no ejecutó ningun proceso en la cola cero, mirará en las otras colas.
    Cada vez que se ejecute algun proceso con prioridad mayor a cero, revisamos la cola cero por si hay nuevos procesos con mayor prioridad para ejecutar, y si los hay, volvemos a iniciar el for ejecutando nuevamente desde la cola cero, respetando asi la regla 1.

### 4.b)
Graficos en archivo EXCEL y un archivo de texto de con los datos obtenidos en las mediciones.
En los graficos representamos los resultados mediante un coeficiente, el cual es la division entre el valor de KFLOPT del cpu, y el valor IOP250T.
Realizamos los testeos en el mismo cpu para que no haya variaciones en los resultados.

#### Con Quantum de 10 millones y 250 ticks:
En el grafico de MLFQ se puede notar practicamente un grafico inverso respecto al de RR. El coeficiente en el test de 2 cpu y 2 io se dispara notablemente.

#### Con Quantum de 1 millon y 2500 ticks:
En este caso, es muy similar al RR de quantum 1 millon, hay una estabilidad creciente en los resultados.

![graficos](/so20lab3g26/graficos.jpeg)

### 4.c)
Si, dado que no tenemos implementada una interrupcion por timer cada equis tiempo, para reiniciar a cero la prioridad de todos los procesos de las colas. Por esta falta de implementacion (es el primer extra), se produce starvation ya que los procesos de las colas mas bajas se ejecutarán cuando ya no haya procesos con prioridad cero.


 
 
 
 
























