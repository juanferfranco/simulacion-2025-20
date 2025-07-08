#### El Reto del estado compartido

:::note[🎯 Enunciado]
Hemos visto que los hilos pueden mejorar la responsividad y acelerar cálculos independientes. Pero, ¿Qué pasa cuando múltiples hilos necesitan trabajar sobre la *misma estructura de datos compleja* al mismo tiempo? Analizaremos el código proporcionado para entender por qué la sincronización se vuelve esencial. **No necesitas implementar nada aquí, solo analizar.**
:::

**¿Qué es el algoritmo de Flocking?**

El algoritmo de Flocking es un modelo de comportamiento colectivo que simula el movimiento de grupos de entidades (como aves o peces) en un entorno. Se basa en tres reglas simples: separación, alineación y cohesión. Estas reglas permiten que las entidades se muevan de manera coordinada y eviten colisiones entre sí. El algoritmo se utiliza en gráficos por computadora y simulaciones para crear movimientos naturales y realistas de grupos de entidades.

El concepto de las reglas simples de separación, alineación y cohesión fue introducido por Craig Reynolds en 1986. En su trabajo, Reynolds demostró que estas reglas simples pueden dar lugar a comportamientos complejos y realistas en grupos de entidades.
El algoritmo de Flocking se basa en la idea de que cada entidad (o "boid") toma decisiones basadas en su entorno inmediato y en las posiciones y velocidades de sus vecinos. Esto permite que el grupo se mueva de manera cohesiva y evite colisiones, creando un comportamiento emergente.

La separación evita que los boids se acerquen demasiado entre sí, la alineación les permite coincidir con la dirección de sus vecinos y la cohesión les anima a moverse hacia el centro del grupo. Estas reglas simples permiten que los boids se comporten de manera natural y realista en un entorno simulado.

La separación se implementa calculando la distancia entre un boid y sus vecinos cercanos. Si la distancia es menor que un umbral predefinido, se aplica una fuerza de separación para alejar al boid de sus vecinos. La alineación se implementa calculando la dirección promedio de los vecinos cercanos y ajustando la dirección del boid para que coincida con esa dirección. La cohesión se implementa calculando el centro de masa de los vecinos cercanos y aplicando una fuerza hacia ese punto.

Ahora te mostraré un ejemplo de cómo implementar el algoritmo de Flocking en openFrameworks. El código está dividido en dos partes: una implementación sin hilos y otra con hilos.

**1. Flocking sin Hilos:**

Usa el evento `mouseDragged` para añadir nuevos boids a la simulación. Observa qué ocurre con el frame rate a medida que añades más boids.

```cpp
// ofApp.h
#pragma once

#include "ofMain.h"

class Boid {
public:
    Boid(float x, float y);
    void run(const vector<Boid>& boids);
    void applyForce(ofVec2f force);
    void flock(const vector<Boid>& boids);
    void update();
    void borders();
    ofVec2f seek(ofVec2f target);
    void draw();

    ofVec2f separate(const vector<Boid>& boids);
    ofVec2f align(const vector<Boid>& boids);
    ofVec2f cohere(const vector<Boid>& boids);

    ofVec2f position;
    ofVec2f velocity;
    ofVec2f acceleration;
    float r;
    float maxforce;
    float maxspeed;
};


class Flock {
public:
    void addBoid(float x, float y);
    void run();

    vector<Boid> boids;
};


class ofApp : public ofBaseApp {

public:
    void setup();
    void update();
    void draw();
    void mouseDragged(int x, int y, int button);

    Flock flock;
};
```

