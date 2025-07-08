#### Hola Objeto: creación de un objeto en el stack

Este experimento es fundamental porque conecta el concepto fundamental de POO (objetos) con este curso. Tómate el tiempo necesario para entenderlo y realizarlo. NO OLVIDES usar el depurador, es la clave para entender cómo funciona la memoria en C++.

Encontrarás preguntas en el enunciado y en la entrega. Ve respondiendo las preguntas a medida que avanzas en el experimento.

**MUY IMPORTANTE**: llama al profesor para que revise tus respuestas antes de continuar con la siguiente actividad. La idea 
es que conversemos sobre tus respuestas y aclaremos cualquier duda que tengas.

**Enunciado**: vas a crear una clase sencilla llamada Punto que represente un punto en el espacio con dos coordenadas (x e y). Luego, crearás un objeto de esta clase en el stack y utilizarás el depurador para inspeccionar su contenido y dirección de memoria.

Pasos:

- Abre Visual Studio y crea un nuevo proyecto de consola en C++.
- Define la siguiente clase en un archivo .cpp (puedes incluir todo en main.cpp):

``` cpp
#include <iostream>
using namespace std;

class Punto {
public:
    int x;
    int y;

    // Constructor
    Punto(int _x, int _y) : x(_x), y(_y) {
        cout << "Constructor: Punto(" << x << ", " << y << ") creado." << endl;
    }

    // Destructor
    ~Punto() {
        cout << "Destructor: Punto(" << x << ", " << y << ") destruido." << endl;
    }

    // Método para imprimir valores
    void imprimir() {
        cout << "Punto(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    // Coloca un breakpoint en la siguiente línea
    Punto p(10, 20);

    // Muestra el contenido del objeto
    p.imprimir();

    // Utiliza el depurador para inspeccionar 'p', observa la dirección de memoria y el valor de x e y.
    return 0;
}
```

- Vas a analizar el programa anterior con su equivalente en C# (puedes crear un nuevo proyecto de consola C# en 
una nueva ventana de Visual Studio):

``` csharp
using System;

public class Punto
{
    public int x;
    public int y;

    public Punto(int _x, int _y)
    {
        x = _x;
        y = _y;
        Console.WriteLine($"Constructor: Punto({x}, {y}) creado.");
    }

    ~Punto()
    {
        Console.WriteLine($"Destructor: Punto({x}, {y}) destruido.");
    }

    public void Imprimir()
    {
        Console.WriteLine($"Punto({x}, {y})");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Punto p = new Punto(10, 20);
        p.Imprimir();
    }
}
```

- Ejecuta el programa en C++ en modo depuración (F5) y coloca un breakpoint en la línea donde se declara Punto p(10, 20);.
- Paso a paso (F10), observa en la ventana de variables (Autos/Locals) los valores de x y y. En el menú Debug, selecciona Windows > Memory > Memory 1 y observa la dirección de memoria de `p`. Escribe en la entrada de texto de Memory 1 la dirección de memoria de `p` así `&p` y presiona Enter. Observa la dirección de memoria de `p`. Observa el contenido de la memoria, deberías ver algunos números en hexadecimal, tales como 0a 00 00 00 14 00 00 00.
- Abre la calculadora de Windows y selecciona el modo de programador. Cambia a modo hexadecimal. Escribe 0a ¿Qué valor en decimal obtienes? Escribe 14 ¿Qué valor en decimal obtienes? ¿Qué observas?
- Nota el orden en el que están almacenados los bytes en la memoria. Observa que el byte de menor peso (menos significativo) está almacenado primero, es decir, en una dirección de memoria menor. A esto se le conocen como arquitecturas little-endian. Otro tipo de arquitectura es big-endian, donde el byte de mayor peso (más significativo) se almacena primero. La mayoría de las arquitecturas modernas son little-endian. Si la arquitectura de tu computador fuera big-endian, ¿Cómo quedarían almacenados los bytes en la memoria de `p`?

**Entrega**: 

Basado en tus observaciones realizando experimentos con el depurador, responde las preguntas que aparecen en el enunciado. Adicionalmente, responde las siguientes preguntas:

- ¿Cuál es la diferencia entre un constructor y un destructor en C++?
- ¿Cuál es la diferencia entre un objeto y una clase en C++?
- ¿Qué diferencia notas entre el objeto Punto en C++ y C#? 
    - ¿Qué es `p` en C++ y qué es `p` en C#? (en uno de ellos `p` es un objeto y en el otro es una referencia a un objeto).
    - ¿En qué parte de memoria se almacena `p` en C++ y en C#?
- Captura de pantalla del depurador mostrando la variable p y su dirección de memoria.
- ¿Qué observaste con el depurador acerca de `p`? Según lo que observaste ¿Qué es un objeto en C++?
