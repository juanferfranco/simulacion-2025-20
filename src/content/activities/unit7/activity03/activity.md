#### Análisis del ejemplo del triángulo simple parte 1

:::note[🎯 Enunciado]
En esta actividad, nos centraremos en el ejemplo más simple, el del triángulo. Tu estarás pensando que estoy loco 
cuando te digo que es el ejemplo simple, pero es así. Este es el hola mundo de OpenGL.
Vas a analizar el código del triángulo simple y vas a entender cómo funciona.
Vas a observar cómo se crea la ventana, cómo funciona el ciclo principal (game loop) y cómo se realiza el dibujo más básico.
No te distraigas, en la fase de aplicación te pediré que hagas algunas modificaciones, pero no ahora. Nuestro objetivo 
es entender cómo funciona el código y cómo se estructura, luego harás las modificaciones.
:::

**¿Qué es el contexto OpenGL?**

Cuando queremos dibujar gráficos con OpenGL, no basta con escribir llamadas a funciones de OpenGL. Esas funciones necesitan un entorno de ejecución que gestione todo lo relacionado con OpenGL en tu computador. Ese entorno se llama **contexto OpenGL**.

Un contexto OpenGL es una estructura de datos interna que contiene:

- El estado actual de OpenGL (colores, shaders, buffers, matrices, etc.).
- Los recursos que vas a usar (texturas, VBOs, VAOs, etc.).
- La conexión con la ventana donde se dibujarán los gráficos.
- La versión de OpenGL que estás usando (por ejemplo, 4.6 Core).

Es como si cada contexto fuera un **espacio de trabajo** de OpenGL.

¿Por qué es necesario? OpenGL no funciona por sí solo: necesita saber dónde dibujar y qué recursos están disponibles.
Esto se logra asociando un contexto OpenGL a una **ventana**, y asegurándote de que ese contexto esté activo en el hilo que va a dibujar (esto del hilo es un tema que no hemos visto, pero no te preocupes, no es necesario para esta unidad. Lo entenderás en la unidad que sigue. Por ahora piensa que **un hilo es el flujo de las instrucciones de tu programa**). En otras palabras, el contexto OpenGL es el intermediario entre tu código y la GPU.

¿Quién crea el contexto? OpenGL no crea contextos por sí solo. Tú necesitas una biblioteca que lo haga por ti.
En este caso vamos a usar **GLFW**. GLFW es una biblioteca que te permite crear ventanas y contextos OpenGL de manera sencilla. 
Cada sistema operativo tiene su propia forma de crear ventanas y contextos, y GLFW se encarga de abstraer esas diferencias para que tú no tengas que preocuparte por ellas. Con GLFW, puedes crear una ventana y un contexto OpenGL de manera portable y sencilla, sin importar si estás en Windows, Linux o MacOS.

Te propongo una analogía para entenderlo mejor: imagina que OpenGL es un artista que necesita un estudio (el contexto) para trabajar. GLFW es el arquitecto que construye ese estudio y le da las herramientas necesarias para crear su obra maestra (los gráficos). Sin el estudio, el artista no puede hacer nada.
GLFW se encarga de crear el contexto OpenGL y asociarlo a una ventana. Luego, tú puedes usar OpenGL para dibujar en esa ventana.

Observa la primera parte de la función `main` del ejemplo del triángulo simple:

```cpp
if (!glfwInit()) { ... }
```

Esta línea inicializa GLFW, la biblioteca que usaremos para crear la ventana y manejar eventos (como teclado, mouse o cambios de tamaño). Si la inicialización falla, se imprime un mensaje de error y el programa se termina.  
🔎 Importante: GLFW debe inicializarse antes de usar cualquiera de sus funciones.

```cpp
glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 6);
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
```

Estas líneas configuran el contexto OpenGL que queremos crear:

- Especificamos la versión 4.6 de OpenGL.
- Usamos el perfil Core, que excluye funciones obsoletas (como glBegin, glEnd).

¿Recuerdas qué es el contexto de OpenGL? **Cierra los ojos e intenta recuperar de memoria** la analogía del artista y el estudio.

