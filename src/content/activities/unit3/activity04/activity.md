#### Experimentos

Vas a realizar múltiples experimentos con el código de la actividad anterior para comprender cómo se comportan 
los segmentos de memoria en un programa C++.

Experimento 1: modificar el segmento de texto:

``` cpp
#include <iostream>
#include <cstdlib>

using namespace std;


int main() {
    // Variable local (stack)
    int a = 10;
    int b = 20;

    /**********************************************************
        EXPERIMENTO 1
    ***********************************************************/

    void* ptr = reinterpret_cast<void*>(&main);
    cout << "Voy a modificar la memoria en la dirección: " << ptr << endl;
    *reinterpret_cast<int*>(ptr) = 0;

    /********************************************************/

    return 0;
}
```

- ¿Qué ocurre? ¿Por qué?

Experimento 2: modificar el segmento de datos (constante global):

``` cpp
#include <iostream>
#include <cstdlib>

using namespace std;

// Constante global
const char* const mensaje_ro = "Hola, memoria de solo lectura";


int main() {
    // Variable local (stack)
    int a = 10;
    int b = 20;


    /**********************************************************
        EXPERIMENTO 2
    ***********************************************************/

    char* ptr = (char*)&mensaje_ro;
    cout << "Voy a modificar la memoria en la dirección: " << ptr << endl;
    *ptr = 0;

    /********************************************************/

    return 0;
}
```

- ¿Qué ocurre? ¿Por qué?


