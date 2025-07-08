#### Hilos para el paralelismo

:::note[🎯 Enunciado]
Otro uso clave de los hilos es acelerar tareas que requieren mucho cálculo, siempre que estas tareas puedan dividirse en partes independientes. No olvides la actividad anterior por favor. Si hay datos compartidos entre los hilos, debes proteger el acceso a esos datos. En esta actividad vas a observar el rendimiento de un algoritmo para calcular un fractal de Mandelbrot. El algoritmo se puede paralelizar, ya que cada pixel del fractal se puede calcular de forma independiente. La idea será entonces que compares el rendimiento de un algoritmo secuencial y uno paralelo.
:::

**Fractal de Mandelbrot**

El fractal de Mandelbrot es un conjunto de puntos en el plano complejo que se define mediante una función iterativa. La función se basa en la siguiente fórmula:

$$
z_{n+1} = z_n^2 + c
$$

donde z es un número complejo y c es una constante compleja. 

El conjunto de Mandelbrot se forma al iterar esta función y observar si los puntos permanecen dentro de un cierto rango o "escapan" a infinito. 

El conjunto de Mandelbrot se representa visualmente asignando un color a cada punto en función del número de iteraciones que tarda en escapar a infinito. Los puntos que no escapan se consideran parte del conjunto y se les asigna un color específico (generalmente negro). Los puntos que escapan se les asigna un color en función de cuántas iteraciones tardaron en escapar. Esto crea una imagen fractal con patrones complejos y hermosos.

El algoritmo para calcular el conjunto de Mandelbrot implica iterar sobre cada píxel de la imagen y aplicar la función iterativa. Para cada píxel, se mapea a un punto en el plano complejo y se calcula cuántas iteraciones tarda en escapar a infinito. El número máximo de iteraciones se puede ajustar para obtener más o menos detalle en la imagen.

El algoritmo básico para calcular el conjunto de Mandelbrot es el siguiente:

1. Inicializar una imagen en blanco.
2. Para cada píxel de la imagen:
   1. Mapear las coordenadas del píxel a un punto en el plano complejo.
   2. Inicializar z = 0 y c = punto complejo.
   3. Iterar la función $$z = z^2 + c$$ hasta que $$|z| > 2$$ o se alcance el número máximo de iteraciones.
   4. Asignar un color al píxel en función del número de iteraciones.
5. Mostrar la imagen resultante.

El algoritmo de Mandelbrot es un ejemplo clásico de un problema que se puede paralelizar, ya que cada píxel se puede calcular de forma independiente. Esto significa que puedes dividir el trabajo entre varios hilos y calcular el conjunto de Mandelbrot más rápidamente.

El código que te propongo a continuación es un ejemplo de cómo implementar el algoritmo de Mandelbrot en openFrameworks. El código está dividido en dos versiones: una secuencial y otra paralela. La versión paralela utiliza hilos para acelerar el cálculo del conjunto de Mandelbrot.

**Versión secuencial**:

ofApp.h

```cpp
// ofApp.h
#pragma once

#include "ofMain.h"

class ofApp : public ofBaseApp {

public:
    void setup();
    void update();
    void draw();
    void keyPressed(int key);

    void startCalculation();
    int calculateMandelbrotPixel(int x, int y);
    ofColor mapIterationsToColor(int iterations);

    ofPixels pixels;      
    ofTexture texture;    

    int imgWidth;
    int imgHeight;
    int maxIterations;

    float startTime;
    float calculationTime;
    bool calculating;
    string statusMessage;
};
```

ofApp.cpp:


```cpp
// ofApp.cpp

#include "ofApp.h" 

void ofApp::setup() {
    ofSetWindowTitle("Mandelbrot Secuencial");
    ofSetFrameRate(60); 
    ofBackground(30);

    imgWidth = ofGetWidth(); 
    imgHeight = ofGetHeight();
    maxIterations = 100; 

    pixels.allocate(imgWidth, imgHeight, OF_PIXELS_RGB);
    texture.allocate(pixels);

    calculating = false;
    calculationTime = 0.0f;
    statusMessage = "Listo. \nPresiona ESPACIO para calcular.";
}

//--------------------------------------------------------------
void ofApp::startCalculation() {
    if (calculating) return; 

    calculating = true;
    statusMessage = "Calculando...";
    ofLogNotice() << statusMessage;
    startTime = ofGetElapsedTimef(); // Tiempo en segundos

    // --- Cálculo Secuencial ---
    for (int y = 0; y < imgHeight; ++y) {
        for (int x = 0; x < imgWidth; ++x) {
            int iterations = calculateMandelbrotPixel(x, y);
            pixels.setColor(x, y, mapIterationsToColor(iterations));
        }
    }
    // --- Fin Cálculo ---

    calculationTime = ofGetElapsedTimef() - startTime;
    calculating = false;
    statusMessage = "Cálculo completado. \nPresiona ESPACIO para recalcular.";
    ofLogNotice() << statusMessage << " Tiempo: " << calculationTime << " s";

    // Actualizar la textura con los nuevos píxeles
    texture.loadData(pixels);
}

//--------------------------------------------------------------
int ofApp::calculateMandelbrotPixel(int x, int y) {
    // Mapear coordenadas de píxel a plano complejo
    // Rango típico: x de -2.0 a 1.0, y de -1.5 a 1.5
    float cx = ofMap(x, 0, imgWidth, -2.0, 1.0);
    float cy = ofMap(y, 0, imgHeight, -1.5, 1.5);

    float zx = 0.0;
    float zy = 0.0;
    int iterations = 0;

    while ( (zx * zx + zy * zy) < 4.0 && iterations < maxIterations) {
        float tempX = zx * zx - zy * zy + cx;
        zy = 2.0 * zx * zy + cy;
        zx = tempX;
        iterations++;
    }
    return iterations;
}

//--------------------------------------------------------------
ofColor ofApp::mapIterationsToColor(int iterations) {
    if (iterations == maxIterations) {
        return ofColor::black; // Dentro del conjunto
    }
    else {
        float hue = ofMap(iterations, 0, maxIterations, 0, 255);
        float brightness = ofMap(iterations, 0, maxIterations, 100, 255);
        float saturation = 200; // Constante para color
        return ofColor::fromHsb(hue, saturation, brightness); // Colorido
    }
}

//--------------------------------------------------------------
void ofApp::update() {
}

//--------------------------------------------------------------
void ofApp::draw() {
    ofSetColor(255); // Dibujar textura con color blanco
    texture.draw(0, 0, ofGetWidth(), ofGetHeight());

    // --- UI de Métricas ---
    stringstream ss;
    ss << "Version: Secuencial" << endl;
    ss << "Status: " << statusMessage << endl;
    if (!calculating && calculationTime > 0.0f) {
        ss << "Ultimo Tiempo: " << ofToString(calculationTime, 3) << " s" << endl;
    }
    ss << "Max Iteraciones: " << maxIterations << endl;
    ss << "Resolucion: " << imgWidth << "x" << imgHeight << endl;
    ss << "FPS: " << ofToString(ofGetFrameRate(), 0); // Será bajo o 0 durante el cálculo

    ofSetColor(0, 180); // Fondo semi-transparente para el texto
    ofDrawRectangle(10, 10, 350, 120);
    ofSetColor(255); // Texto blanco
    ofDrawBitmapString(ss.str(), 20, 30);
}

//--------------------------------------------------------------
void ofApp::keyPressed(int key) {
    if (key == ' ') {
        maxIterations += 1;
        startCalculation();
        
    }
    if (key == 'n') {

        maxIterations = 0;
        startCalculation();
    }
}
```

**Versión paralela**:

ofApp.h