Contexto OpenGL: es el entorno donde OpenGL guarda todo el estado gráfico (shaders, texturas, buffers, etc.).
Lo necesitas para que las funciones de OpenGL tengan efecto.

```cpp
GLFWwindow* mainWindow = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "Ventana", NULL, NULL);
``` 

Esta línea crea una ventana de 800x600 píxeles y le asocia un contexto OpenGL. Si la creación falla, se imprime un error y el programa se termina. Este contexto será el que usaremos para dibujar con OpenGL.

```cpp
int bufferWidth, bufferHeight;
glfwGetFramebufferSize(mainWindow, &bufferWidth, &bufferHeight);
```

Esta línea obtiene el tamaño del framebuffer de la ventana. **¿Qué es el framebuffer?** El framebuffer 
es una porción de memoria donde OpenGL dibuja los píxeles antes de enviarlos a la pantalla.
Podemos imaginarlo como una “hoja invisible” donde OpenGL pinta cada imagen cuadro a cuadro.

Este tamaño puede ser diferente al tamaño de la ventana en píxeles, especialmente en pantallas 
con escalado (como pantallas retina). ¿Por qué? En pantallas Hi‑DPI (Retina) cada “píxel lógico” de la ventana se representa con varios píxeles físicos; por ello el framebuffer, que usa los píxeles físicos reales, puede tener dimensiones mayores que las reportadas para la ventana.

Aquí te estarás preguntando, cuando se dice que OpenGL dibuja en el framebuffer, ¿Qué significa eso? 
¿No se supone que quien dibuja es la GPU? Entonces **¿Quién dibuja: la GPU o OpenGL?** La respuesta corta es:

> La GPU es quien realmente dibuja, y OpenGL es la API que le dice a la GPU qué y cómo dibujar.

Entonces repasemos un poco: 

**¿Qué es OpenGL?** OpenGL es una interfaz (API): un conjunto de funciones que tú como programador usas para 
enviar instrucciones a la GPU. OpenGL no dibuja directamente. En cambio, traduce tus comandos en operaciones que la GPU ejecuta.

**¿Y qué hace la GPU?** 

- Toma los datos de entrada que le pasas (vértices, texturas, shaders…). 
- Ejecuta los shaders: pequeños programas que definen cómo transformar esos datos en píxeles.
- Dibuja en el framebuffer, que es memoria de video (RAM de la GPU).

En otras palabras:

- Tú escribes código OpenGL en C++.
- OpenGL lo convierte en instrucciones que la GPU entiende.
- La GPU hace el trabajo pesado en paralelo, pintando los píxeles en el framebuffer.

**¿Por qué se dice entonces que “OpenGL dibuja”?** Porque es una simplificación útil cuando 
estás empezando. OpenGL es el lenguaje de control, pero el artista es la GPU. Decir "OpenGL dibuja 
en el framebuffer" es como decir que Photoshop hace el diseño: en realidad, lo haces tú usando 
la herramienta.

Como analogía final considera lo siguiente:

- Tú (el programador) -> Diseñas la escena, es decir, lo qué se va a dibujar y cómo.
- OpenGL -> El lenguaje que usas para dar instrucciones.
- GPU -> El artista que ejecuta todo el trabajo gráfico.
- Framebuffer -> La hoja donde el artista (GPU) pinta.
- Pantalla / La galería donde muestras el resultado final.

```cpp
glfwMakeContextCurrent(mainWindow);
```
Aquí hacemos que el contexto OpenGL asociado a mainWindow sea el contexto actual.
Esto es fundamental: cualquier función de OpenGL que llamemos a partir de ahora afectará a este contexto.

```cpp
glfwSetFramebufferSizeCallback(mainWindow, framebuffer_size_callback);
```

Esta línea registra una función de callback que se ejecutará cada vez que la ventana cambie de tamaño.

En este caso, la función framebuffer_size_callback hará lo siguiente:

```cpp	
glViewport(0, 0, width, height);
```

