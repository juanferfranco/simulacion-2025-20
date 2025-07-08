#### Análisis del ejemplo del triángulo simple parte 2

:::note[🎯 Enunciado]
En la actividad anterior dejamos de lado algunos conceptos fundamentales de OpenGL. En esta actividad vamos a analizar el código del ejemplo del triángulo simple y a entender cómo funciona el pipeline de OpenGL. Vamos a ver cómo se comunican los shaders con el código C++ y cómo se envían los datos a la GPU.
:::

**¿Cuál es la diferencia entre una CPU y una GPU** para responder esta pregunta te pediré que veas el siguiente 
video de NVIDIA. No te asustes, es muy entretenido. [Mythbusters Demo GPU versus CPU](https://youtu.be/-P28LKWTzrI?si=fJJAHQJp44OLiKJk).

:::note[🧐✍️ Reporta en tu bitácora]
Luego de estudiar las unidades 1 y 2 de este curso y ver el video, escribe con tus propias palabras ¿Cuál es la diferencia 
entre una CPU y una GPU? 
:::

**¿Cómo funcionan las gráficas en un computador?** De nuevo te voy a proponer que veas un video hermoso. La animación 
es increíble y la explicación es muy clara. [How Graphics Work](https://youtu.be/C8YtdC8mxTU?si=t41oXWfbK2Q3TSzf), eso si, 
reconozco que es mucha información. Si te hace falta, puedes pausar el video y volver a ver partes que no entendiste.

:::note[🧐✍️ Reporta en tu bitácora]
Es momento de practicar la técnica de **aprender a aprender** que te he venido mostrando de manera insistente a lo largo 
del curso. Te voy a proponer una serie de preguntas para que reflexiones y escribas en tu bitácora. Trata de responder 
de memoria a cada pregunta. No busques la respuesta en el video. Trata de recordar lo que viste. De todas maneras 
si no lo logras hacer, regresa al video y busca la respuesta.

1.  ¿Cuáles son los tres pasos claves del pipeline de OpenGL? Explica en tus propias palabras cuál es el objetivo 
de cada paso.
2. La gran novedad que introduce OpenGL moderno es el pipeline programable. ¿Qué significa esto? ¿Qué
diferencia hay entre el pipeline fijo y el programable? ¿Qué ventajas le ves a esto? y si el pipeline es programable, ¿Qué tengo 
que programar?
3. Si fueras a describir el proceso de **rasterización** ¿Qué dirías?
4. ¿Qué son los fragmentos? ¿Es lo mismo un fragmento que un pixel? ¿Por qué?
5. Explica qué problema resuelve el **Z-buffer** y ¿Qué es el depth test?
6. ¿Por qué se presenta el problema de la **aliasing**? ¿Qué es el anti-aliasing?
7. ¿Qué relación hay entre la iluminación y el fragment shader? Siempre es necesario tener en cuenta la iluminación 
en un fragment shader? o puedo hacer un fragment shader sin iluminación? Explica que implicaciones tiene esto.
8. ¿Qué implica para la GPU que una aplicación tenga múltiples fuentes de iluminación?
:::

Ahora que ya conoces cómo funciona el pipeline de OpenGL, vamos a analizar las partes del código del ejemplo del triángulo simple 
que dejamos pendientes en la actividad anterior. 

```cpp	
// 7) Compila y linkea shaders
shaderProg = buildShaderProgram();

// 8) Genera el contenido a mostrar
setupTriangle();
```

Antes de abordar a fondo estas líneas de código, es importante que analicemos el concepto de OBJETOS en OpenGL.
En OpenGL, los objetos son entidades que representan recursos gráficos. Estos recursos pueden incluir texturas, buffers de vértices, shaders y otros elementos necesarios para renderizar gráficos en la GPU. Cada objeto tiene un identificador único (ID) que se utiliza para referenciarlo en las llamadas a funciones de OpenGL.
Los objetos en OpenGL son gestionados por la GPU y permiten optimizar el rendimiento al reducir la cantidad de datos que deben ser transferidos entre la CPU y la GPU. Al crear un objeto, la asignación de un ID único permite a la GPU acceder rápidamente a los recursos necesarios para renderizar gráficos, de esa manera no es necesario enviar los datos de los objetos cada vez que se quiere renderizar una escena. En su lugar, OpenGL utiliza el ID del objeto para acceder a los datos almacenados en la GPU.

En este punto ya hemos analizado un gran objeto de OpenGL denominado el **contexto de OpenGL**. Este objeto es el que permite a OpenGL comunicarse con la GPU y gestionar los recursos gráficos. Dentro de este contexto, OpenGL crea y gestiona otros objetos como los buffers de vértices, los shaders y las texturas. Cada uno de estos objetos tiene su propio ID único que se utiliza para referenciarlo en las llamadas a funciones de OpenGL.

La estructura general de creación y uso de un objeto OpenGL es la siguiente:

```cpp
// Crea la variable que contendrá el ID del objeto
unsigned int objectId = 0;
// Genera el objeto y asigna un ID único
glGenObject(1, &objectId);
// Asocia el objeto a un destino específico dentro del contexto de OpenGL.
glBindObject(GL_WINDOW_TARGET, objectId);
// Establece opciones para el objeto
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_WIDTH,  800);
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_HEIGHT, 600);
// Hace una desactivación del objeto o un UNBINDING
glBindObject(GL_WINDOW_TARGET, 0);
``` 

Si luego se quiere usar el objeto, se hace un **binding** del objeto y todos los comandos que se envían a OpenGL 
se aplican a ese objeto. Por ejemplo, si se quiere usar un shader, se hace un binding del shader y todos los comandos que se envían a OpenGL se aplican a ese shader. Esto permite a OpenGL gestionar múltiples objetos de manera eficiente y optimizar el rendimiento al reducir la cantidad de datos que deben ser transferidos entre la CPU y la GPU.

Vamos a repasar algunas de las etapas del pipeline de OpenGL y cómo se relacionan con el código que hemos visto hasta ahora. Para 
ello vamos a tomar como referencia esta imagen tomada del curso [learnopengl.com](https://learnopengl.com/Getting-started/Hello-Triangle):

![render pipeline](../../../../assets/pipeline.png)

Observa en la gráfica que lo primero que se recibe son los datos de los vértices. Estos datos son
enviados a la GPU y se almacenan en un buffer de vértices (VBO). Este buffer es un objeto OpenGL que contiene los datos de los vértices y se utiliza para enviar estos datos a la GPU. En el código del ejemplo del triángulo simple, esto se hace en la función `setupTriangle()`.

```cpp
void setupTriangle() {
	float vertices[] = {
		-0.5f, -0.5f, 0.0f,
		 0.5f, -0.5f, 0.0f,
		 0.0f,  0.5f, 0.0f
	};

	glGenVertexArrays(1, &VAO);
	glGenBuffers(1, &VBO);

	glBindVertexArray(VAO);
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);
	glBindVertexArray(0);
}
```

Nota que lo primero que hacemos es definir los vértices del triángulo. Luego, creamos un **objeto VAO** (Vertex Array Object) y un **VBO** (Vertex Buffer Object). El objeto VAO es un objeto OpenGL que contiene la configuración de los atributos de los vértices y el objeto VBO es un objeto OpenGL que contiene los datos de los vértices. Fíjate que luego de crear los objetos para obtener el ID, hacemos un binding del VAO y del VBO. Esto significa que todos los comandos que se envían a OpenGL se aplican a estos objetos. Luego, enviamos los datos de los vértices al buffer de vértices (VBO) y configuramos los atributos de los vértices. Finalmente, hacemos un UNBINDING del VAO.

```cpp
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

Esta línea de código envía los datos de los vértices al buffer de vértices (VBO). El primer parámetro es el tipo de buffer, el segundo es el tamaño de los datos, el tercero son los datos (un puntero al primer elemento del arreglo) y el cuarto es la forma en que se van a usar los datos. En este caso, estamos usando `GL_STATIC_DRAW` porque los datos no van a cambiar. Si los datos de 
los vértices cambian, se puede usar `GL_DYNAMIC_DRAW` por ejemplo.

```cpp
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
```

Esta línea de código configura los atributos de los vértices. El primer parámetro es el índice del atributo (en este caso 0), el segundo es el número de componentes por vértice (en este caso 3, porque cada vértice tiene 3 coordenadas), el tercero es el tipo de dato (en este caso `GL_FLOAT`), el cuarto es si los datos están normalizados o no (en este caso `GL_FALSE`), el quinto es el tamaño del paso entre los atributos (en este caso 3 * sizeof(float)) y el sexto es un puntero al primer elemento del arreglo.

Te mostraré todo esto con un diagrama para que lo entiendas mejor:

![VBO con un atributo](../../../../assets/VBO-1Attr.webp)

Cada uno de los parámetros de la función `glVertexAttribPointer` se puede entender de la siguiente manera:

```cpp
glVertexAttribPointer(
    GLuint index,         // Atributo del shader (layout(location = index))
    GLint size,           // Componentes por vértice (1–4)
    GLenum type,          // Tipo de dato (GL_FLOAT, GL_INT, etc.)
    GLboolean normalized, // ¿Normalizar datos enteros?
    GLsizei stride,       // Espaciado (en bytes) entre vértices
    const void* pointer   // Desplazamiento inicial dentro del VBO
);
```

```cpp
glEnableVertexAttribArray(0);
```

Esta línea de código habilita el atributo de vértice. El parámetro es el índice del atributo (en este caso 0). 
Te estarás preguntado ¿Qué es eso de los atributos? **¿Qué es eso de los vertex attributes?**
Los atributos de vértice son propiedades que describen cada vértice en un buffer de vértices. Estos atributos pueden incluir información como la posición, el color, las coordenadas de textura y las normales. Cada atributo tiene un índice único que se utiliza para referenciarlo en el shader. En el ejemplo del triángulo simple, solo estamos usando la **posición** del vértice como atributo. Sin embargo, en aplicaciones más complejas, puedes tener múltiples atributos por vértice. Te prometo que voy 
a retomar esto en un momento y lo ampliaremos un poco más.

```cpp
glBindVertexArray(0);
```

Finalmente, hacemos un UNBINDING del VAO. Esto significa que todos los comandos que se envían a OpenGL no se aplican a este objeto. Esto es importante porque si no hacemos un UNBINDING, todos los comandos que se envían a OpenGL se aplican a este objeto y esto puede causar problemas.

Ahora, observa de nuevo las posiciones de los vértices:

```cpp
float vertices[] = {
	-0.5f, -0.5f, 0.0f,
	 0.5f, -0.5f, 0.0f,
	 0.0f,  0.5f, 0.0f
};
```

No te has preguntado ¿Cómo hago para definir los vértices? ¿Qué significa cada número? ¿Por qué son esos números?
En OpenGL, los vértices se definen en un espacio de **coordenadas de dispositivo normalizadas** (NDC). Esto significa que las coordenadas van de -1 a 1. En este caso, el primer vértice está en la esquina inferior izquierda (-0.5, -0.5), el segundo vértice está en la esquina inferior derecha (0.5, -0.5) y el tercer vértice está en la parte superior (0, 0.5). Te lo aclaro con 
una figura:

![NDC](../../../../assets/triangle-ndc.webp)

Ya con esta información, volvamos ahora a la figura del pipeline de OpenGL. Ya te hablé entonces de los datos que va a recibir la GPU (Vertex Data[] en la figura). Ahora te hablaré de los shaders. En OpenGL moderno es obligatorio usar shaders. Debes crear al menos un shader para poder usar OpenGL. En el ejemplo del triángulo simple, estamos usando un shader de vértices y un shader de fragmentos. Estos shaders son programas que se ejecutan en la GPU y se utilizan para procesar los datos de los vértices y los fragmentos. En el código del ejemplo del triángulo simple, la **creación del objeto que contendrá los shaders** se hace en la función `buildShaderProgram()`. Este objeto tiene un ID único que se utiliza para referenciarlo en las llamadas a funciones de OpenGL. Sin embargo, el shader no se ejecuta hasta que se hace un binding de este (lo activo). En el ejemplo del triángulo simple, esto se hace en la función `glUseProgram(shaderProg);`. De esta manera al llamar `glDrawArrays(GL_TRIANGLES, 0, 3);` se le dice a OpenGL que use el shader que hemos activado (bind) y que dibuje los vértices que hemos definido. 

Ahora, al observar el código de la función `buildShaderProgram()` verás que primero creamos un shader de vértices, lo compilamos y 
verificamos si hubo errores. Luego hacemos lo mismo con el shader de fragmentos. Finalmente, creamos un **objeto programa** y le 
asociamos los shaders. Luego, enlazamos (linkeamos) el programa y verificamos si hubo errores. Si todo sale bien, eliminamos los shaders porque ya no los necesitamos, pero no el objeto programa. El objeto programa es el que se usa para ejecutar los shaders en la GPU. **¿Cuál es el código de los shaders?**

**Vertex shader:**

``` glsl
#version 460 core
layout(location = 0) in vec3 aPos;
void main() {
	gl_Position = vec4(aPos, 1.0);
}
```

**Fragment shader:**

``` glsl
#version 460 core
out vec4 FragColor;
void main() {
	FragColor = vec4(1.0, 0.5, 0.2, 1.0);
}
```

Analicemos el código del vertex shader. Este shader se ejecuta en la GPU y recibe los datos de los vértices. En este caso, estamos usando un solo atributo de vértice (la posición). El shader toma la posición del vértice y la convierte en un vector de cuatro componentes (x, y, z, w). El componente w se establece en 1.0 porque estamos trabajando en un espacio de coordenadas homogéneo. Luego, el shader asigna este vector a la variable `gl_Position`, que es una variable predefinida en OpenGL que representa la posición del vértice en el espacio de clip. El espacio de clip (clip space) es el espacio de coordenadas justo antes de que OpenGL haga el clipping y la proyección en pantalla. En otras palabras: es el espacio donde deben estar las posiciones de los vértices antes de ser recortados por el frustum de visión (el volumen visible de la cámara) y antes de transformarse en coordenadas de pantalla (viewport).

Miremos ahora en detalle cada línea de código del vertex shader:

``` glsl
#version 460 core
```

Esta línea indica la versión del lenguaje GLSL (OpenGL Shading Language) que estamos utilizando. En este caso, estamos usando la versión 460 core, que es una de las versiones más recientes y estables.

``` glsl
layout(location = 0) in vec3 aPos;
```

Esta línea define un atributo de entrada llamado `aPos` que representa la posición del vértice. La palabra clave `layout(location = 0)` indica que este atributo se asigna a la ubicación 0 en el buffer de atributos. Esto es importante porque OpenGL utiliza estas ubicaciones para vincular los atributos de los vértices con los datos en el buffer de vértices.

``` glsl
void main() {
```
Esta línea define la función principal del shader. Esta función se ejecuta para cada vértice que se procesa en el pipeline de OpenGL.

``` glsl
gl_Position = vec4(aPos, 1.0);
```

Esta línea asigna la posición del vértice a la variable predefinida `gl_Position`. La función `vec4(aPos, 1.0)` convierte el vector de tres componentes `aPos` en un vector de cuatro componentes, donde el cuarto componente (w) se establece en 1.0. Esto es necesario porque OpenGL trabaja con coordenadas homogéneas y necesita un vector de cuatro componentes para representar la posición del vértice en el espacio de clip.

El shader de fragmentos se ejecuta después del vertex shader y recibe los datos de los fragmentos. En este caso, estamos asignando un color fijo (naranja) a la variable `FragColor`, que es una variable de salida predefinida en OpenGL que representa el color del fragmento. Este color se utiliza para dibujar el triángulo en la pantalla.

``` glsl
#version 460 core
out vec4 FragColor;
```

Esta línea indica la versión del lenguaje GLSL (OpenGL Shading Language) que estamos utilizando. La variable `FragColor` es una variable de salida que representa el color del fragmento.

``` glsl
void main() {
```
Esta línea define la función principal del shader. Esta función se ejecuta para cada fragmento que se procesa en el pipeline de OpenGL.

``` glsl
FragColor = vec4(1.0, 0.5, 0.2, 1.0);
```
Esta línea asigna un color fijo (naranja) a la variable `FragColor`. La función `vec4(1.0, 0.5, 0.2, 1.0)` crea un vector de cuatro componentes que representa el color en formato RGBA (rojo, verde, azul y alfa). En este caso, el color es un naranja claro con un valor alfa de 1.0 (completamente opaco).

:::note[🧐✍️ Reporta en tu bitácora]
Es momento de hacer digestión cognitiva. Debemos parar de nuevo en este punto y consolidar. Para ello te pediré 
que hagas lo siguiente:
1.  Escribe un resumen en tus propias palabras de lo que se necesita para dibujar un triángulo en OpenGL.
2.  Escribe un resumen en tus propias palabras de lo que necesitas para poder usar un shader en OpenGL.
:::

Volvamos al asunto del ``glVertexAttribPointer`` ¿Recuerdas? Te prometí que lo retomaríamos. Pero ahora que 
ya sabes un poco más de OpenGL, te voy a proponer algo más. Supón que vas a definir un VBO con tres atributos y 
la idea es usar un shader diferente en cada draw call. Por ejemplo, el primer shader va a usar la posición, el 
segundo shader va a usar el color y el tercer shader va a usar el offset. Es decir, en cada shader vas a usar un 
atributo diferente. En este caso, el VBO tendría tres atributos:

1.  Posición (x, y, z)
2.  Color (r, g, b)
3.  Offset (u, v)

Estos serán los vértices:

```cpp
float vertices[] = {
	//  pos         color         offset
	-1.0f, -1.0f, 0.0f,   0.0f, 0.0f, 0.0f,   0.1f, 0.5f,
		0.0f, -1.0f, 0.0f,   1.0f, 0.0f, 0.0f,   0.2f, 0.5f,
		-0.5f,  -0.5f, 0.0f,   0.5f, 0.5f, 0.0f,   0.15f, 0.75f,
};
```

Estos serían los 3 shaders:

Shader A:

``` glsl
layout(location = 0) in vec3 aPos;

void main() {
    gl_Position = vec4(aPos, 1.0);
}

```

Shader B:

``` glsl
layout(location = 1) in vec3 aColor;

void main() {
    gl_Position = vec4(aColor * 0.5, 1.0); // usar color como posición "falsa"
}

```
Shader C:

``` glsl
layout(location = 2) in vec2 aOffset;

void main() {
    gl_Position = vec4(aOffset, 0.0, 1.0);
}
```

Ahora, al momento de hacer la configuración del VAO y el VBO harías esto:

```cpp
GLuint VAO, VBO;
glGenVertexArrays(1, &VAO);
glGenBuffers(1, &VBO);

glBindVertexArray(VAO);
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// Atributo 0: posición
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(0));
glEnableVertexAttribArray(0);

// Atributo 1: color
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);

// Atributo 2: offset
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(6 * sizeof(float)));
glEnableVertexAttribArray(2);

glBindVertexArray(0);
```

De nuevo, gráficamente:

![VBO con 3 atributos](../../../../assets/VBO-3Attrs.webp)

Ya al memonto de hacer el render harías algo como esto:

```cpp
glBindVertexArray(VAO);

// 1. Usar solo el atributo 0 (posición)
glUseProgram(shaderA);
glEnableVertexAttribArray(0);
glDisableVertexAttribArray(1);
glDisableVertexAttribArray(2);
glDrawArrays(GL_TRIANGLES, 0, 3);

// 2. Usar solo el atributo 1 (color)
glUseProgram(shaderB);
glDisableVertexAttribArray(0);
glEnableVertexAttribArray(1);
glDisableVertexAttribArray(2);
glDrawArrays(GL_TRIANGLES, 0, 3);

// 3. Usar solo el atributo 2 (offset)
glUseProgram(shaderC);
glDisableVertexAttribArray(0);
glDisableVertexAttribArray(1);
glEnableVertexAttribArray(2);
glDrawArrays(GL_TRIANGLES, 0, 3);
```

¿Pudiste notar entonces cómo se usa glEnableVertexAttribArray? Observa que en cada draw call habilitamos solo el atributo que vamos a usar. 

:::note[🧐🧪✍️ Reporta en tu bitácora]
Implementa el código anterior en tu máquina y captura pantalla del resultado. Pero antes de hacerlo 
trata de predecir qué va a pasar.
:::

:::caution[📤 Entrega]
Reporta en tu bitácora lo que te pedí a lo largo de la actividad. 
:::
