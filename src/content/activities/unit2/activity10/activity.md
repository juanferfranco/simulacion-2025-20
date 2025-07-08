#### Llegaron las funciones

**Enunciado**: considera el siguiente programa en lenguaje de máquina. Salva el programa 
en un archivo "test.hack" y cárgalo en el simulador. Una vez cargado lo puedes 
visualizar en formato **asm**.

```
0100000000000000
1110110000010000
0000000000010000
1110001100001000
0110000000000000
1111110000010000
0000000000010011
1110001100000101
0000000000010000
1111110000010000
0100000000000000
1110010011010000
0000000000000100
1110001100000110
0000000000010000
1111110010101000
1110101010001000
0000000000000100
1110101010000111
0000000000010000
1111110000010000
0110000000000000
1110010011010000
0000000000000100
1110001100000011
0000000000010000
1111110000100000
1110111010001000
0000000000010000
1111110111001000
0000000000000100
1110101010000111
```

- Vas a modificar este programa para que pinte la pantalla solo 
cuando se presione la tecla **p**.
- Debe borrar la pantalla solo si se presiona la tecla **b**.
- Vas a proponer una implementación para modificar el programa 
de modo que tenga una función llamada pantalla. La función 
pantalla deberá pintar o borrar la pantalla dependiendo del valor 
que le pases a esta función. Puedes imaginarte la función pantalla 
de la siguiente manera en lenguaje de alto nivel, pero la idea es 
que implementes en ensamblador su funcionalidad:

``` c
void pantalla(int valor){

    if(valor == 'p'){
        //Pinta
    }
    else if(valor == 'q'){
        // Borra
    }
    else{
        // No hace nada
    }
}
```

En ensamblador tendrías que hacer algo así:

``` asm
(inicio)
// Código que lee el teclado
.
.
.

// Debes pensar en este punto dos cosas antes de llamar a la función 
// pantalla:
// 1. ¿Cómo le vas a pasar a pantalla el valor de la tecla presionada
// 2. Una vez termine pantalla debe retornar a **retorno_pantalla**

@pantalla
0;JMP
(retorno_pantalla)
@inicio
0;JMP


(pantalla)
// Aquí va el código inicial de la función
.
.
.
// Aquí va el código que permite retornar de la función
```

**Entrega**: la modificación solicitada en lenguaje ensamblador.