**¿Qué es el viewport?** El viewport define qué parte del framebuffer se usará para dibujar.
Se mide en píxeles y normalmente coincide con el tamaño completo del framebuffer.

Si el viewport no se ajusta correctamente al tamaño del framebuffer, lo que dibujas podría 
aparecer estirado, recortado o mal posicionado.

:::note[🧐🧪✍️ Reporta en tu bitácora]
Qué tal si ensayas. Prueba con esta línea 
```cpp
// 9) Configura el viewport
glViewport(0, 0, bufferWidth, bufferHeight);
```
¿Qué pasa si?

```cpp	
glViewport(0, bufferHeight/2, bufferWidth/2, bufferHeight/2);
```
Cambia los valores de bufferWidth y bufferHeight: divide por 2, por 4, multiplica por 2, por 4, etc.
¿Qué pasa? ¿Qué observas? ¿Qué crees que está pasando?
:::

## Resumen hasta aquí 
| Concepto        | ¿Qué es?                                               | ¿Por qué es importante?                                            |
|-----------------|--------------------------------------------------------|--------------------------------------------------------------------|
| **GLFW**        | Biblioteca para crear la ventana y manejar eventos.    | Nos evita programar código específico de cada sistema operativo.   |
| **Contexto OpenGL** | Entorno donde OpenGL guarda todo su estado.        | Sin él, no se pueden ejecutar funciones de OpenGL.                 |
| **Framebuffer** | Memoria donde OpenGL dibuja cada cuadro.               | Es lo que finalmente se muestra en pantalla.                       |
| **Viewport**    | Área del framebuffer donde se dibuja.                  | Define el espacio visible. Debe ajustarse al tamaño del framebuffer. |

:::note[🧐🧪✍️ Reporta en tu bitácora]
Ya llevas un rato leyendo y no has hecho **digestión**. Para favorecer tu aprendizaje, es necesario que 
te detengas un momento y **ELABORES** lo que has visto hasta ahora. Mira, quiero contarte algo. 
Lo que he venido haciendo de manera implícita en el curso es enseñarte a **aprender a aprender**. 
¿Te has dado cuenta? Te voy exponiendo a conceptos y luego te pido que experimentes, que los pongas en práctica, que los 
reflexiones. Entonces, no te quedes largos periodos de tiempo leyendo, viendo tutoriales o escuchando. Cada cierta cantidad 
de tiempo, puede ser cada 30 minutos o cada hora, haz una pausa y reflexiona sobre lo que has aprendido.

¿Cómo lo haces? Realiza un resumen de lo que has aprendido hasta ahora, haciendo un diagrama conceptual o un mapa mental. 
Experimentando. ¿Cómo? Haciendo la pregunta mágica: ¿Qué pasaría si? ¿Qué pasaría si cambio el tamaño de la ventana? ¿Qué pasaría si cambio el tamaño del viewport?

Entonces hagamos "digestión": en tu bitácora, escribe un resumen de lo que has aprendido hasta ahora y piensa en un experimento del tipo ¿Qué pasaría si?
:::

```cpp
gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)
```

Esta línea carga las funciones de OpenGL en el contexto actual. GLAD es una biblioteca que se encarga de cargar las funciones de OpenGL y hacerlas accesibles en tu programa. Sin esta línea, no podrías usar las funciones de OpenGL.

```cpp
glfwSwapInterval(1);
```
Esta línea activa la sincronización vertical (VSync), que limita la tasa de refresco de la ventana al mismo valor que la tasa de refresco del monitor. Esto evita el **tearing** y hace que el movimiento sea más suave.

```cpp
shaderProg = buildShaderProgram();
```

Esta línea llama a la función buildShaderProgram(), que compila y enlaza los shaders (vertex y fragment) que usaremos para dibujar el triángulo.
Esta función es fundamental porque los shaders son los programas que OpenGL ejecuta en la GPU para procesar los vértices y fragmentos (píxeles) de la escena. Sin shaders, OpenGL no sabe cómo dibujar nada.

