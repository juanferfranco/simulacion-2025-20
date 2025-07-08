#### Explorando el ciclo de vida de un objeto

**Enunciado**: realiza un experimento en el que crees un objeto dentro de un bloque de código y observa qué ocurre al salir del bloque (uso del stack) en comparación con un objeto creado dinámicamente que persiste hasta que lo liberas.

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
    {
        cout << "Inicio del bloque" << endl;
        Punto pBloque(100, 200);
        pBloque.imprimir();
        // Coloca un breakpoint aquí para ver 'pBloque' en el stack.
    }
    // Al salir del bloque, el destructor de 'pBloque' se invoca.
    cout << "Fuera del bloque" << endl;

    // Creación dinámica:
    Punto* pDinamico = new Punto(300, 400);
    pDinamico->imprimir();
    // 'pDinamico' sigue existiendo hasta que se libere manualmente.
    // Coloca un breakpoint aquí y observa la dirección de memoria.
    delete pDinamico;
    // Después de 'delete', el destructor se llama y la memoria se libera.

    return 0;
}

```

Ten presente que un bloque es un conjunto de instrucciones encerradas entre llaves `{}`.

- Ejecuta el programa en depuración paso a paso. No olvides colocar un breakpoint en la línea 
`cout << "Inicio del bloque" << endl;`. 
- Inspecciona el ciclo de vida de pBloque (observa el constructor y el destructor al entrar y salir del bloque).
- Observa que el objeto creado dinámicamente no se destruye automáticamente al salir del bloque, sino cuando se llama a delete.

**Entrega**:

- Explicación del ciclo de vida de un objeto en el stack versus uno en el heap.
- Ahora realiza la siguiente modificación:

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
    {
        cout << "Inicio del bloque" << endl;
        Punto pBloque(100, 200);
        pBloque.imprimir();
        // Coloca un breakpoint aquí para ver 'pBloque' en el stack.
    }
    // Al salir del bloque, el destructor de 'pBloque' se invoca.
    cout << "Fuera del bloque" << endl;
    // Creación dinámica:
    Punto* pDinamico = new Punto(300, 400);
    pDinamico->imprimir();
    // 'pDinamico' sigue existiendo hasta que se libere manualmente.
    // Coloca un breakpoint aquí y observa la dirección de memoria.
    delete pDinamico;
    // Después de 'delete', el destructor se llama y la memoria se libera.

	{
		cout << "Inicio del bloque 2" << endl;
		Punto* pBloque2 = new Punto(500, 600);
		pBloque2->imprimir();
    }
    pBloque2->imprimir();
    delete pBloque2;

    return 0;
}

```

- ¿Compila? ¿Por qué? 
- Modifica el programa para declarar `pBloque2` por fuera del bloque, pero inicializarlo dentro del bloque. ¿Qué ocurre? ¿Por qué?

- En este caso:

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
    {
        cout << "Inicio del bloque" << endl;
        Punto pBloque(100, 200);
        pBloque.imprimir();
        // Coloca un breakpoint aquí para ver 'pBloque' en el stack.
    }

	Punto* pBloque2 = nullptr;
	{
		cout << "Inicio del bloque 2" << endl;
		pBloque2 = new Punto(500, 600);
		pBloque2->imprimir();
    }
    pBloque2->imprimir();
    delete pBloque2;

    return 0;
}

```

¿Por qué el objeto pBloque se destruye al salir del bloque y pBloque2 no? Recuerda de nuevo, pBloque2 es un objeto o 
es una referencia a un objeto? ¿En qué parte de la memoria se almacena pBloque2? ¿En qué parte de la memoria se almacena el objeto al que apunta pBloque2?