```cpp
// ofApp.h
#pragma once

#include "ofMain.h"
#include "ofThread.h" 

class MandelbrotThread : public ofThread {
public:
    MandelbrotThread(int startY, int endY, int width, int height, int maxIter, ofPixels& pixelsRef)
        : startRow(startY), endRow(endY), imgWidth(width), imgHeight(height),
        maxIterations(maxIter), pixels(pixelsRef) {
    } 

    void threadedFunction() override {
        for (int y = startRow; y < endRow && isThreadRunning(); ++y) {
            for (int x = 0; x < imgWidth; ++x) {
                int iterations = calculateMandelbrotPixel(x, y);
                pixels.setColor(x, y, mapIterationsToColor(iterations));
            }
        }
        
        ofLogVerbose("MandelbrotThread") << "Hilo para filas " << startRow << "-" << endRow << " terminado.";
    }

private:
    int startRow, endRow;
    int imgWidth, imgHeight;
    int maxIterations;
    ofPixels& pixels; 

    int calculateMandelbrotPixel(int x, int y) {
        float cx = ofMap(x, 0, imgWidth, -2.0, 1.0);
        float cy = ofMap(y, 0, imgHeight, -1.5, 1.5);
        float zx = 0.0, zy = 0.0;
        int iterations = 0;
        while (zx * zx + zy * zy < 4.0 && iterations < maxIterations) {
            float tempX = zx * zx - zy * zy + cx;
            zy = 2.0 * zx * zy + cy;
            zx = tempX;
            iterations++;
        }
        return iterations;
    }

    ofColor mapIterationsToColor(int iterations) {
        if (iterations == maxIterations) return ofColor::black;
        else {
            float hue = ofMap(iterations, 0, maxIterations, 0, 255);
            float brightness = ofMap(iterations, 0, maxIterations, 100, 255);
            float saturation = 200;
            return ofColor::fromHsb(hue, saturation, brightness);
        }
    }
};


class ofApp : public ofBaseApp {

public:
    void setup();
    void update();
    void draw();
    void exit(); 
    void keyPressed(int key);

    void startCalculation();

    ofPixels pixels;
    ofTexture texture;

    int imgWidth;
    int imgHeight;
    int maxIterations;
    int numThreads;

    vector<MandelbrotThread*> threads; 

    float startTime;
    float calculationTime;
    bool calculating;
    string statusMessage;
    int runningThreads;
};

```

ofApp.cpp