```cpp	
setupTriangle();
```
Esta línea llama a la función setupTriangle(), que configura los datos del triángulo (posición de los vértices) y los carga en la GPU. Esta función es fundamental porque define cómo se verá el triángulo en pantalla.
Esta función es la que se encarga de crear el VBO y el VAO del triángulo (más sobre los shaders, VBOs y VAOs en un rato).

```cpp	
glViewport(0, 0, bufferWidth, bufferHeight);
```

Esta línea define el viewport, que es el área del framebuffer donde OpenGL dibujará. En este caso, se ajusta al tamaño del framebuffer de la ventana. Ya lo habíamos discutido antes, pero es importante que lo repases. Te dejo esta simulación 
para que entiendas mejor cómo funciona el viewport: [viewport simulation](https://editor.p5js.org/juanferfranco/sketches/-BE4VgV1h).

```cpp	
while (!glfwWindowShouldClose(mainWindow))
{
    // 11) Manejo de eventos
    glfwPollEvents();


    // 12) Procesa la entrada
    processInput(mainWindow);

    // 13) Configura el color de fondo y limpia el framebuffer
    glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);
    
    // 14) Indica a OpenGL que use el shader program
    glUseProgram(shaderProg);

    // 15) Activa el VAO y dibuja el triángulo
    glBindVertexArray(VAO);
    glDrawArrays(GL_TRIANGLES, 0, 3);

    // 16) Intercambia buffers y muestra el contenido
    glfwSwapBuffers(mainWindow);
}
```

Esta es la parte más importante del ciclo principal (game loop) de OpenGL. Aquí es donde ocurre la magia.
Vamos a desglosar cada línea:

```cpp
while (!glfwWindowShouldClose(mainWindow))
```

Este es el bucle principal del programa. Se ejecuta mientras la ventana no esté cerrada.
Dentro de este bucle, se procesan los eventos, se actualiza la lógica del programa y se dibuja la escena. 
La función glfwWindowShouldClose(mainWindow) devuelve true si el usuario ha cerrado la ventana (por ejemplo, haciendo clic en la X).

```cpp
glfwPollEvents();
```

Esta función procesa todos los eventos pendientes de la ventana. Esto incluye eventos de teclado, mouse y cambios de tamaño.
Es importante llamar a esta función en cada iteración del bucle principal para que la ventana responda a las entradas del usuario.

```cpp
processInput(mainWindow);
```

Esta función procesa la entrada del teclado. En este caso, se encarga de cerrar la ventana si el usuario presiona la tecla ESC.

```cpp
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
```

Esta línea establece el color de fondo de la ventana. El primer parámetro es el rojo, el segundo el verde, el tercero el azul y el cuarto la opacidad (alpha). En este caso, se establece un color azul claro.

```cpp
glClear(GL_COLOR_BUFFER_BIT);
```

Esta línea limpia el framebuffer, es decir, borra el contenido anterior y lo prepara para dibujar la nueva escena.

```cpp
glUseProgram(shaderProg);
```
Esta línea indica a OpenGL que use el shader program que hemos creado anteriormente (shaderProg).
Esto es fundamental porque los shaders son los que definen cómo se procesan los vértices y fragmentos (píxeles) de la escena. Sin esta línea, OpenGL no sabría qué shader usar para dibujar.

```cpp
glBindVertexArray(VAO);
```

Esta línea activa el Vertex Array Object (VAO) que hemos creado anteriormente. El VAO es un objeto que encapsula todos los estados de los buffers y atributos de vértices necesarios para dibujar el triángulo. Al activar el VAO, le decimos a OpenGL que use los datos de vértices que hemos configurado previamente.

```cpp
glDrawArrays(GL_TRIANGLES, 0, 3);
```
Esta línea le dice a OpenGL que dibuje el triángulo. GL_TRIANGLES indica que queremos dibujar un triángulo, 0 es el índice de inicio y 3 es el número de vértices a dibujar (un triángulo necesita tres vértices o puntos).

:::note[🧐🧪✍️ Reporta en tu bitácora]
¿Qué pasa si cambias el primer parámetro de glDrawArrays a GL_LINES? ¿Qué pasa si lo cambias a GL_POINTS? ¿Qué pasa si cambias el tercer parámetro a 2? ¿Qué pasa si lo cambias a 4?

En esta unidad no profundizaremos en los tipos de primitivas, pero es importante que entiendas que OpenGL puede dibujar diferentes tipos de primitivas (triángulos, líneas, puntos, etc.).
:::


```cpp
glfwSwapBuffers(mainWindow);
```

Esta línea muestra en pantalla el contenido del framebuffer que OpenGL acaba de renderizar.
Internamente, intercambia (por eso la palabra **Swap**) el buffer trasero (donde dibujas) con el buffer delantero (que se ve en pantalla), una técnica llamada **doble buffering** que evita parpadeos y asegura una imagen fluida.

```cpp	
// 17) Limpieza
glfwMakeContextCurrent(mainWindow);
glDeleteVertexArrays(1, &VAO);
glDeleteBuffers(1, &VBO);
glDeleteProgram(shaderProg);

glfwDestroyWindow(mainWindow);
glfwTerminate();
```

Esta parte del código se encarga de limpiar los recursos utilizados por OpenGL y GLFW antes de cerrar el programa. Es importante liberar la memoria y los recursos que ya no se necesitan para evitar fugas de memoria y otros problemas. Claro que en este caso 
el programa se termina inmediatamente, pero es una buena práctica hacerlo.


:::note[🧐🧪✍️ Reporta en tu bitácora]
Vamos a terminar esta actividad con un nuevo momento de consolidación parcial. Hay algunos conceptos 
relacionados con los shaders y el pipeline de OpenGL que no hemos visto en detalle, pero no te preocupes, 
los vamos a trabajar en la siguiente actividad. Por ahora, quiero que te concentres en lo que has aprendido hasta aquí.
Explica con tus propias palabras los siguientes conceptos. Puedes usar ejemplos, analogías o diagramas para 
ilustrar tus respuestas. Es importante que intentes responder estos conceptos sin ver inicialmente tus notas. Trata 
de ejercitar tu memoria y tu comprensión. Luego, puedes revisar tus notas para completar o corregir lo que hayas escrito.

1.  ¿Qué es el contexto OpenGL?
2. ¿Cuál es el rol de la biblioteca GLFW y qué ventaja tiene usarla?
3. ¿Por qué crees que OpenGL necesita un contexto (recuerda la analogía del taller de arte)?
4. ¿En últimas qué será el framebuffer y a qué te recuerda de las dos primeras unidades del curso?
5. ¿Qué relación entre en el viewport y el framebuffer?
6. ¿En todo la analizado hasta ahora qué rol juega los drivers de la GPU y la GPU misma?
7. ¿Por qué crees que sea necesario activar el VSync? ¿Si no lo activas y la imagen es estática qué crees que pase, y si es dinámica?
8. En esta unidad estamos usando OpenGL moderno, pero ¿Qué es OpenGL Legacy? ¿Qué diferencias hay entre ambos?
9. ¿Qué es el shader program? ¿Por qué es importante en OpenGL moderno?
10. Trata de revisar el código setupTriangle(), intuitivamente ¿Qué crees que hace? ¿Qué crees que es el VAO y el VBO?
11. En el ciclo principal (game loop) de OpenGL, notaste que en cada frame (cuadro) le decimos a openGL que use el shader program y el VAO. Si le indicas esto antes del game loop ¿Será necesario seguirlo haciendo en cada loop? Si no es necesario ¿En qué casos crees que esto puede ser útil?
12. Finalmente, recuerda lo que hace glfwSwapBuffers(mainWindow); ¿Por qué crees que es importante? ¿Qué pasaría si no lo llamas? ¿Cómo explicas lo que pasa si no lo llamas? (experimenta)
:::

:::caution[📤 Entrega]
Documenta libremente tu proceso en la bitácora y reporta los dos momentos de "digestión" que hemos tenido en esta actividad.
:::
