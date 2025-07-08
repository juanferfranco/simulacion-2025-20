#### Orientación y Conceptos

:::note[🎯 Enunciado]
En esta actividad introductoria, vamos a explorar de manera práctica los conceptos que aprenderás en esta unidad. 
:::

En esta unidad vamos a utilizar algunos servicios del sistema operativo que te permitirán acceder a múltiples 
recursos de procesamiento del hardware de tu computador: **los cores**. Para ello utilizarás de nuevo openFrameworks.

**¿Qué es un sistema operativo?**

En términos generales, un sistema operativo es un SOFTWARE que administra RECURSOS de hardware y software del computador y provee servicios mediante los cuales los programas de usuario pueden hacer uso de esos recursos.

El servicio que vamos a explorar en esta actividad es el de hilos o threads. Un hilo es una unidad de ejecución dentro de un proceso. Un proceso puede contener múltiples hilos, cada uno de los cuales puede ejecutar su propio código de manera concurrente. Los hilos comparten el mismo espacio de memoria y recursos del proceso, lo que les permite comunicarse entre sí de manera eficiente.

**¿Qué es esto de un proceso?** No es más que un programa en ejecución. Entonces en términos simples, los hilos permiten que 
un proceso (programa en ejecución) pueda ejecutar múltiples tareas al mismo tiempo.

**De nuevo ¿Qué es un hilo?** 

Hasta ahora todos los programas que has realizado tienen un **SOLO flujo de instrucciones**. ¿Y si quieres tener en el mismo programa VARIOS flujos de instrucciones independientes? Lo puedes hacer con los hilos. Los hilos permitirán que un programa pueda **HACER VARIAS COSAS AL MISMO TIEMPO**, cada cosa con un hilo independiente. Ten presente que los hilos compartirán recursos del proceso entre ellos estará el HEAP; sin embargo, cada hilo tendrá su propio STACK (que belleza, ya podemos relacionar el stack y el heap con los hilos).

**Y en la práctica ¿Cómo sería la cosa?**

Crea un proyecto en openFrameworks y modifica el archivo `ofApp.h` para que contenga lo siguiente:

```cpp
#pragma once
#include "ofMain.h"

class ofApp : public ofBaseApp {
public:
    float x = 0;
    float speed = 3;
    float circleSize = 50;

    void setup();
    void draw();
    void mousePressed(int x, int y, int button);
    void heavyComputation();
};

```

Ahora modifica el archivo `ofApp.cpp` para que contenga lo siguiente:

```cpp
#include "ofApp.h"

void ofApp::setup() {
    ofSetFrameRate(60);
    ofSetWindowShape(400, 400);
}

void ofApp::draw() {
    ofBackground(220);
    ofSetColor(0);
    ofDrawCircle(x, ofGetHeight() / 2, circleSize);
    x = fmod(x + speed, ofGetWidth());
}

void ofApp::mousePressed(int x, int y, int button) {
    heavyComputation();
}

void ofApp::heavyComputation() {
    double result = 0;
    for (int j = 0; j < 1000000000; ++j) {
        result += sqrt(j);
    }
    circleSize = ofRandom(20, 70);
    ofLog() << "Circle size: " << circleSize;
}
```

:::note[🧐🧪✍️]
Ejecuta el programa y haz clic en la ventana. Observa lo que sucede. ¿Qué es lo que ves? ¿Qué es lo que esperabas ver? ¿Por qué crees que sucede esto?
:::

El problema que acabas de observar es que el programa se congela cuando haces clic en la ventana. Esto sucede porque la función `heavyComputation()` está bloqueando el hilo principal de la aplicación, lo que impide que la interfaz gráfica se actualice.
Esto es un problema común en aplicaciones que requieren realizar cálculos intensivos o tareas de larga duración. La solución a este problema es mover la tarea pesada a un hilo separado, lo que permitirá que la interfaz gráfica siga siendo responsiva mientras se realiza el cálculo.