```cpp
// ofApp.cpp
#include "ofApp.h" 
#include <thread> 

//--------------------------------------------------------------
void ofApp::setup() {
    ofSetWindowTitle("Mandelbrot Paralelo (ofThread)");
    ofSetFrameRate(60);
    ofBackground(30);

    imgWidth = ofGetWidth();
    imgHeight = ofGetHeight();
    maxIterations = 100;

    numThreads = std::thread::hardware_concurrency();
    
    if (numThreads == 0) numThreads = 4; // Fallback si no se puede detectar
    ofLogNotice() << "Usando " << numThreads << " hilos.";

    pixels.allocate(imgWidth, imgHeight, OF_PIXELS_RGB);
    texture.allocate(pixels);

    calculating = false;
    calculationTime = 0.0f;
    runningThreads = 0;
    statusMessage = "Listo. \nPresiona ESPACIO para calcular.";

}

//--------------------------------------------------------------
void ofApp::startCalculation() {
    if (calculating) {
        ofLogWarning() << "Ya se está calculando, espera a que termine.";
        return;
    }
    calculating = true;
    runningThreads = 0; // Reseteamos contador antes de lanzar nuevos
    statusMessage = "Calculando con " + ofToString(numThreads) + " hilos...";

    ofLogNotice() << statusMessage;
    startTime = ofGetElapsedTimef();

    if (!threads.empty()) {
        ofLogVerbose() << "Limpiando hilos anteriores...";
        for (auto& thread : threads) {
            thread->waitForThread(true); 
            delete thread;
        }
        threads.clear();
        ofLogVerbose() << "Hilos anteriores limpiados.";
    }

    int rowsPerThread = imgHeight / numThreads;
    for (int i = 0; i < numThreads; ++i) {
        int startY = i * rowsPerThread;
        int endY = (i == numThreads - 1) ? imgHeight : (i + 1) * rowsPerThread; // Asegura que el último hilo llegue hasta el final

        MandelbrotThread* newThread = new MandelbrotThread(startY, endY, imgWidth, imgHeight, maxIterations, pixels);
        threads.push_back(newThread);
        runningThreads++;
        threads.back()->startThread(); // Inicia la ejecución de threadedFunction
        ofLogVerbose() << "Lanzado hilo " << i << " para filas " << startY << "-" << endY;
    }
    ofLogNotice() << runningThreads << " hilos lanzados.";
}

//--------------------------------------------------------------
void ofApp::update() {
    bool allThreadsFinished = true;
    if (!threads.empty()) { // Solo comprobar si hay hilos
        for (const auto& thread : threads) {
            if (thread->isThreadRunning()) {
                allThreadsFinished = false;
                break; // Si uno sigue corriendo, no necesitamos comprobar los demás
            }
        }
    }
    else {
        // Si no hay hilos en el vector, definitivamente no están corriendo
        allThreadsFinished = true;
    }

    if (allThreadsFinished && calculating) {
        calculationTime = ofGetElapsedTimef() - startTime;
        calculating = false;
        runningThreads = 0; 
        statusMessage = "Cálculo completado. \nPresiona ESPACIO para recalcular.";
        ofLogNotice() << statusMessage << " Tiempo: " << calculationTime << " s";
        texture.loadData(pixels);
    }
}

//--------------------------------------------------------------
void ofApp::draw() {
    ofSetColor(255);
    texture.draw(0, 0, ofGetWidth(), ofGetHeight());

    stringstream ss;
    ss << "Version: Paralelo (ofThread)" << endl;

    ss << "Status: " << statusMessage << endl;
    if (!calculating && calculationTime > 0.0f) {
        ss << "Ultimo Tiempo: " << ofToString(calculationTime, 3) << " s" << endl;
    }
    ss << "Hilos Usados: " << numThreads << endl;
    // ss << "Hilos Corriendo: " << runningThreads << endl; // Podría fluctuar rápido

    ss << "Max Iteraciones: " << maxIterations << endl;
    ss << "Resolucion: " << imgWidth << "x" << imgHeight << endl;
    ss << "FPS: " << ofToString(ofGetFrameRate(), 0);

    ofSetColor(0, 180);
    ofDrawRectangle(10, 10, 350, 120); // Un poco más grande
    ofSetColor(255);
    ofDrawBitmapString(ss.str(), 20, 30);
}

//--------------------------------------------------------------
void ofApp::exit() {
    ofLogNotice() << "Saliendo, esperando a los hilos...";
    for (auto& thread : threads) {
        thread->waitForThread(true); // Espera bloqueante hasta que el hilo termine
        delete thread; // Liberar memoria
    }
    threads.clear();
    ofLogNotice() << "Hilos detenidos y limpiados. Adiós.";
}

//--------------------------------------------------------------
void ofApp::keyPressed(int key) {
    if (key == ' ') {
        maxIterations += 1;
        startCalculation();
    }
	if (key == 'n') {
		maxIterations = 0;
		startCalculation();
	}
}
```

:::note[🧐🧪✍️]
- Ejecuta el código y observa el resultado. 
- Analiza el código y estudia detenidamente su funcionamiento. En la fase de aplicación tendrás que retomar 
este código para resolver un reto.
- Experimenta modificando, PERO, no olvides cómo investigamos en este curso:

    - Realiza cambios pequeños y específicos.
    - Lanza una hipótesis sobre lo que crees que va a pasar.
    - Ejecuta el código y observa lo que ocurre.
    - ¿Tu hipótesis era correcta? ¿Por qué crees que ocurre esto?

Te dejo una idea para comenzar a experimentar: ¿Qué ocurre si cambias el número de hilos? ¿Por qué crees que ocurre esto?
:::


:::caution[📤 Entrega]
Reporta en tu bitácora el resultado de la actividad.
:::