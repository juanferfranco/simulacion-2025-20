#### Consolida tu comprensión

:::note[🎯 Enunciado]
Has explorado diferentes facetas de la programación con hilos. En esta actividad te pido que sintetices lo aprendido, conectando los ejemplos y análisis para formar una comprensión coherente de cuándo y por qué usar hilos, y cuáles son sus principales desafíos.
:::

Escribe respuestas claras y concisas para cada pregunta, demostrando tu comprensión de los conceptos clave y tu capacidad para comparar los diferentes ejemplos.

:::note[🧐🧪✍️ Reporta en tu bitácora]

1. Beneficio - Responsividad: describe un escenario donde usarías un hilo principalmente para mejorar la **responsividad** de la aplicación. ¿Qué característica de la tarea que se ejecuta en el hilo hace que esto sea beneficioso? 
2. Beneficio - Paralelismo: describe un escenario donde usarías hilos principalmente para **acelerar el tiempo total de ejecución**. ¿Qué característica de la tarea hace que el paralelismo sea efectivo aquí?  
3. Desafío - Estado compartido: ¿Cuál es el principal **peligro** o desafío cuando múltiples hilos necesitan acceder y/o modificar los **mismos datos** (estado compartido)? ¿Cuál fue la solución básica que vimos para manejar esto?  
4. Comparación de casos: ¿Por qué el cálculo de Mandelbrot/Julia se pudo paralelizar eficientemente con muy poca necesidad de sincronización entre los hilos trabajadores, mientras que la simulación de Flocking requería `lock`/`unlock` frecuentes para acceder al estado compartido? ¿Cuál es la diferencia fundamental en cómo se accede y modifica los datos en ambos casos?
5. Conclusión personal: basado en tu experiencia en esta unidad, ¿Cuándo considerarías usar hilos en tus futuros proyectos? ¿Y qué precaución tendrías siempre en mente al hacerlo?
:::

:::caution[📤 Entrega]
Tus respuestas ea las preguntas de consolidación en tu bitácora.
:::