```cpp
// ofApp.cpp
#include "ofApp.h"

Boid::Boid(float x, float y) {
    acceleration.set(0, 0);
    velocity.set(ofRandom(-1, 1), ofRandom(-1, 1));
    position.set(x, y);
    r = 3.0;
    maxspeed = 3;
    maxforce = 0.05;
}

void Boid::run(const vector<Boid>& boids) {
    flock(boids);
    update();
    borders();
}

void Boid::applyForce(ofVec2f force) {
    acceleration += force;
}

void Boid::flock(const vector<Boid>& boids) {
    ofVec2f sep = separate(boids);
    ofVec2f ali = align(boids);
    ofVec2f coh = cohere(boids);
    sep *= 1.5;
    ali *= 1.0;
    coh *= 1.0;

    applyForce(sep);
    applyForce(ali);
    applyForce(coh);
}

void Boid::update() {
    velocity += acceleration;
    velocity.limit(maxspeed);
    position += velocity;
    acceleration *= 0;
}

ofVec2f Boid::seek(ofVec2f target) {
    ofVec2f desired = target - position;
    desired.normalize();
    desired *= maxspeed;
    ofVec2f steer = desired - velocity;
    steer.limit(maxforce);
    return steer;
}

void Boid::borders() {

    float width = ofGetWidth();
    float height = ofGetHeight();

    if (position.x < -r) position.x = width + r;
    if (position.y < -r) position.y = height + r;
    if (position.x > width + r) position.x = -r;
    if (position.y > height + r) position.y = -r;

}

void Boid::draw() {
    float theta = atan2(velocity.y, velocity.x) + PI / 2;
    ofSetColor(255);
    ofFill();
    ofPushMatrix();
    ofTranslate(position.x, position.y);
    ofRotate(ofRadToDeg(theta));
    ofBeginShape();
    ofVertex(0, -r * 2);
    ofVertex(-r, r * 2);
    ofVertex(r, r * 2);
    ofEndShape();
    ofPopMatrix();
}

ofVec2f Boid::separate(const vector<Boid>& boids) {
    float desiredSeparation = 25;
    ofVec2f steer(0, 0);
    int count = 0;

    for (const Boid& other : boids) {
        float d = position.distance(other.position);
        if (d > 0 && d < desiredSeparation) {
            ofVec2f diff = position - other.position;
            diff.normalize();
            diff /= d;
            steer += diff;
            count++;
        }
    }
    if (count > 0) {
        steer /= (float)count;
    }

    if (steer.length() > 0) {
        steer.normalize();
        steer *= maxspeed;
        steer -= velocity;
        steer.limit(maxforce);
    }
    return steer;
}

ofVec2f Boid::align(const vector<Boid>& boids) {
    float neighborDist = 50;
    ofVec2f sum(0, 0);
    int count = 0;

    for (const Boid& other : boids) {
        float d = position.distance(other.position);
        if (d > 0 && d < neighborDist) {
            sum += other.velocity;
            count++;
        }
    }
    if (count > 0) {
        sum /= (float)count;
        sum.normalize();
        sum *= maxspeed;
        ofVec2f steer = sum - velocity;
        steer.limit(maxforce);
        return steer;
    }
    return ofVec2f(0, 0);
}

ofVec2f Boid::cohere(const vector<Boid>& boids) {
    float neighborDist = 50;
    ofVec2f sum(0, 0);
    int count = 0;

    for (const Boid& other : boids) {
        float d = position.distance(other.position);
        if (d > 0 && d < neighborDist) {
            sum += other.position;
            count++;
        }
    }
    if (count > 0) {
        sum /= count;
        return seek(sum);
    }
    return ofVec2f(0, 0);
}

void Flock::addBoid(float x, float y) {
    boids.emplace_back(x, y);
}

void Flock::run() {
    for (Boid& b : boids) {
        b.run(boids);
    }
}


void ofApp::setup() {
    flock.boids.reserve(120);
    for (int i = 0; i < 120; i++) {
        flock.addBoid(ofGetWidth() / 2, ofGetHeight() / 2);
    }
}

void ofApp::update() {
    flock.run();
}

void ofApp::draw() {
    ofBackground(0);
    for (Boid& b : flock.boids) {
        b.draw();
    }
    ofDrawBitmapStringHighlight("FPS: " + ofToString(ofGetFrameRate()), 20, 20);
    ofDrawBitmapStringHighlight("Boids: " + ofToString(flock.boids.size()), 20, 40);

}

void ofApp::mouseDragged(int x, int y, int button) {
    flock.addBoid(x, y);
}
```

**2. Flocking con Hilos:**