Ahora vamos a crear un hilo para la función `heavyComputation()`. Para ello, primero debes incluir la biblioteca de hilos de openFrameworks en tu archivo `ofApp.h`:

```cpp
#include "ofMain.h"
#include "ofThread.h"
```
Luego, debes crear una clase que herede de `ofThread` y que contenga la función `heavyComputation()`. Modifica el archivo `ofApp.h` para que contenga lo siguiente:

```cpp
#pragma once
#include "ofMain.h"
#include "ofThread.h"

class ofApp : public ofBaseApp, public ofThread {
public:
    float x = 0;
    float speed = 3;
    float circleSize = 50;

    void setup();
    void draw();
    void mousePressed(int x, int y, int button);
    void heavyComputation();
    void startHeavyComputation();
    void threadedFunction() override;
    void exit();
};
```

Modifica el archivo `ofApp.cpp` para que contenga lo siguiente:

```cpp
#include "ofApp.h"

void ofApp::setup() {
    ofSetFrameRate(60);
    ofSetWindowShape(400, 400);
}

void ofApp::draw() {

    ofBackground(220);
    ofSetColor(0);
    lock();
    ofDrawCircle(x, ofGetHeight() / 2, circleSize);
    unlock();
    x = fmod(x + speed, ofGetWidth());
}

void ofApp::mousePressed(int x, int y, int button) {
    if (!isThreadRunning()) {
        std::cout << "Starting heavy computation in a thread" << std::endl;
        startThread();
    }
    else {
        std::cout << "Thread is already running" << std::endl;
    }
}

void ofApp::threadedFunction() {
    heavyComputation();
    std::cout << "Thread ends" << std::endl;
}

void ofApp::heavyComputation() {
    double result = 0;
    for (int j = 0; j < 1000000000; ++j) {
        result += sqrt(j);
    }
    lock();
    ofSeedRandom();
    circleSize = ofRandom(20, 70);
    unlock();
	std::cout << "Circle size: " << circleSize << std::endl;
}

void ofApp::exit() {
    if (isThreadRunning()) {
        stopThread();
        waitForThread();
    }
}

```
:::note[🧐🧪✍️]
Ejecuta el programa y haz clic en la ventana. Observa lo que sucede. ¿Qué es lo que ves? ¿Qué es lo que esperabas ver? ¿Por qué crees que sucede esto?

Observa que el programa ahora no se congela, pero el círculo no cambia de tamaño inmediatamente. ¿Por qué crees que sucede esto? ¿Qué es lo que está pasando?
:::

Vamos a repasar de nuevo algunos conceptos importantes:

**Repaso de conceptos**
*   **Proceso:** una instancia de un programa en ejecución (tiene su propia memoria: stack, heap, etc.).
*   **Hilo (Thread):** un flujo de instrucciones *dentro* de un proceso. Un proceso puede tener múltiples hilos. En el último 
programa, ¿Puedes identificar los hilos que se están ejecutando? ¿Cuántos hilos hay en total? La cantidad de hilos cambia 
cuando ¿Haces clic en la ventana? ¿Termina el heavyComputation()?
*   **Memoria Compartida:** los hilos dentro del mismo proceso comparten la mayor parte de la memoria (como el heap y las variables globales). ¡Esto es potente pero peligroso! Cada hilo tiene su *propio* stack para variables locales y llamadas a funciones.
*   **Concurrencia:** la idea de que múltiples tareas parecen progresar al mismo tiempo (pueden estar intercalándose en un solo núcleo o corriendo en paralelo en múltiples núcleos).
*   **Paralelismo:** la ejecución *simultánea* real de múltiples tareas (requiere hardware con múltiples núcleos).

:::note[🧐🧪✍️]
En tus propias palabras, explica la diferencia entre concurrencia y paralelismo. ¿Por qué es importante entender esta diferencia al trabajar con hilos?
:::

:::caution[📤 Entrega]
:::