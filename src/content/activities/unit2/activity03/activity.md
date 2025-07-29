#### Experimenta

Dale una mirada a este código:

``` js
let position;

function setup() {
    createCanvas(400, 400);
    position = createVector(6,9);
    console.log(position.toString());
    playingVector(position);
    console.log(position.toString());
    noLoop();
}

function playingVector(v){
    v.x = 20;
    v.y = 30;
}

function draw() {
    background(220);
    console.log("Only once");
}

```

:::caution[📤 Bitácora]

- ¿Qué resultado esperas obtener en el programa anterior?
- ¿Qué resultado obtuviste?
- Recuerda los conceptos de paso por valor y paso por referencia en programación. Muestra ejemplos de este concepto en 
javascript. 
- ¿Qué tipo de paso se está realizando en el código?
- ¿Qué aprendiste?
::: 


