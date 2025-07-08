#### Paso por valor y paso por referencia

Analiza el siguiente código y trata de responder las preguntas que se plantean, pero 
ten presente que luego de responderlas debes ejecutar el código para verificar 
tus respuestas. Además, te explicaré el comportamiento del código para que puedas 
comparar tus respuestas con la explicación.

``` cpp
#include <iostream>

using namespace std;

// Función que modifica el parámetro pasado por valor
void modificarPorValor(int n) {
    cout << "Dentro de modificarPorValor, valor inicial: " << n << endl;
    n += 5;
    cout << "Dentro de modificarPorValor, valor modificado: " << n << endl;
}

// Función que modifica el parámetro pasado por referencia
void modificarPorReferencia(int &n) {
    cout << "Dentro de modificarPorReferencia, valor inicial: " << n << endl;
    n += 5;
    cout << "Dentro de modificarPorReferencia, valor modificado: " << n << endl;
}

// Función que modifica el parámetro utilizando punteros
void modificarPorPuntero(int *n) {
    cout << "Dentro de modificarPorPuntero, valor inicial: " << *n << endl;
    *n += 5;
    cout << "Dentro de modificarPorPuntero, valor modificado: " << *n << endl;
}

int main() {
    int a = 10;
    int b = 10;
    int c = 10;

    cout << "Valor inicial de a (paso por valor): " << a << endl;
    cout << "Valor inicial de b (paso por referencia): " << b << endl;
    cout << "Valor inicial de c (paso por puntero): " << c << endl;

    cout << "\nLlamando a modificarPorValor(a)..." << endl;
    modificarPorValor(a);
    cout << "Después de modificarPorValor, valor de a: " << a << endl;

    cout << "\nLlamando a modificarPorReferencia(b)..." << endl;
    modificarPorReferencia(b);
    cout << "Después de modificarPorReferencia, valor de b: " << b << endl;

    cout << "\nLlamando a modificarPorPuntero(&c)..." << endl;
    modificarPorPuntero(&c);
    cout << "Después de modificarPorPuntero, valor de c: " << c << endl;

    return 0;
}
```

- Predicción: antes de ejecutar el programa, predice la salida de cada función y explica el resultado.
- ¿Qué diferencias observas en el comportamiento de a, b y c tras cada llamada?
- ¿Por qué ocurre esta diferencia?

Analicemos el código línea por línea y expliquemos en detalle qué sucede en cada función y cómo se comporta el paso de parámetros de diferentes maneras.

1. Inclusión de librerías y uso del espacio de nombres:

``` cpp
#include <iostream>
using namespace std;
```

- #include <iostream>: se incluye la librería estándar de C++ para poder usar cout y otras funcionalidades de entrada/salida.  
- using namespace std;: permite usar los elementos del espacio de nombres std directamente, 
sin tener que escribir std:: cada vez como en la actividad anterior.  

2. Función modificarPorValor

``` cpp	
void modificarPorValor(int n) {
    cout << "Dentro de modificarPorValor, valor inicial: " << n << endl;
    n += 5;
    cout << "Dentro de modificarPorValor, valor modificado: " << n << endl;
}

```

Paso por Valor:

- Parámetro: la función recibe n por valor. Esto significa que se hace una copia del valor de la variable que se pasa desde main().  
- Efecto: las modificaciones que se realizan en n dentro de la función no afectan a la variable original, ya que se trabaja sobre una copia.  
- Salida: dentro de la función se imprimen dos mensajes: uno antes y otro después de sumar 5 a n. Sin embargo, fuera de la función, la variable original permanece igual.  

3. Función modificarPorReferencia

``` cpp
void modificarPorReferencia(int &n) {
    cout << "Dentro de modificarPorReferencia, valor inicial: " << n << endl;
    n += 5;
    cout << "Dentro de modificarPorReferencia, valor modificado: " << n << endl;
}
```

Paso por Referencia (con Referencias):

- Parámetro: se declara int &n, lo que significa que n es una referencia a la variable original.  
- Efecto: la variable n en la función es un alias de la variable pasada. Cualquier cambio realizado en n afecta directamente a la variable original.  
- Salida: la suma de 5 a n dentro de la función modifica la variable original, y esto se refleja fuera de la función.  

4. Función modificarPorPuntero

``` cpp
void modificarPorPuntero(int *n) {
    cout << "Dentro de modificarPorPuntero, valor inicial: " << *n << endl;
    *n += 5;
    cout << "Dentro de modificarPorPuntero, valor modificado: " << *n << endl;
}
```	

Paso por Puntero:

- Parámetro: la función recibe un puntero int *n, que contiene la dirección de memoria de una variable.  
- Acceso al Valor: para acceder y modificar el valor apuntado, se utiliza el operador de indirección (*).  
- Efecto: al modificar *n, se está cambiando el valor de la variable original a la que apunta el puntero.  
- Salida: al igual que en el caso de la referencia, el cambio (suma de 5) afecta directamente a la variable original.  

5. Función main

``` cpp	
int main() {
    int a = 10;
    int b = 10;
    int c = 10;
```

