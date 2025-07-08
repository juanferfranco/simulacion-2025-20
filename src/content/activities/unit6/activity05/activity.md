#### Aplica los Patrones: Tu Propio Arte Generativo

:::note[🎯 Enunciado]
Llegó el momento de aplicar lo aprendido. En esta actividad, diseñarás y desarrollarás **desde cero** un nuevo proyecto de arte generativo interactivo utilizando C++ y openFrameworks. El requisito fundamental es que apliques **correcta y significativamente** los tres patrones de diseño analizados: Observer, Factory Method y State. Además, deberás incorporar un enfoque de pruebas básicas durante el desarrollo.

⚠️ **IMPORTANTE:** el objetivo es crear algo **nuevo y diferente** al caso de estudio. No copies ni adaptes mínimamente el código proporcionado. Usa los patrones como herramientas para estructurar *tu propia* idea creativa. Te ruego que **no uses** asistentes de IA (como ChatGPT) para generar el código de la solución; el propósito es que tú consolides tu aprendizaje a través de la aplicación práctica y la resolución de problemas.
:::

**Requisitos del proyecto:**

1.  **Tecnología:** C++ y openFrameworks.
2.  **Patrones Obligatorios:**
    *   **Observer:** debe haber una relación clara Sujeto-Observador donde un evento en el Sujeto desencadene una acción en los Observadores. La interacción (teclado, mouse, tiempo, etc.) debe usar este patrón para comunicar cambios o eventos.
    *   **Factory Method (o Simple Factory):** debes usar una factory para crear diferentes tipos de objetos principales en tu sistema (pueden ser los elementos visuales, emisores, comportamientos, etc.). La creación debe estar encapsulada y desacoplada del cliente que los usa.
    *   **State:** al menos una clase principal de tu sistema debe cambiar su comportamiento basándose en un estado interno, utilizando el patrón State para encapsular esos comportamientos y gestionar las transiciones.
3.  **Interactividad:** la aplicación debe responder a alguna forma de entrada del usuario (teclado, mouse) o cambiar con el tiempo de manera observable.
4.  **Originalidad:** la idea visual y/o la mecánica de interacción deben ser distintas a las del caso de estudio. 
5.  **Testing (básico):** debes demostrar cómo verificaste, con el depurador o con pruebas simples (ej. ``cout <<`` ), que cada patrón funciona como esperas *durante el desarrollo*. Este paso te obligará a desarrollar la aplicación de manera incremental y a probar cada parte a medida que la implementas.

**Pasos sugeridos:**

1.  **Idea y diseño:**
    *   Piensa en una idea visual/interactiva simple pero interesante.
    *   Esboza cómo encajarían los tres patrones:
        *   ¿Qué objeto(s) cambiarán de comportamiento? (-> State)
        *   ¿Qué tipos diferentes de objetos necesitarás crear? (-> Factory)
        *   ¿Cómo se comunicarán los eventos o cambios globales a los objetos interesados? (-> Observer)
    *   Define las clases principales, sus responsabilidades y relaciones. Haz un diagrama simple si ayuda.

2.  **Implementación iterativa y pruebas:**
    *   Empieza por una parte central, quizás la clase que usará el patrón State. Implementa la interfaz State, un par de ConcreteStates y el Context. **Prueba:** ¿Puedes cambiar el estado? ¿El comportamiento en `update()` cambia correctamente? Usa `ofLogNotice` o `std::cout` para verificar.
    *   Implementa tu Factory. Haz que cree al menos dos tipos diferentes de objetos. **Prueba:** ¿Llama a la fábrica? ¿Obtienes los tipos correctos de objetos? ¿Están inicializados como esperas?
    *   Implementa Observer. Define tu Subject y Observer. Haz que el Subject notifique un evento simple. **Prueba:** ¿Se registran los observadores? ¿Reciben la notificación cuando llamas a `notify`?
    *   Integra los patrones. Haz que la interacción (ej. `keyPressed`) use `notify` (Observer), que `onNotify` en los observadores quizás cambie su estado (State) o use la Factory para crear algo nuevo.
    *   Refina la lógica de `update()`, `draw()` y la interacción.

3.  **Documentación del proceso:** mientras desarrollas, toma notas sobre cómo estás probando cada parte y las decisiones de diseño que tomas.

:::note[🧐🧪✍️ Reporta en tu bitácora]
**Durante y después del desarrollo de tu proyecto, documenta:**

1.  **Descripción del proyecto:** explica tu idea. ¿Qué hace tu aplicación de arte generativo? ¿Cómo interactúa el usuario? Incluye capturas de pantalla.
2.  **Diseño con patrones:**
    *   **Observer:** ¿Quién es tu Sujeto? ¿Quiénes son tus Observadores? ¿Qué eventos se notifican? ¿Cómo probaste que la notificación y reacción funcionan? Muestra un fragmento de código clave de la implementación y de la prueba.
    *   **Factory Method:** ¿Cuál es tu clase Factory y tu método factory? ¿Qué tipos de "productos" crea? ¿Por qué decidiste usar una factory aquí? ¿Cómo probaste que crea los objetos correctos? Muestra un fragmento de código clave.
    *   **State:** ¿Qué clase es tu Contexto? ¿Cuáles son tus Estados? ¿Qué comportamientos diferentes encapsulan? ¿Cómo se gestionan las transiciones? ¿Cómo probaste que los cambios de estado y comportamiento funcionan? Muestra un fragmento de código clave.
3.  **Integración:** ¿Cómo colaboran los tres patrones en tu aplicación final? Describe un escenario de interacción y cómo fluye la información/control a través de los patrones.
4.  **Desafíos y soluciones:** ¿Qué partes te resultaron más difíciles? ¿Cómo superaste esos desafíos?
:::

:::caution[📤 Entrega]
1.  El **código fuente completo** de tu proyecto openFrameworks.
2.  La **documentación detallada en tu bitácora** que cubra todos los puntos solicitados en la sección "Reporta en tu bitácora". Asegúrate de incluir la evidencia de tus pruebas (descripciones, logs, asserts, o fragmentos de código de verificación).
:::
