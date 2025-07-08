#### Debes sincronizar los hilos

:::note[🎯 Enunciado]
En la actividad anterior pudiste ver cómo lograr que el hilo principal no se congele, pero esto tuvo un costo que en 
principio no es tan obvio. Cuando dos hilos comparten datos, es necesario sincronizarlos para evitar que se
produzcan condiciones de carrera. En este caso, el hilo principal y el hilo de heavyComputation() están accediendo a la
variable circleSize. Si el hilo de heavyComputation() cambia el valor de circleSize mientras el hilo principal lo está
usando, puede provocar un comportamiento inesperado.
:::

**¿Qué es una condición de carrera?**
Una condición de carrera es un error que ocurre cuando dos o más hilos acceden a un recurso compartido y al menos uno de ellos lo modifica. Esto puede provocar resultados inesperados, ya que el resultado final depende del orden en que se ejecutan los hilos.
Por ejemplo, si un hilo está leyendo el valor de circleSize mientras otro hilo lo está modificando, el hilo que lee puede obtener un valor incorrecto. Esto puede provocar que el círculo se dibuje en una posición incorrecta o que se produzcan otros errores en el programa.

Mira, este tipo de errores son difíciles de detectar y depurar, ya que pueden ocurrir en cualquier momento y no siempre se producen. Por eso es importante sincronizar los hilos para evitar condiciones de carrera.
Para sincronizar los hilos, puedes usar un mutex (mutual exclusion). Un mutex es un objeto que se utiliza para proteger el acceso a un recurso compartido. Cuando un hilo quiere acceder al recurso, debe bloquear el mutex. Si otro hilo intenta acceder al mismo recurso mientras el mutex está bloqueado, debe esperar hasta que el mutex se desbloquee.
Esto garantiza que solo un hilo pueda acceder al recurso a la vez, evitando así condiciones de carrera.

:::note[🧐🧪✍️]
Analiza de nuevo el código de la actividad anterior. ¿En qué partes del código se está protegiendo el acceso a la variable circleSize?

Según lo que te he venido comentando, los hilos te permiten ejecutar tareas en paralelo; sin embargo, piensa qué ocurre con 
el paralelismo cuando se sincroniza el acceso a un recurso compartido. ¿Qué ocurre con el rendimiento del programa? ¿Es posible que el rendimiento se vea afectado por el uso de mutex? ¿Por qué?
:::

Ahora quiero mostrarte un ejemplo más dramático de una condición de carrera. Para ello te pediré que modifiques el código 
de ofApp.h para que contenga lo siguiente:
```cpp
#pragma once
#include "ofMain.h"
#include "ofThread.h"

// Parámetros fijos ― modifica si quieres
const int  NUM_THREADS = 4;        
const long NUM_STEPS = 1000000;  

//--------------------------------------------------------------
class CounterThread : public ofThread {
public:
    CounterThread(long* sharedCounter,
        ofThread* sharedLocker,
        bool* lockFlag)
        : counter(sharedCounter)
        , locker(sharedLocker)
        , useLock(lockFlag)
    {
    }

    void threadedFunction() override {
        for (long i = 0; i < NUM_STEPS; ++i) {
            if (*useLock) {                 
                locker->lock();          
                ++(*counter);
                locker->unlock();
            }
            else {                        
                ++(*counter);             
            }
        }
    }
private:
    long* counter;   
    ofThread* locker;    
    bool* useLock;   
};

//--------------------------------------------------------------
class ofApp : public ofBaseApp {
public:
    void setup();
    void draw();
    void exit();
    void keyPressed(int);

private:
    // Lógica auxiliar
    void startTest();
    void stopWorkers();

    long counter = 0;           
    bool useLock = true;        

    CounterThread* workers[NUM_THREADS]; 
    ofThread       locker;
};
```

Y modifiques el código de ofApp.cpp para que contenga lo siguiente:

