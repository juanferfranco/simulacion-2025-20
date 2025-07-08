#### Triángulo interactivo

:::note[🎯 Enunciado]
En esta actividad vas a modificar el ejemplo del triángulo simple para que sea interactivo.
La idea es que puedas cambiar el color del triángulo y su posición en la pantalla pasando 
información desde el código C++ a los shaders. 
:::

Lo que vamos a hacer es modificar el código C++ para que pase información a los shaders de modo 
que el triángulo cambie de color y posición dependiendo de la posición del mouse.
Vamos a usar un `uniform` para pasar la posición del mouse y otro `uniform` para el color.

**¿Qué es un uniform?**
Un `uniform` es una variable global de solo lectura dentro del shader, que puede ser establecida 
desde el código C++ antes del draw call y permanece constante durante la ejecución de un draw call. 

A diferencia de los atributos de vértices, que son específicos para cada vértice, los `uniforms` 
son globales y son **únicos para cada shader object**, pero pueden ser compartidos entre diferentes 
shaders en el mismo programa, es decir, pueden ser usados en diferentes etapas del pipeline de OpenGL.

Vamos a modificar los vertex y fragment shaders para que acepten estos `uniforms` y los utilicen para cambiar el color 
y la posición del triángulo:

```glsl
#version 460 core

layout(location = 0) in vec3 aPos;
uniform vec2 offset;

void main() {
    vec3 newPos = aPos;
    newPos.x += offset.x;
    newPos.y += offset.y;
    gl_Position = vec4(newPos, 1.0);
}
```

```glsl
#version 460 core

out vec4 FragColor;
uniform vec4 ourColor;

void main() {
    FragColor = ourColor;
}
```
En el vertex shader, estamos usando un `uniform` llamado `offset` para modificar la posición del triángulo.
En el fragment shader, estamos usando un `uniform` llamado `ourColor` para modificar el color del triángulo.

Ahora vamos a modificar el código C++ para que pase estos `uniforms` a los shaders. Justo antes del loop 
de renderizado, vamos a obtener la ubicación de los `uniforms` y pasarlos al shader:

```cpp
glUseProgram(shaderProg);
int offsetLocation = glGetUniformLocation(shaderProg, "offset");
int colorLocation = glGetUniformLocation(shaderProg, "ourColor");
```
Luego, dentro del loop de renderizado, vamos a actualizar los `uniforms` con la posición del mouse y el color:

```cpp
// Dibuja el triángulo
double xpos, ypos;
glfwGetCursorPos(mainWindow, &xpos, &ypos);

// Normalizo las coordenadas del mouse
float x = (float)xpos / (float)SCR_WIDTH;
x < 0 ? x = 0 : x;
x > 1 ? x = 1 : x;

float y = (float)ypos / (float)SCR_HEIGHT;
y < 0 ? y = 0 : y;
y > 1 ? y = 1 : y;

// Envio el color y la posición del triángulo
float color[] = { x, y, 0.0f, 1.0f };
glUniform4f(colorLocation, x, y, 0.0f, 1.0f);

// Envio el offset del triángulo normalizado a NDC
glUniform2f(offsetLocation, x*2 - 1, 1 - y*2);


glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3);

// Intercambia buffers y muestra el contenido
glfwSwapBuffers(mainWindow);

```
En este código, estamos obteniendo la posición del mouse y normalizándola para que esté entre 0 y 1.
Luego, estamos enviando el color y la posición del triángulo al shader usando los `uniforms` que definimos
anteriormente.
Finalmente, estamos dibujando el triángulo y actualizando la pantalla.

:::note[🧐🧪✍️ Reporta en tu bitácora]
1. Modifica el código del triángulo para que sea interactivo.
2. Incluye una captura de pantalla del triángulo interactivo funcionando en tu máquina.
3. Explica el proceso de normalización de las coordenadas del mouse y cómo se relaciona con el sistema de coordenadas de OpenGL.
4. Explica el proceso de normalización a coordenadas de dispositivo (NDC) y cómo se relaciona con el sistema de coordenadas de OpenGL.
:::

:::caution[📤 Entrega]
Captura de pantalla del triángulo interactivo funcionando y la documentación solicitada en tu bitácora.
:::

