#### Investiga el Patrón State

:::note[🎯 Enunciado]
En esta actividad, explorarás el patrón de diseño State. Analizarás cómo permite a un objeto alterar su comportamiento cuando su estado interno cambia, haciendo que el objeto parezca cambiar de clase. Identificarás su implementación en el caso de estudio y comprenderás cómo gestiona los diferentes comportamientos de las partículas.
:::

**Concepto del Patrón State**

Piensa en un reproductor de música. Puede estar en estado "Reproduciendo", "Pausado" o "Detenido". Las acciones (como presionar el botón "Play/Pause") tienen diferentes efectos dependiendo del estado actual. Si está "Reproduciendo" y presionas, pasa a "Pausado". Si está "Pausado" o "Detenido" y presionas, pasa a "Reproduciendo".

El patrón State permite a un objeto encapsular diferentes comportamientos (estados) en objetos separados y delegar la ejecución a su objeto de estado actual. Esto evita tener grandes bloques `if/else` o `switch` en la clase principal para manejar el comportamiento dependiente del estado.

Componentes clave:

*   **Context (contexto):** mantiene una instancia de una subclase de `State` que define el estado actual. Delega las solicitudes dependientes del estado al objeto de estado actual. Puede proporcionar un método para cambiar su estado. (En nuestro caso, `Particle`).
*   **State (estado):** define una interfaz común para todos los estados concretos. Esta interfaz declara los métodos que representan las operaciones dependientes del estado. (En nuestro caso, la clase base `State`).
*   **ConcreteState (estado concreto):** cada subclase implementa un comportamiento asociado con un estado del Contexto. (En nuestro caso, `NormalState`, `AttractState`, `RepelState`, `StopState`).

**Análisis del caso de estudio**

Examina el código (`ofApp.h`, `ofApp.cpp`) enfocándote en las clases relacionadas con el estado (`State`, `NormalState`, `AttractState`, etc.) y cómo interactúan con `Particle`.

1.  **Identifica los componentes:**
    *   ¿Cuál es la clase `Context`? ¿Qué miembro utiliza para mantener el estado actual?
    *   ¿Cuál es la interfaz `State`? ¿Qué métodos importantes define? (Piensa en `update`, `onEnter`, `onExit`).
    *   Enumera las clases `ConcreteState`. ¿Qué comportamiento específico encapsula cada una?

2.  **Delegación del comportamiento:**
    *   Observa el método `Particle::update()`. ¿Cómo delega la lógica de actualización al estado actual?
    *   Compara el código dentro de `NormalState::update()`, `AttractState::update()`, `RepelState::update()` y `StopState::update()`. ¿Cómo encapsula cada clase un comportamiento diferente?

3.  **Transiciones de estado:**
    *   ¿Cómo cambia una `Particle` de un estado a otro? ¿Qué método es responsable de gestionar la transición? (Busca `setState`).
    *   ¿Qué sucede dentro de `Particle::setState()`? ¿Por qué son importantes los métodos `onEnter` y `onExit` de la interfaz `State` (aunque no todos los estados concretos los usen extensivamente en este ejemplo)? ¿Qué gestionan `onEnter` y `onExit` en `NormalState`?
    *   ¿Qué evento externo (mediado por el patrón Observer, que ya analizaste) desencadena la llamada a `setState` en una `Particle`?

:::note[🧐🧪✍️ Reporta en tu bitácora]
1.  Explica con tus propias palabras el propósito del patrón State. ¿Cuándo es útil aplicarlo?
2.  Dibuja un diagrama de estados simple para la clase `Particle`. Muestra los diferentes estados (`Normal`, `Attract`, `Repel`, `Stop`) como nodos y las transiciones entre ellos como flechas etiquetadas con el evento que las causa (p. ej., la tecla presionada: 'n', 'a', 'r', 's').
3.  Describe las ventajas de usar el patrón State en `Particle` en lugar de tener un miembro `std::string estadoActual` y usar un gran `if/else if/else` o `switch` dentro de `Particle::update()` para cambiar el comportamiento. Piensa en cohesión, extensibilidad (añadir nuevos estados) y el Principio Abierto/Cerrado (Open/Closed Principle).
4.  ¿Qué responsabilidad tienen los métodos `onEnter` y `onExit` en el patrón State? Proporciona un ejemplo de por qué podrían ser útiles (incluso si no se usan mucho en *todos* los estados de este caso de estudio). Por ejemplo, ¿Qué podrías hacer en `onEnter` para `AttractState` o en `onExit` para `StopState`?
:::

:::caution[📤 Entrega]
Documenta tu análisis del patrón State, el diagrama de estados, la reflexión sobre sus ventajas y el rol de `onEnter`/`onExit` en tu bitácora de aprendizaje.
:::