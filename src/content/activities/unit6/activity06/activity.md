#### Consolida tu aprendizaje: reflexiona sobre tu proyecto

:::note[🎯 Enunciado]
Esta actividad es crucial para consolidar lo que has aprendido y practicado. Ahora que has construido tu propio proyecto aplicando los patrones Observer, Factory Method y State, vas a analizar críticamente tu propio trabajo. El objetivo es articular claramente cómo y por qué utilizaste cada patrón en *tu* solución.

⚠️ **IMPORTANTE:** basa tus respuestas exclusivamente en el proyecto que **tú desarrollaste** en la Actividad 5. No uses asistentes de IA. Este es un ejercicio de **metacognición y elaboración** sobre tu propia experiencia.
:::

Reflexiona sobre el código que escribiste para tu proyecto de arte generativo (Actividad 5) y responde las siguientes preguntas:

1.  **Patrón Observer en tu proyecto:**
    *   ¿En qué parte específica de *tu código* implementaste el rol de Sujeto (Subject)? Muestra el fragmento relevante.
    *   ¿Qué clase(s) implementaron el rol de Observador (Observer)? Muestra el fragmento relevante de la implementación de la interfaz (ej. `onNotify`).
    *   ¿Qué eventos concretos notificaba tu Sujeto? ¿Por qué elegiste usar Observer para esta comunicación en lugar de llamadas directas?
    *   ¿En qué parte de tu código se *utiliza* el patrón (es decir, dónde se llama a `notify` y dónde reaccionan los observadores)?

2.  **Patrón Factory Method (o Simple Factory) en tu proyecto:**
    *   ¿Dónde está definido tu método factory? Muestra el fragmento de código.
    *   ¿Qué tipos específicos de objetos creaba tu factory?
    *   ¿Por qué fue beneficioso usar una factory en *esa parte* de tu código? ¿Qué problema te ayudó a resolver o qué mejora aportó a la estructura?
    *   Muestra un ejemplo de cómo *usaste* una factory en tu código cliente (ej. en `setup` o en respuesta a un evento).

3.  **Patrón State en tu proyecto:**
    *   ¿Qué clase actuó como Contexto (la que *tiene* un estado)? Muestra dónde guarda la referencia al estado actual.
    *   ¿Cuáles fueron los ConcreteStates que implementaste? Muestra un fragmento de la implementación de `update` (o el método principal de comportamiento) de al menos dos estados diferentes.
    *   ¿Qué desencadenaba las transiciones entre estados en tu aplicación? Muestra el código que llama a `setState` (o tu método equivalente).
    *   ¿Por qué decidiste que el patrón State era apropiado para gestionar el comportamiento de esa clase en particular? ¿Qué alternativa habrías considerado y por qué la descartaste?

4.  **Definiciones Post-Experiencia:**
    *   Después de haber trabajado intensamente con objetos, herencia, polimorfismo (implícito en State y Observer) y patrones: Define con *tus propias palabras* ¿qué es una **clase**?
    *   Define con *tus propias palabras* ¿qué es un **objeto**?

5.  **Beneficios Estructurales:**
    *   En general, ¿cómo crees que el uso de estos tres patrones (Observer, Factory, State) ayudó a organizar y estructurar el código de *tu proyecto*? ¿Lo hizo más fácil de entender, modificar o extender? ¿Por qué?

:::note[🧐🧪✍️ Reporta en tu bitácora]
Responde detalladamente a cada una de las preguntas anteriores, siempre refiriéndote a tu propio código y decisiones de diseño del proyecto de la Actividad 5. Incluye los fragmentos de código solicitados para ilustrar tus explicaciones.
:::

:::caution[📤 Entrega]
Las respuestas reflexivas y fundamentadas a todas las preguntas de esta actividad, documentadas en tu bitácora de aprendizaje.
:::