```cpp
#include "ofApp.h"

//--------------------------------------------------------------
void ofApp::setup() {
    ofSetFrameRate(60);
    startTest();
}

//--------------------------------------------------------------
void ofApp::startTest() {
    counter = 0;

    ofSetWindowTitle(
        std::string(useLock ? "SAFE" : "UNSAFE") +
        "  |  's' toggle lock  |  'r' restart");

    for (int i = 0; i < NUM_THREADS; ++i) {
        workers[i] = new CounterThread(&counter, &locker, &useLock);
        workers[i]->startThread();
    }
}

//--------------------------------------------------------------
void ofApp::stopWorkers() {
    for (int i = 0; i < NUM_THREADS; ++i) {
        if (workers[i]) {
            workers[i]->waitForThread(true);
            delete workers[i];
            workers[i] = nullptr;
        }
    }
}

//--------------------------------------------------------------
void ofApp::draw() {
    ofBackground(30);
    ofSetColor(255);

    long expected = NUM_THREADS * NUM_STEPS;

    ofDrawBitmapStringHighlight(
        std::string(useLock ? "Modo SEGURO - con lock()" :
            "Modo INSEGURO - sin lock()"),
        20, 40);
    ofDrawBitmapStringHighlight(
        "Contador:  " + ofToString(counter), 20, 70);
    ofDrawBitmapStringHighlight(
        "Esperado:  " + ofToString(expected), 20, 100);
    ofDrawBitmapStringHighlight(
        "Pulsa  's'  para alternar  |  'r'  para reiniciar",
        20, 130);
}

//--------------------------------------------------------------
void ofApp::keyPressed(int key) {
    if (key == 'r' || key == 'R') {     // reiniciar prueba
        stopWorkers();
        startTest();
    }
    if (key == 's' || key == 'S') {     // alternar lock / unlock
        useLock = !useLock;
        stopWorkers();
        startTest();
    }
}

//--------------------------------------------------------------
void ofApp::exit() {
    stopWorkers();                    // limpieza ordenada
}
```

:::note[🧐🧪✍️]
Ejecuta el código y observa el resultado. ¿Qué ocurre si cambias el valor de la variable useLock? ¿Por qué crees que ocurre esto?
:::

**¿Por qué ocurre la condición de carrera en este caso?**

La condición de carrera ocurre porque varios hilos están accediendo y modificando la misma variable `counter` al mismo tiempo. Cuando `useLock` es verdadero, el acceso a `counter` está protegido por un mutex, lo que evita que varios hilos lo modifiquen al mismo tiempo. Sin embargo, cuando `useLock` es falso, los hilos pueden modificar `counter` simultáneamente, lo que puede provocar resultados inesperados.

**Pero ¿Qué puede estar pasando para que esto ocurra?**

Cuando varios hilos intentan incrementar `counter` al mismo tiempo, pueden leer el mismo valor inicial de `counter`, incrementarlo y luego escribir el nuevo valor. Esto significa que dos hilos pueden leer el mismo valor, incrementarlo y escribirlo de nuevo, lo que provoca que se pierda una actualización.
Esto se debe a que la operación de incremento no es atómica, lo que significa que no se puede garantizar que un hilo complete la operación antes de que otro hilo comience. Por lo tanto, si dos hilos intentan incrementar `counter` al mismo tiempo, pueden interferir entre sí y provocar resultados inesperados.

**¿Lo puedes ver?** Te doy un poco más de ideas. Concéntrate en esta línea:

```cpp
++(*counter);
```

¿Recuerdas la unidad 1 y 2? Piensa en términos de acceso a la memoria y manejo de los registros del procesador (en la unidad 1 
nuestros registros eran A y D, ¿Recuerdas?). ¿Cómo crees que el procesador realice la línea de código anterior?

Para incrementar el valor de `counter`, el procesador debe realizar varias operaciones:
1. Leer el valor actual de `counter` desde la memoria. Ese valor se lee a un registro del procesador.
2. Incrementar el valor leído. El cálculo se realiza en el registro del procesador.
3. Escribir el nuevo valor de `counter` de nuevo en la memoria. El valor que está en el registro se escribe de nuevo en la memoria.

Esto implica que el procesador debe acceder a la memoria varias veces (¿Cuántas?) para realizar la operación de incremento. Si varios hilos intentan realizar esta operación al mismo tiempo, pueden interferir entre sí y provocar resultados inesperados.


:::note[🧐🧪✍️]
Explica en tus propias palabras ¿Cómo puede presentarse la condición de carrera en este caso? ¿Qué es lo que está pasando? 
Te pido que propongas un ejemplo.
:::


:::caution[📤 Entrega]
Reporta tus observaciones, experimentos y conclusiones en la bitácora.
:::