Se declaran y definen tres variables enteras a, b y c, todas inicializadas en 10. Cada una se utilizará para evaluar uno de los métodos de paso de parámetros.

``` cpp	
cout << "Valor inicial de a (paso por valor): " << a << endl;
cout << "Valor inicial de b (paso por referencia): " << b << endl;
cout << "Valor inicial de c (paso por puntero): " << c << endl;
```	

Se imprime el valor inicial de cada variable antes de cualquier modificación.

Llamada a modificarPorValor

``` cpp
cout << "\nLlamando a modificarPorValor(a)..." << endl;
modificarPorValor(a);
cout << "Después de modificarPorValor, valor de a: " << a << endl;
```

¿Qué ocurre?

- Se llama a modificarPorValor(a). Dentro de la función, a se pasa por valor, lo que genera 
una copia de a.  
- Dentro de la función, se suma 5 a la copia y se imprimen los valores modificados.
- Al regresar a main(), la variable a no ha cambiado, ya que la copia modificada no afecta a la original.  

Resultado Esperado:

- Dentro de la función: valor inicial: 10 y valor modificado: 15.  
- Fuera de la función: a sigue siendo 10.  

Llamada a modificarPorReferencia

``` cpp
cout << "\nLlamando a modificarPorReferencia(b)..." << endl;
modificarPorReferencia(b);
cout << "Después de modificarPorReferencia, valor de b: " << b << endl;
```

¿Qué ocurre?

- Se llama a modificarPorReferencia(b). Aquí, b se pasa por referencia, lo que significa que no se hace una copia: n es simplemente otro nombre para b.  
- Al sumar 5 a n dentro de la función, b se modifica directamente.

Resultado Esperado:

- Dentro de la función: valor inicial: 10 y valor modificado: 15.  
- Fuera de la función: b es 15, reflejando la modificación.  

Llamada a modificarPorPuntero

``` cpp	
cout << "\nLlamando a modificarPorPuntero(&c)..." << endl;
modificarPorPuntero(&c);
cout << "Después de modificarPorPuntero, valor de c: " << c << endl;
```

¿Qué ocurre?

- Se llama a modificarPorPuntero(&c), pasando la dirección de c.  
- Dentro de la función, n es un puntero a c. Usando *n, accedemos al valor de c.  
- Al sumar 5 a *n, se modifica el valor almacenado en c.  

Resultado Esperado:

- Dentro de la función: valor inicial: 10 y valor modificado: 15.
- Fuera de la función: c es 15, ya que se ha modificado directamente mediante el puntero.

6. Conclusión

Paso por Valor:

La función recibe una copia del valor. Las modificaciones realizadas dentro de la función no afectan a la variable original. En este ejemplo, a sigue siendo 10 después de la llamada a modificarPorValor.

Paso por Referencia (con referencias):

La función recibe una referencia (alias) a la variable original. Las modificaciones realizadas dentro de la función afectan a la variable original. En el ejemplo, b se convierte en 15 después de la llamada a modificarPorReferencia.

Paso por Puntero:

La función recibe la dirección de la variable original. Accediendo al valor mediante la indirección (*), se puede modificar el contenido de la variable original. Así, c se convierte en 15 después de la llamada a modificarPorPuntero.

**Enunciado**: implementación de funciones de Swap

Implementa tres versiones de una función para intercambiar (swap) los valores de dos variables enteras, utilizando:

- Paso por valor.  
- Paso por referencia (usando referencias).  
- Paso por puntero.  

Crea un proyecto de consola en Visual Studio. Implementa las siguientes funciones:

``` cpp	
swapPorValor(int a, int b)
```	

Esta función debe intentar intercambiar los valores de a y b pasándolos por valor.
Nota: Se espera que el intercambio no afecte a las variables originales en main().

``` cpp
swapPorReferencia(int &a, int &b)
``` 

Esta función debe intercambiar los valores de a y b utilizando paso por referencia con referencias.

``` cpp
swapPorPuntero(int *a, int *b)
```	

Esta función debe intercambiar los valores de a y b utilizando punteros. Recuerda acceder a los valores con el operador de indirección (*).

En la función main():

Declara dos variables enteras, por ejemplo, x e y, e inicialízalas con valores distintos (por ejemplo, x = 5 e y = 10).

Muestra los valores iniciales de x e y.

Llama a swapPorValor(x, y) y, a continuación, muestra los valores de x e y para comprobar si se han intercambiado.

Vuelve a inicializar x e y con los mismos valores originales.

Llama a swapPorReferencia(x, y) y muestra los valores de x e y.

Vuelve a inicializar x e y con los mismos valores originales.

Llama a swapPorPuntero(&x, &y) y muestra los valores de x e y.

Preguntas para Reflexionar:

- ¿Por qué la versión de swapPorValor no logra intercambiar los valores de x e y en el main()?  
- ¿Cómo y por qué logran las otras dos funciones (por referencia y por puntero) modificar las variables originales?  
- ¿Cuáles son las ventajas y consideraciones de usar referencias versus punteros en este caso?  

**Entrega**: el código de las tres funciones de swap y el código de main() con las llamadas a las funciones y las respuestas a las preguntas planteadas.
