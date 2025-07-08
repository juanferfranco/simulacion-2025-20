#### Objetos con miembros estáticos y variables de instancia

**Enunciado**: 

Crea una clase llamada Contador que tenga:

- Un miembro de instancia, `valor`, que se incremente cada vez que se llame a un método `incrementar()`.  
- Un miembro estático, `total`, que cuente cuántos objetos de la clase se han creado.  
- Explora cómo se gestionan estas variables (estáticas vs. no estáticas) en la memoria, y utiliza el depurador para inspeccionar sus valores y direcciones.  

Pasos:

Define la clase Contador de la siguiente manera:

``` cpp
#include <iostream>
using namespace std;

class Contador {
public:
    int valor;
    static int total;

    // Constructor
    Contador(int v = 0) : valor(v) {
        total++;
        cout << "Contador creado. total de Contadores = " << total << endl;
    }

    // Destructor
    ~Contador() {
        cout << "Contador destruido. valor = " << valor << endl;
    }

    // Método para incrementar el contador de instancia
    void incrementar() {
        valor++;
    }
};

// Definición e inicialización del miembro estático
int Contador::total = 0;

int main() {
    // Crea varios objetos en el stack
    Contador c1(5);
    Contador c2(10);

    // Inspecciona con el depurador las direcciones de c1 y c2.
    // Observa que 'total' es compartido entre todos los objetos.

    c1.incrementar();
    c2.incrementar();

    cout << "c1.valor = " << c1.valor << endl;
    cout << "c2.valor = " << c2.valor << endl;
    cout << "Contador::total = " << Contador::total << endl;

    // Puedes también crear un objeto dinámico para comparar:
    Contador* c3 = new Contador(15);
    c3->incrementar();
    cout << "c3->valor = " << c3->valor << endl;

    // Coloca breakpoints en la creación de cada objeto y en las llamadas a 'incrementar()'
    // Observa cómo el miembro estático 'total' se comparte y no se almacena en el stack de cada objeto.

    delete c3;
    return 0;
}
```

- Ejecuta el programa en modo depuración e inspecciona los valores y direcciones de c1, c2, c3.
- Observa el contenido de los objetos en memoria (Debug->Windows->Memory->Memory 1). Recuerda escribir la dirección de memoria de cada objeto como `&c1` por ejemplo. ¿Puedes observar el valor de los miembros `valor` y `total`?
- ¿En dónde está almacenado el miembro `valor` y el miembro `total` de la clase Contador?
- Selecciona `Contador::total` y presiona click derecho, luego selecciona "Add Watch". Esto te permite inspeccionar 
variables globales y puede ser de utilidad cuando investigues en los experimentos del curso.

**Entrega**:

- ¿Qué puedes concluir de los miembros estáticos y de instancia de una clase en C++? ¿Cómo se gestionan en memoria? ¿Qué ventajas y desventajas tienen? ¿Cuándo es útil utilizarlos?
- En el programa, en qué segmento de memoria se están almacenando c1, c2, c3 y Contador::total? Ten especial cuidado con la 
respuesta que das para el caso de c3, piensa de nuevo, qué es c3 y qué está almacenando. Ahora, responde de nuevo, en qué 
segmento de la memoria se está almacenando c3 y en qué segmento de la memoria se está almacenando el objeto al que apunta c3.