```cpp
// ofApp.h
#pragma once

#include "ofMain.h"
#include "ofThread.h"

class Boid {
public:
    Boid(float x, float y);
    void run(const vector<Boid>& boids);
    void applyForce(ofVec2f force);
    void flock(const vector<Boid>& boids);
    void update();
    void borders();
    ofVec2f seek(ofVec2f target);
    void draw();

    ofVec2f separate(const vector<Boid>& boids);
    ofVec2f align(const vector<Boid>& boids);
    ofVec2f cohere(const vector<Boid>& boids);

    ofVec2f position;
    ofVec2f velocity;
    ofVec2f acceleration;
    float r;
    float maxforce;
    float maxspeed;
};


class Flock : public ofThread {
public:
    void addBoid(int x, int y);
    void run();
    void threadedFunction();

    std::vector<Boid> boids;
};


class ofApp : public ofBaseApp {

public:
    void setup();
    void update();
    void draw();
    void exit();
    void mouseDragged(int x, int y, int button);

    Flock flock;
};
```

```cpp
// ofApp.cpp
#include "ofApp.h"

Boid::Boid(float x, float y) {
    acceleration.set(0, 0);
    velocity.set(ofRandom(-1, 1), ofRandom(-1, 1));
    position.set(x, y);
    r = 3.0;
    maxspeed = 3;
    maxforce = 0.05;
}

void Boid::run(const vector<Boid>& boids) {
    flock(boids);
    update();
    borders();
}

void Boid::applyForce(ofVec2f force) {
    acceleration += force;
}

void Boid::flock(const vector<Boid>& boids) {
    ofVec2f sep = separate(boids);
    ofVec2f ali = align(boids);
    ofVec2f coh = cohere(boids);
    sep *= 1.5;
    ali *= 1.0;
    coh *= 1.0;

    applyForce(sep);
    applyForce(ali);
    applyForce(coh);
}

void Boid::update() {
    velocity += acceleration;
    velocity.limit(maxspeed);
    position += velocity;
    acceleration *= 0;
}

ofVec2f Boid::seek(ofVec2f target) {
    ofVec2f desired = target - position;
    desired.normalize();
    desired *= maxspeed;
    ofVec2f steer = desired - velocity;
    steer.limit(maxforce);
    return steer;
}

void Boid::borders() {
    if (position.x < -r) position.x = ofGetWidth() + r;
    if (position.y < -r) position.y = ofGetHeight() + r;
    if (position.x > ofGetWidth() + r) position.x = -r;
    if (position.y > ofGetHeight() + r) position.y = -r;
}

void Boid::draw() {
    float theta = atan2(velocity.y, velocity.x) + PI / 2;
    ofSetColor(255);
    ofFill();
    ofPushMatrix();
    ofTranslate(position.x, position.y);
    ofRotate(ofRadToDeg(theta));
    ofBeginShape();
    ofVertex(0, -r * 2);
    ofVertex(-r, r * 2);
    ofVertex(r, r * 2);
    ofEndShape();
    ofPopMatrix();
}

ofVec2f Boid::separate(const vector<Boid>& boids) {
    float desiredSeparation = 25;
    ofVec2f steer(0, 0);
    int count = 0;

    for (const Boid& other : boids) {
        float d = position.distance(other.position);
        if (d > 0 && d < desiredSeparation) {
            ofVec2f diff = position - other.position;
            diff.normalize();
            diff /= d;
            steer += diff;
            count++;
        }
    }
    if (count > 0) {
        steer /= (float)count;
    }

    if (steer.length() > 0) {
        steer.normalize();
        steer *= maxspeed;
        steer -= velocity;
        steer.limit(maxforce);
    }
    return steer;
}

ofVec2f Boid::align(const vector<Boid>& boids) {
    float neighborDist = 50;
    ofVec2f sum(0, 0);
    int count = 0;

    for (const Boid& other : boids) {
        float d = position.distance(other.position);
        if (d > 0 && d < neighborDist) {
            sum += other.velocity;
            count++;
        }
    }
    if (count > 0) {
        sum /= (float)count;
        sum.normalize();
        sum *= maxspeed;
        ofVec2f steer = sum - velocity;
        steer.limit(maxforce);
        return steer;
    }
    return ofVec2f(0, 0);
}

ofVec2f Boid::cohere(const vector<Boid>& boids) {
    float neighborDist = 50;
    ofVec2f sum(0, 0);
    int count = 0;

    for (const Boid& other : boids) {
        float d = position.distance(other.position);
        if (d > 0 && d < neighborDist) {
            sum += other.position;
            count++;
        }
    }
    if (count > 0) {
        sum /= count;
        return seek(sum);
    }
    return ofVec2f(0, 0);
}

void Flock::addBoid(int x, int y) {
    lock();
    boids.emplace_back(x, y);
    unlock();
}

void Flock::run() {
    if (!isThreadRunning()) {
        startThread();
    }
}

void Flock::threadedFunction() {
    while (isThreadRunning()) {
        lock();
        for (Boid& b : boids) {
            b.run(boids);
        }
        unlock();
        sleep(5);
    }
}

void ofApp::setup() {
    // Add an initial set of boids
    for (int i = 0; i < 120; i++) {
        flock.addBoid(ofGetWidth() / 2, ofGetHeight() / 2);
    }
}

void ofApp::update() {
    flock.run();
}

void ofApp::draw() {
    ofBackground(0);

    flock.lock();
    for (Boid& b : flock.boids) {
        b.draw();
    }
    flock.unlock();

    ofDrawBitmapStringHighlight("FPS: " + ofToString(ofGetFrameRate()), 20, 20);
    ofDrawBitmapStringHighlight("Boids: " + ofToString(flock.boids.size()), 20, 40);
}

void ofApp::mouseDragged(int x, int y, int button) {
    flock.addBoid(x, y);
}

void ofApp::exit() {
    flock.stopThread();
    flock.waitForThread();
}
```

