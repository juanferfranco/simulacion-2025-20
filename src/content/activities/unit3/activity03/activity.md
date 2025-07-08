#### Mapa de memoria de un programa escrito en C++

Un programa en C++ (ya sea en Windows o en otros sistemas operativos) se organiza en diferentes secciones o segmentos de memoria. Aunque la implementación exacta puede variar según el compilador y el sistema operativo, la estructura general del mapa de memoria es similar. 

Piensa en el mapa de memoria como un gran arreglo donde cada sección tiene un propósito específico. 


``` bash
+-------------------------------+
|       Segmento de código      |
|   (instrucciones, funciones)  |
+-------------------------------+
| Variables globales y estáticas|
+-------------------------------+
|           Heap                | <--- Asignación dinámica (new/malloc)
|                               |
|                               |
+-------------------------------+
|           Stack               | <--- Variables locales
+-------------------------------+

``` 

A continuación, te describiré los segmentos de memoria más comunes en un programa C++:


1. Segmento de código (o Text). Es la zona de memoria donde se encuentra el código ejecutable del programa, es decir, las instrucciones compiladas. Se caracteriza porque es de solo lectura en muchos sistemas para prevenir modificaciones accidentales o maliciosas. Contiene todas las funciones, incluyendo main(), y cualquier otra función definida en el programa.

2. Variables globales y estáticas: aquí se almacenan las variables globales y estáticas que han sido inicializadas explícitamente y 
también las que no han sido inicializadas.

3. Heap: es el área de memoria utilizada para la asignación dinámica de memoria en tiempo de ejecución. Se gestiona manualmente mediante funciones como new y delete en C++.

4. Stack: es la región de memoria donde se almacenan las variables locales y la información de control de las funciones (como direcciones de retorno, parámetros y variables locales). La asignación y liberación de memoria en el stack se hace de manera automática al entrar y salir de las funciones. Su tamaño es limitado y, en caso de usar demasiada memoria local, puede 
producirse un stack overflow. Las variables definidas en el stack tienen un tiempo de vida limitado al alcance de la función 
o bloque en que se definen.

Ahora te mostraré un ejemplo donde trataré de ejemplificar el uso de cada uno de estos segmentos de memoria. 

```cpp
#include <iostream>
#include <cstdlib>

using namespace std;

// Variables globales
int global_inicializada = 42;      
int global_no_inicializada;        

// Constante global
const char* const mensaje_ro = "Hola, memoria de solo lectura";

// Función de ejemplo que muestra la dirección de su variable local estática
void funcionConStatic() {
    static int var_estatica = 100;
    cout << "Dirección de var_estatica (static): " << &var_estatica << endl;
}

// Función que asigna memoria dinámica (heap)
int* crearArrayHeap(int tam) {
    int* arr = new int[tam];
    for (int i = 0; i < tam; i++) {
        arr[i] = i;
    }
    return arr;
}

// Una función simple para representar el código (se encontrará en la región de código)
int suma(int a, int b) {
    int c = a + b; // "c" es una variable local (stack)
    return c;
}

int main() {
    // Variable local (stack)
    int a = 10;
    int b = 20;
    int c = suma(a, b);

    cout << "Resultado de suma(a, b): " << c << endl;
    cout << "Dirección de variable local 'a': " << &a << endl;
    cout << "Dirección de variable local 'b': " << &b << endl;
    cout << "Dirección de la variable local 'c' (resultado): " << &c << endl;

    // Variables globales
    cout << "Dirección de 'global_inicializada': " << &global_inicializada << endl;
    cout << "Dirección de 'global_no_inicializada': " << &global_no_inicializada << endl;

    // Constante global (solo lectura)
    cout << "Dirección de 'mensaje_ro' (zona de solo lectura): " << static_cast<const void*>(mensaje_ro) << endl;

    // Llamada a función que tiene variable estática
    funcionConStatic();

    // Uso del Heap: asignación dinámica
    int tamArray = 10;
    int* arrayHeap = crearArrayHeap(tamArray);
    cout << "Dirección del primer elemento del array asignado en Heap: " << arrayHeap << endl;
    for (int i = 0; i < tamArray; i++) {
        cout << "arrayHeap[" << i << "] = " << arrayHeap[i]
            << " en " << (arrayHeap + i) << endl;
    }
    delete[] arrayHeap; // Liberamos la memoria dinámica

    return 0;
}
```

**Enunciado**: ejecuta el programa con el depurador. No olvides colocar un breakpoint en la declaración y 
definición de la variable `a` en la función `main`. No termines el programa una vez llegues al final de la función `main`.
La idea es que compares la salida del programa con la información que te mostrará el depurador.

**Entrega**: captura de pantalla con el resultado de la ejecución del programa. Ahora trata de armar tu mismo el mapa de memoria 
del programa. Para ello, identifica las direcciones de memoria de las variables y constantes globales, locales, estáticas y de la memoria dinámica. Así como las direcciones de las funciones y el mensaje de solo lectura. Ten presente que los números están 
en hexadecimal. Cuando hagas el mapa de memoria, ubica las direcciones mayores en la parte superior y las menores en la parte inferior. Compara tu mapa de memoria con el que te mostré al inicio de la actividad.

