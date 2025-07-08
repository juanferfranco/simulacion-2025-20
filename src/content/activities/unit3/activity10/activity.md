#### Funciones y objetos en C++

**Enunciado**: analiza, ejecuta, depura y experimenta con el siguiente código en C++.

``` cpp
#include <iostream>
#include <string>
using namespace std;

class Punto {
public:
	string name;
    int x;
    int y;

    // Constructor
    Punto(string _name, int _x, int _y) : name(_name),x(_x), y(_y) {
        cout << "Constructor: Punto "<< name <<" (" << x << ", " << y << ") creado." << endl;
    }

    // Destructor
    ~Punto() {
        cout << "Destructor: Punto " << name << "(" << x << ", " << y << ") destruido." << endl;
    }

    // Método para imprimir valores
    void imprimir() {
        cout << "Punto "<< name << "(" << x << ", " << y << ")" << endl;
    }
};

void cambiarNombre(Punto p, string nuevoNombre) {
	p.name = nuevoNombre;
}

int main() {
    // Objeto original
    Punto original("original",70, 80);
    original.imprimir();

	cambiarNombre(original, "cambiado");
	original.imprimir();
    return 0;
}

```

**Entrega**:

- ¿Qué ocurre después de llamar a la función `cambiarNombre`? ¿Por qué aparece el mensaje `Destructor: Punto cambiado(70, 80) destruido.`? 
- ¿Por qué `original` sigue existiendo luego de llamar `cambiarNombre`?
- ¿En qué parte del mapa de memoria se encuentra `original` y en qué parte se encuentra `p`? ¿Son el mismo objeto? (recuerda 
usar siempre el depurador para responder estas preguntas).
- Modifica la función `cambiarNombre`:

``` cpp
void cambiarNombre(Punto& p, string nuevoNombre) {
	p.name = nuevoNombre;
}
```
- ¿Qué ocurre ahora? ¿Por qué?

- Modifica ahora a `cambiarNombre` y a `main` de la siguiente manera:

``` cpp
void cambiarNombre(Punto* p, string nuevoNombre) {
	p->name = nuevoNombre;
}

int main() {
    // Objeto original
    Punto original("original",70, 80);
    original.imprimir();

	cambiarNombre(&original, "cambiado");
	original.imprimir();
    return 0;
}
```

- ¿Qué ocurre ahora? ¿Por qué?
- En este caso ¿Cuál es la diferencia entre pasar un objeto por valor, por referencia y por puntero? 