Analicemos juntos varias partes del código:

- **Flocking sin hilos**: en este enfoque, la simulación de los boids se realiza en el hilo principal. Cada boid actualiza su posición y dibuja su representación gráfica en cada frame. Esto puede llevar a una disminución del rendimiento a medida que se añaden más boids, ya que todo el trabajo se realiza en un solo hilo.
- **Flocking con hilos**: en este enfoque, la simulación de los boids se realiza en un hilo separado. Esto permite que el hilo principal se encargue de la representación gráfica, mientras que el hilo de la simulación se encarga de actualizar las posiciones de los boids. La sincronización se maneja mediante `lock()` y `unlock()` para evitar condiciones de carrera al acceder a la lista de boids.

:::note[🧐✍️]
Observa ambos códigos y responde a las siguientes preguntas:

1. ¿Cuál es la estructura de datos principal que contiene la información de todos los boids y que es accedida por múltiples hilos (el hilo principal para dibujar, el hilo trabajador para actualizar)?
2. Observa la función `Flock::threadedFunction()` donde el hilo trabajador calcula el movimiento. ¿Qué operaciones realizan sobre el vector de boids compartido? 
*   Observa la función `ofApp::draw()`. ¿Qué operación realiza sobre el vector compartido?
*   Observa `Flock::addBoid()` y `ofApp::mouseDragged()`. ¿Qué operación realizan?

3. Describe un escenario *específico* y *concreto* donde la falta de sincronización podría causar un problema. Por ejemplo:

    *   "El Hilo X está recorriendo el vector para calcular la separación (leyendo posiciones). Al mismo tiempo, el Hilo Y (llamado desde `mouseDragged`) intenta añadir un nuevo boid al final del vector. ¿Qué podría pasarle al iterador del Hilo X o al tamaño del vector que está usando?"

4. Localiza todas las llamadas a `lock()` y `unlock()` dentro de la clase `Flock` (o donde se acceda al vector compartido).

**Justificación:** para uno de los escenarios problemáticos que describiste arriba, explica cómo las llamadas a `lock()`/`unlock()` en las secciones de código relevantes *evitan* que ocurra ese problema específico.

5. Aunque los locks aseguran la correctitud, ¿Puedes intuir por qué tener muchos hilos esperando para adquirir un lock sobre el mismo vector (alta **contención**) podría limitar el beneficio de rendimiento del paralelismo en este caso? Justifica tu respuesta.
:::

En el ejemplo que te di del flocking con hilos, el hilo principal se encarga de dibujar los boids y el hilo secundario se encarga de calcular el movimiento de los boids. Sin embargo, este escenario es muy limitado porque solo hay dos hilos: el principal y el hilo trabajador que calcula el flocking, pero **realmente no se está explotando la idea de tener hilos**. **¿Es correcto esto?**

:::note[🧐✍️]
Este es un ejercicio mental y de reflexión, no tienes que implementar nada, solo pensar:

