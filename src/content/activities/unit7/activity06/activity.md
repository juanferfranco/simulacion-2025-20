#### Aplica lo aprendido en esta unidad

:::note[🎯 Enunciado]
¡Es hora de poner en práctica lo aprendido! En la actividad anterior, hiciste que el triángulo respondiera a la posición del mouse. Ahora, vamos a dar un paso más allá y a introducir un elemento dinámico que no dependa directamente de tu interacción: el tiempo.

Tu misión es modificar el ejemplo del triángulo simple (puedes partir del resultado de la Actividad 05 o del original) para que su color cambie automáticamente a lo largo del tiempo, creando un efecto pulsante o cíclico.
:::

**Pistas:**

1.  **Necesitas obtener el tiempo:** la biblioteca GLFW proporciona una función muy útil para esto: `glfwGetTime()`. Esta función devuelve el tiempo en segundos (como un `double`) desde que se inicializó GLFW.
2.  **Pasar el tiempo al shader:** ¿Cómo puedes enviar información desde tu código C++ al shader, información que es la misma para todos los vértices y fragmentos en un **draw call** específico?
3.  **Modificar el color en el shader:** dentro del `main()` de tu *fragment shader*, ya no usarás un color fijo ni dependerás directamente del `uniform` del mouse (si partiste de la Actividad 05). Ahora, usarás el `uniform` del tiempo para calcular el color.
    *   **Idea:** Las funciones trigonométricas como `sin()` o `cos()` son excelentes para crear ciclos suaves. Por ejemplo, `(sin(time) + 1.0) / 2.0` produce un valor que oscila suavemente entre 0.0 y 1.0. Puedes usar esto para modular uno o más componentes (R, G, B) del color. ¡Experimenta!

:::note[🧐🧪✍️ Reporta en tu bitácora]

1.  Describe brevemente los cambios que realizaste en el código C++ (dónde obtienes el tiempo, cómo y dónde actualizas el uniform).
2.  Pega el código modificado de tu *fragment shader*.
3.  Explica cómo usaste la función de tiempo (`sin`, `cos`, u otra) para lograr el efecto de cambio de color cíclico. ¿Qué rango de valores produce tu cálculo y cómo afecta eso al color final?
4.  Incluye una captura de pantalla o **UN ENLACE** a un video mostrando el resultado del triángulo con color cambiante.
5.  **Reflexión:** ¿Qué otros efectos visuales simples podrías lograr usando el tiempo como `uniform`? Piensa en la posición, el tamaño o la rotación (aunque no hemos visto rotaciones formalmente, ¡intuitivamente podrías intentarlo!). Anota al menos una idea.
:::

:::caution[📤 Entrega]
Documenta tu proceso, el código modificado del shader, las explicaciones solicitadas y la captura en tu bitácora.
:::
