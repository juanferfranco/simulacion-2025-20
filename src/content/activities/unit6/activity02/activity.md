#### Investiga el patrón observer

:::note[🎯 Enunciado]
En esta actividad, profundizarás en el patrón de diseño ``Observer``. Analizarás su propósito, 
estructura y cómo está implementado en el caso de estudio que exploraste en la actividad anterior. 
El objetivo es que comprendas cómo este patrón permite la comunicación entre objetos de forma desacoplada.
:::

**Concepto del Patrón Observer**

Imagina que quieres recibir notificaciones de una tienda online cuando tu producto favorito vuelve a estar en stock. En lugar de revisar la página web constantemente (polling), te suscribes a las notificaciones. Cuando el producto está disponible, la tienda (el *Sujeto* u *Observable*) envía automáticamente un mensaje a todos los *Observadores* suscritos (como tú).

El patrón Observer define una dependencia uno-a-muchos entre objetos, de manera que cuando un objeto (el Sujeto) cambia su estado, todos sus dependientes (Observadores) son notificados y actualizados automáticamente.

Componentes clave:

*   **Subject (Sujeto):** mantiene una ``lista de Observadores``. Proporciona métodos para agregar (`attach`/`addObserver`), eliminar (`detach`/`removeObserver`) y notificar (`notify`) a los observadores.
*   **Observer (Observador):** define una interfaz de actualización (`update`/`onNotify`) que será llamada cuando el Sujeto cambie.
*   **ConcreteSubject (Sujeto Concreto):** almacena el estado de interés y envía notificaciones a sus observadores cuando su estado cambia.
*   **ConcreteObserver (Observador Concreto):** implementa la interfaz Observer. Almacena una referencia al Sujeto Concreto (opcional) y reacciona a la notificación actualizando su propio estado.

**Análisis del caso de estudio**

Vuelve al código del caso de estudio (`ofApp.h` y `ofApp.cpp`).

1.  **Identifica los Roles:**
    *   ¿Qué clase actúa como la interfaz `Observer`? ¿Qué método define?
    *   ¿Qué clase actúa como `Subject`? ¿Qué métodos proporciona para gestionar observadores y notificar?
    *   ¿Qué clase es el `ConcreteSubject` en esta aplicación? ¿Por qué? (Pista: ¿Quién *envía* las notificaciones?)
    *   ¿Qué clase(s) actúan como `ConcreteObserver`? ¿Por qué? (Pista: ¿Quién *recibe* y *reacciona* a las notificaciones?)

2.  **Sigue el flujo de notificación:**
    *   Localiza el método `keyPressed` en `ofApp.cpp`. ¿Qué sucede cuando se presiona la tecla 'a'? ¿Qué método se llama?
    *   Ve al método `notify` en la clase `Subject`. ¿Qué hace este método?
    *   Localiza el método que implementa la interfaz `Observer` en la clase `Particle` (`onNotify`). ¿Qué hace este método cuando recibe el evento "attract"?

3.  **Registro y eliminación de observadores:**
    *   ¿En qué parte del código se añaden las instancias de `Particle` como observadores de `ofApp`? (Busca dónde se llama a `addObserver`).
    *   Aunque no se usa explícitamente en este ejemplo simple, ¿Dónde se eliminarían los observadores si fuera necesario (por ejemplo, si una partícula se destruyera durante la ejecución)? (Busca `removeObserver`). ¿Por qué es importante el destructor de `ofApp` en este contexto?

:::note[🧐🧪✍️ Reporta en tu bitácora]
1.  Explica con tus propias palabras el propósito del patrón Observer. ¿Qué problema resuelve?
2.  Dibuja un diagrama simple (puede ser un diagrama de clases UML básico o un diagrama de bloques) que muestre la relación entre `Subject`, `Observer`, `ofApp` y `Particle` en el caso de estudio, indicando quién es el Sujeto y quiénes los Observadores.
3.  Describe paso a paso qué ocurre en el código desde que presionas la tecla 'r' hasta que las partículas empiezan a ser repelidas por el mouse. Menciona las clases y métodos clave involucrados en la notificación y la reacción.
4.  ¿Qué ventajas crees que ofrece usar el patrón Observer en esta aplicación en comparación con, por ejemplo, que `ofApp::update` recorriera todas las partículas y les dijera directamente que cambien su comportamiento basado en una variable global? Piensa en términos de acoplamiento y extensibilidad.
:::

:::caution[📤 Entrega]
Documenta tu análisis, diagrama, descripción del flujo y reflexión sobre las ventajas del patrón Observer en tu bitácora de aprendizaje.
:::