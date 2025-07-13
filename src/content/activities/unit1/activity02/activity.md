#### Ejemplo de aleatoriedad en el arte generativo

Ahora analicemos juntos el trabajo del año 2025-10 de la estudiante Sofía Lezcano Arenas.
Podemos ver la obra [aquí](https://editor.p5js.org/juanferfranco/sketches/GuYP9nwMk).


El concepto de la creación de Sofía Lezcano Arenas es una obra de arte generativo e interactivo que interpreta 
visualmente la canción "The Seed" de AURORA. La idea central es representar una atmósfera orgánica a través de 
la metáfora de un gran árbol que expande sus raíces, simbolizando el crecimiento y la vida en sintonía con la música.

Para lograrlo, el proyecto utiliza la biblioteca p5.js y p5.sound para analizar el audio en tiempo real, vinculando 
características musicales específicas a elementos visuales:

* Ritmo y amplitud: el ritmo de la canción dicta la velocidad del movimiento de las raíces, mientras que la amplitud 
(volumen) controla directamente el brillo y la saturación de sus colores. Esto crea una sincronía visual donde los momentos 
más intensos de la canción se traducen en un crecimiento más rápido y colores más vivos.

* Algoritmo generativo: el núcleo de la obra es un **sistema de partículas** que representa las raíces. Su movimiento es 
guiado por un **flowfield** (campo de flujo) basado en **ruido Perlin** para lograr un comportamiento orgánico, coordinado 
y no repetitivo. Esto asegura que cada visualización sea única.  

* Interactividad: el usuario puede interactuar con la obra haciendo clic en el lienzo, lo que genera un nuevo flowfield y, 
por lo tanto, altera el patrón de movimiento de las raíces.

<details>
<summary>Este es el código (modificado) de Sofi</summary>

``` js
// Archivo: sketch.js

let audio;
let amplitude;
let audioStarted = false;

let roots = [];
let flowfield;
let spacing = 20;
let cols, rows;
let firstFrame = false;

// --- 4. Paletas de Colores Configurables ---
let palettes;
let currentPaletteIndex = 0;

function preload() {
  soundFormats('mp3', 'ogg');
  audio = loadSound('AURORA - The Seed.mp3');
}

function setup() {
  // El canvas se crea con un tamaño base, pero se ajustará en pantalla completa.
  createCanvas(1280, 720); // Un tamaño más manejable para modo ventana
  frameRate(60);
  
  colorMode(HSB, 360, 100, 100, 255);
  background(0);

  // Inicializar paletas de colores
  setupPalettes();

  amplitude = new p5.Amplitude();
  
  // La inicialización del flowfield se mueve a una función
  // para poder llamarla al reiniciar o redimensionar.
  initializeFlowField();
}

function draw() {
  if (!audioStarted) {
    background(0);
    textAlign(CENTER, CENTER);
    textSize(36);
    fill(255);
    let initHeight = height * 0.3;
    let interline = 60;
    text("When the last tree has been cut down,", width / 2, initHeight + interline * 1);
    text("the last fish caught,", width / 2, initHeight + interline * 2);
    text("the last river poisoned,", width / 2, initHeight + interline * 3);
    text("only then will we realize", width / 2, initHeight + interline * 4);
    text("WE CANNOT EAT MONEY", width / 2, initHeight + interline * 5);
    text("Click to start", width / 2, initHeight + interline * 10);
    return;
  }

  if (!firstFrame) {
    background(0);
    firstFrame = true;
  }

  let amp_ = amplitude.getLevel();

  let brightness = map(amp_, 0, 0.3, 20, 100);
  let speed_ = map(amp_, 0, 0.3, 0.5, 3);
  
  // --- 3. Flowfield dinámico con el audio ---
  // Pasamos la amplitud a la función update del flowfield
  flowfield.update(amp_);

  for (let i = 0; i < 4; i++) {
    roots.push(new Root(width / 2, height / 2, flowfield));
  }

  for (let i = roots.length - 1; i >= 0; i--) {
    let r = roots[i];
    r.update(speed_);
    r.display(brightness);
    if (r.isDead()) {
      roots.splice(i, 1);
    }
  }
}

function mousePressed() {
  if (!audioStarted) {
    audio.loop();
    audioStarted = true;
    noCursor();
  }
  // Se ha eliminado la reinicialización del flowfield al hacer click.
  // Ahora el audio se encarga de su evolución.
}

function keyPressed() {
  if (key === 'r') {
    initializeFlowField(); // Reinicia el campo de vectores
    background(0); // Limpia el fondo
    roots = []; // Vacía las raíces existentes
  }
  
  // --- 2. Modo Pantalla Completa ---
  if (key === 'f') {
    let fs = fullscreen();
    fullscreen(!fs);
  }
  
  // --- 4. Cambiar Paleta de Colores ---
  if (key === 'c') {
    currentPaletteIndex = (currentPaletteIndex + 1) % palettes.length;
  }
}

// --- 2. Función para manejar el redimensionamiento de la ventana ---
// Se llama automáticamente cuando se entra/sale de pantalla completa.
function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
  initializeFlowField();
  background(0);
  roots = [];
}

function initializeFlowField() {
  cols = floor(width / spacing);
  rows = floor(height / spacing);
  flowfield = new FlowField(spacing);
}

// --- 4. Función para definir las paletas ---
function setupPalettes() {
  palettes = [
    { // Bosque / Original
      name: "Forest",
      c1: color(103, 68, 80),
      c2: color(235, 54, 80)
    },
    { // Fuego
      name: "Fire",
      c1: color(0, 90, 100),
      c2: color(50, 100, 100)
    },
    { // Océano
      name: "Ocean",
      c1: color(180, 80, 90),
      c2: color(240, 70, 100)
    },
    { // Nebulosa / Synthwave
      name: "Nebula",
      c1: color(270, 90, 100),
      c2: color(330, 80, 100)
    }
  ];
}


// --- CLASES (FlowField y Root modificadas) ---

class FlowField {
  constructor(spacing) {
    this.spacing = spacing;
    this.cols = floor(width / spacing);
    this.rows = floor(height / spacing);
    this.field = [];
    this.zoff = 0;
    this.init();
  }

  init() {
    noiseSeed(floor(random(10000)));
    let xoff = 0;
    for (let i = 0; i < this.cols; i++) {
      this.field[i] = [];
      let yoff = 0;
      for (let j = 0; j < this.rows; j++) {
        let angle = map(noise(xoff, yoff), 0, 1, 0, TWO_PI);
        this.field[i][j] = p5.Vector.fromAngle(angle);
        yoff += 0.1;
      }
      xoff += 0.1;
    }
  }

  // --- 3. Update modificado para aceptar la amplitud del audio ---
  update(amp_) {
    // La velocidad a la que cambia el campo de ruido (z-axis)
    // ahora depende del volumen de la música.
    let zIncrement = map(amp_, 0, 0.3, 0.0005, 0.005);
    this.zoff += zIncrement;
    
    for (let x = 0; x < this.cols; x++) {
      for (let y = 0; y < this.rows; y++) {
        // Usamos el noise tridimensional para una evolución suave
        let angle = noise(x * 0.05, y * 0.05, this.zoff) * TWO_PI * 2;
        this.field[x][y] = p5.Vector.fromAngle(angle);
      }
    }
  }

  lookup(lookup) {
    let column = floor(constrain(lookup.x / this.spacing, 0, this.cols - 1));
    let row = floor(constrain(lookup.y / this.spacing, 0, this.rows - 1));
    return this.field[column][row].copy();
  }
}

class Root {
  constructor(x, y, flowfield) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(0.5);
    this.acc = createVector(0, 0);
    this.r = 10;
    this.flowfield = flowfield;

    // --- 4. Lectura de la paleta de color activa ---
    const currentPalette = palettes[currentPaletteIndex];
    this.baseColor = lerpColor(currentPalette.c1, currentPalette.c2, random(1));
  }
  
  // update, display, y isDead permanecen sin cambios.
  update(speed_) {
    let force = this.flowfield.lookup(this.pos);
    this.acc.add(force);
    this.vel.add(this.acc);
    this.vel.limit(speed_);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.r *= 0.995;
  }

  display(brightness) {
    let c = color(
      hue(this.baseColor),
      saturation(this.baseColor),
      brightness,
      200
    );
    fill(c);
    noStroke();
    ellipse(this.pos.x, this.pos.y, this.r * 2, this.r * 2);
  }

  isDead() {
    return this.r < 0.5 ||
           this.pos.x < -10 || this.pos.x > width + 10 ||
           this.pos.y < -10 || this.pos.y > height + 10;
  }
}
```
</details>

En esta obra, la aleatoriedad es el motor que garantiza que cada ejecución sea una experiencia visual única e irrepetible, 
actuando como un "ADN" digital para la creación. Su papel se manifiesta en varios niveles: en la noiseSeed(random(10000)) 
que genera un campo de flujo (flowfield) completamente nuevo cada vez que se reinicia, dictando patrones de movimiento 
impredecibles para las raíces; en la velocidad inicial de cada partícula (p5.Vector.random2D()) que introduce pequeñas 
variaciones en su arranque; y en la selección de color (lerpColor(..., random(1))) que asigna a cada raíz una tonalidad 
distinta dentro de la paleta activa. Esta imprevisibilidad controlada es fundamental porque infunde a la obra una sensación 
de vida y espontaneidad, evitando que sea una animación predecible y repetitiva. En el arte generativo, la aleatoriedad 
no es un caos, sino un concepto esencial que permite al artista diseñar un sistema de reglas y parámetros que, al combinarse 
con elementos estocásticos, produce resultados emergentes y siempre novedosos, convirtiendo al código en un colaborador 
creativo en lugar de una simple herramienta de ejecución.

:::caution[📤 Bitácora] 
Luego de ver el trabajo de Sofía piensa y escribe en TUS PROPIAS palabras:

* Cuál es el papel de la aleatoriedad en su obra.
* Según tu perfil profesional, cómo se aplica el concepto de aleatoriedad en el tipo de proyectos que desarrollas. 
Ilustra tu respuesta con ejemplos concretos.
:::



