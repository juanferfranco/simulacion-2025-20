#### Descifrando el Hack 

**Enunciado**: en el capítulo 4 del libro The Elements of Computing Systems que puedes 
ver [aquí](https://www.nand2tetris.org/_files/ugd/44046b_7ef1c00a714c46768f08c459a6cab45a.pdf) (es el mismo capítulo que analizaste la unidad anterior), hay un ejemplo interesante. Se trata la traducción 
de un programa de lenguaje C++ a lenguaje ensamblador:

``` cpp
//Adds 1+...+100.
 int i=1;
 int sum=0;
 
 while(i <=100){
    sum+= i;
    i++;
 }
 ```
 Traducción a ensamblador: 

``` asm
// Adds1+...+100.
 @i // i refers to some memory location.
 M=1 // i=1
 @sum // sum refers to some memory location.
 M=0 // sum=0
 (LOOP)
 @i
 D=M // D=i
 @100
 D=D-A // D=i-100
 @END
 D;JGT // If(i-100)>0 gotoEND
 @i
 D=M // D=i
 @sum
 M=D+M // sum=sum+i
 @i
 M=M+1 // i=i+1
 @LOOP
 0;JMP // GotoLOOP
 (END)
 @END
 0;JMP // Infinite loop
```

- Carga el programa en el [simulador](https://nand2tetris.github.io/web-ide/cpu) y ejecuta 
paso a paso cada instrucción. Trata de predecir el resultado de la instrucción 
antes de ejecutarla.
- ¿En qué direcciones de memoria se implementan las variables i, sum?
- Basado en esta experiencia, ¿Cuál es la diferencia entre la dirección de una 
variable y su contenido?
- Explica cómo se implementa la condición i <= 100

**Entrega**: responde a las cuestiones anteriores en tu bitácora.