Piensa en la pregunta que te acabo de hacer. ¿Qué pasaría si tuviéramos varios hilos que calculan el movimiento de los boids? ¿Cómo podrías implementar esto? ¿Qué problemas crees que podrían surgir? ¿Cómo podrías solucionarlos? 
:::

:::note[🧐🧪✍️]
- Analiza el código del Flocking sin hilos y el Flocking con hilos. ¿Qué diferencias encuentras? ¿Por qué crees que es importante la sincronización en el segundo caso?
- ¿Por qué al añadir un nuevo boid la simulación se ralentiza? ¿Qué ocurre si añades muchos boids?
- Notaste que la versión con hilos tiene un `sleep(5)` en el hilo trabajador. ¿Por qué crees que se ha añadido? ¿Qué pasaría si lo eliminamos?
- Compara el rendimiento de ambos enfoques. ¿Cuál crees que es más eficiente? ¿Por qué?
- El uso de lock y unlock en la versión con hilos es crucial para evitar condiciones de carrera. ¿Qué pasaría si no se usaran? ¿Cómo afectaría esto al comportamiento del programa? (No olvides por favor que las condiciones de carrera son difíciles de reproducir, así que no te preocupes si no puedes verlas en acción).
:::

**Reflexión final:**

El ejemplo de flocking con hilos, aunque utiliza ofThread, no explota realmente el paralelismo para acelerar el cálculo del flocking en sí mismo.

Lo que hace esa implementación es:

- Mover el trabajo pesado: traslada el bucle principal que calcula boid.run() para todos los boids desde el hilo principal (ofApp::update) a un único hilo secundario (Flock::threadedFunction).  
- Lograr responsividad: el principal beneficio aquí es que el hilo principal (que maneja el dibujo y los eventos de UI como mouseDragged) ya no se bloquea mientras se realizan los cálculos potencialmente largos del flocking. La aplicación se siente más fluida y responde a la interacción incluso si el cálculo del flocking es intensivo. 
- Cálculo secuencial (dentro del hilo): dentro de Flock::threadedFunction, el bucle for (Boid& b : boids) sigue procesando cada boid uno tras otro, de forma secuencial. No hay múltiples hilos trabajando simultáneamente en diferentes subconjuntos de boids para calcular sus interacciones.

:::note[🧐✍️]
¿Qué ocurre si mientras el hilo trabajador está calculando el movimiento de los boids, el hilo principal intenta añadir un nuevo boid? ¿Se congelará la aplicación? ¿Por qué?
:::

En resumen, la versión de flocking con hilos mejora la responsividad al mover el trabajo pesado a un hilo secundario, pero no logra paralelismo real para acelerar el cálculo del flocking. La implementación actual sigue siendo secuencial dentro del hilo secundario.

Para explotar verdaderamente el paralelismo en el flocking, necesitarías múltiples hilos trabajadores. La idea sería crear varios hilos (no solo uno). Luego dividir el conjunto de boids o las tareas de cálculo entre esos hilos. Por ejemplo: cada hilo calcula las interacciones para un subconjunto de boids o usar un enfoque basado en tareas donde cada hilo toma un boid, calcula sus fuerzas y actualiza su estado. Sin embargo, el gran desafío aquí es que para calcular las fuerzas de flocking (separación, alineación, cohesión), un boid necesita información sobre sus vecinos. Si diferentes hilos trabajan en diferentes boids, necesitarán leer de forma segura la información (posiciones, velocidades) de boids que podrían estar siendo modificados por otros hilos. Esto requiere una sincronización mucho más cuidadosa y granular que simplemente bloquear todo el vector, o técnicas más avanzadas (como dividir el espacio, usar copias de datos, etc.), lo cual complica significativamente el código y puede introducir sus propios cuellos de botella por la sincronización.

**Conclusión** 

El ejemplo es bueno para ilustrar cómo mover una tarea completa a un hilo secundario para mejorar la responsividad, y también introduce la necesidad de lock/unlock al acceder a datos compartidos (el vector boids) desde diferentes hilos (el hilo principal para draw/addBoid y el hilo secundario para threadedFunction). Sin embargo, no es un ejemplo de paralelización del cálculo para obtener **speedup**. 

:::caution[📤 Entrega]
Responde a las preguntas planteadas en los ejercicios y reflexiona sobre el uso de hilos y la sincronización.
:::

