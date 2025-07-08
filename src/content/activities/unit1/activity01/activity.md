### Ciclo feetch-decode-execute

El siguiente programa está escrito en el lenguaje ensamblador del computador 
Hack. Este computador no es un computador comercial, sino un computador didáctico 
que te permitirá acercarte a los conceptos fundamentales de manera amigable.

``` asm
@1 
D=A 
@2 
D=D+A 
@16 
M=D 
@6 
0;JMP
```

¿Qué crees que haga este programa? 

Para responder esta pregunta te propondré que uses un simulador de la CPU Hack que está 
[aquí](https://nand2tetris.github.io/web-ide/cpu).

Para ejecutar este programa la CPU realiza un **ciclo** constante llamado Fetch-Decode-Execute.

El ciclo Fetch-Decode-Execute describe cómo la CPU ejecuta instrucciones de un programa. Aquí está explicado 
de forma breve y simple:

Fetch (buscar): la CPU obtiene (lee) la siguiente instrucción desde la memoria. El contador de programa (PC) 
indica dónde se encuentra esa instrucción.

Decode (decodificar): la CPU interpreta la instrucción que acaba de leer. Esto significa entender qué 
operación debe realizarse y qué datos o recursos necesita.

Execute (ejecutar): la CPU realiza la operación indicada. Por ejemplo, puede ser una operación matemática, 
mover datos entre registros, o acceder a la memoria.

Este ciclo se repite continuamente mientras la computadora esté encendida, procesando instrucciones una 
tras otra. Es la base del funcionamiento de cualquier procesador.

**Enunciado**: una vez has comprendido cómo ejecuta un programa una CPU te pediré que hagas algunas modificaciones 
al programa que te presenté:

- Suma los número 60 y 9 y guarda el resultado en la posición de memoria 6.  
- Has que el programa vuelva a comenzar desde la posición 0 una vez almacene el resultado 
de la operación.

**Entrega**: los códigos de los dos programas y una breve explicación de los cambios que tuviste que realizar