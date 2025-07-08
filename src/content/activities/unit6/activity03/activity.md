#### Investiga el Patrón Factory Method

:::note[🎯 Enunciado]
Esta actividad se centra en el patrón de diseño Factory Method. Investigarás su propósito, cómo abstrae el proceso de creación de objetos y cómo se utiliza en el caso de estudio para generar diferentes tipos de partículas.
:::

**Concepto del patrón Factory Method**

Imagina que estás construyendo una aplicación de logística que necesita crear diferentes tipos de transporte (camiones, barcos, aviones). En lugar de tener código de creación (`new Camion()`, `new Barco()`) esparcido por toda tu aplicación, el patrón Factory Method propone definir una interfaz (o un método en una clase base/existente) para crear objetos, pero deja que sean las subclases (o la implementación concreta del método) quienes decidan qué clase específica instanciar.

El Factory Method es un patrón creacional que proporciona una interfaz para crear objetos en una superclase, mientras permite a las subclases alterar el tipo de objetos que se crearán. También se puede implementar como un método estático simple en una clase (a veces llamado "Simple Factory" o "Static Factory Method", que es lo que vemos en el caso de estudio).

Propósito principal:

*   **Desacoplar la creación de objetos:** el código cliente que necesita un objeto no necesita saber cómo crearlo ni qué clase concreta se está creando. Solo interactúa con la interfaz o clase base del producto.
*   **Flexibilidad:** facilita la introducción de nuevos tipos de productos sin modificar el código cliente que utiliza la fábrica.

**Análisis del caso de estudio**

Revisa nuevamente el código, prestando atención a la clase `ParticleFactory` y su uso en `ofApp::setup`.

1.  **Identifica la Factory:**
    *   ¿Qué clase actúa como la factory en este ejemplo?
    *   ¿Cuál es el "método factory" específico? ¿Es un método de instancia o estático?
    *   ¿Qué tipo de objeto devuelve este método fábrica?

2.  **Proceso de creación:**
    *   Observa el método `ParticleFactory::createParticle`. ¿Cómo decide qué tipo de partícula específica crear y configurar?
    *   ¿Qué información necesita el método fábrica para realizar su trabajo?
    *   ¿Qué devuelve si se le pasa un tipo desconocido? ¿Cómo podrías mejorar esto?

3.  **Uso de Factory:**
    *   Localiza `ofApp::setup`. ¿Cómo se utiliza la `ParticleFactory` para poblar el vector `particles`?
    *   Compara esto con la alternativa: ¿Cómo se vería `ofApp::setup` si *no* usara la fábrica y tuviera que crear y configurar cada tipo de partícula (`star`, `shooting_star`, `planet`) directamente usando `new Particle()` y luego ajustando sus propiedades (`size`, `color`, `velocity`)?

:::note[🧐🧪✍️ Reporta en tu bitácora]
1.  Explica con tus propias palabras el propósito del patrón Factory Method (o Simple Factory, en este caso). ¿Qué problema principal aborda en la creación de objetos?
2.  ¿Qué ventajas aporta el uso de `ParticleFactory` en `ofApp::setup` en comparación con instanciar y configurar las partículas directamente allí? Piensa en términos de organización del código (SRP - Single Responsibility Principle), legibilidad y facilidad para añadir *nuevos* tipos de partículas en el futuro.
3.  Imagina que quieres añadir un nuevo tipo de partícula llamada `"black_hole"` que tiene tamaño grande, color negro y velocidad muy lenta. Describe los pasos que necesitarías seguir para implementar esto utilizando la `ParticleFactory` existente. ¿Tendrías que modificar `ofApp::setup`? ¿Por qué sí o por qué no?
4.  El método `createParticle` en el ejemplo es estático. ¿Qué implicaciones (ventajas/desventajas) tiene esto comparado con tener una instancia de `ParticleFactory` y un método de instancia `createParticle()`?.
:::

:::caution[📤 Entrega]
Documenta tu análisis del patrón Factory Method, sus ventajas en el caso de estudio, los pasos para añadir un nuevo tipo de partícula y tu reflexión sobre métodos estáticos vs. de instancia en tu bitácora.
:::
