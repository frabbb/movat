<script setup>
import { ref, defineProps } from "vue";
import { Engine, Bodies, Bounds, Composite, Constraint } from "matter-js";
import helvetica from "../assets/Helvetica-Rounded-Bold.otf";

import p5 from "p5";

const container = ref(null);

const sk = new p5((p) => p);
let canvas, sound, engine, world, font;

let particles = [];
let boundaries = [];
let currentLetter = 0;

const sentence = "MOVAT 2025";
const colors = [
  { r: 255, g: 0, b: 0 },
  { r: 137, g: 194, b: 255 },
  { r: 214, g: 221, b: 92 },
  { r: 54, g: 207, b: 156 },
  { r: 185, g: 182, b: 255 },
  { r: 255, g: 255, b: 255 },
  { r: 255, g: 148, b: 225 },
];

window.addEventListener("resize", resize);

function preload() {
  return new Promise(async (resolve) => {
    await sk.loadFont(
      helvetica,
      (e) => {
        font = e;
        resolve();
      },
      (e) => {
        console.log("error", e);
        resolve();
      }
    );
  });
}

sk.setup = async () => {
  await preload();
  sk.textFont(font);
  canvas = sk.createCanvas(sk.windowWidth, sk.windowHeight);
  container.value.appendChild(canvas.elt);

  engine = Engine.create();
  world = engine.world;

  // let bounds = Bounds.create({
  //   min: { x: 0, y: 0 },
  //   max: { x: sk.width, y: sk.height },
  // });
  boundaries.push(new Boundary(sk.width / 2, sk.height, sk.width, 10, 0));
  boundaries.push(new Boundary(0, sk.height / 2, 10, sk.height, 0));
  boundaries.push(new Boundary(sk.width, sk.height / 2, 10, sk.height, 0));
};

sk.mousePressed = () => {
  let p = new Particle(sk.mouseX, sk.mouseY, 30, false);
  particles.push(p);
};

sk.draw = () => {
  sk.clear();

  Engine.update(engine);
  for (let i = 0; i < boundaries.length; i++) {
    boundaries[i].show();
  }
  for (let i = 0; i < particles.length; i++) {
    particles[i].show();
  }
};

function resize() {}

class Particle {
  constructor(x, y, r, fixed) {
    this.x = x;
    this.y = y;
    this.r = r;
    this.color = colors[Math.floor(Math.random() * colors.length)];
    this.index = particles.length;

    let letter;
    while (!letter || letter === " ") {
      letter = sentence[currentLetter];
      currentLetter++;
      if (currentLetter >= sentence.length) {
        currentLetter = 0;
      }
    }

    this.letter = letter;

    let options = {
      friction: 0,
      restitution: 0.95,
      isStatic: fixed,
    };
    this.body = Bodies.circle(this.x, this.y, this.r, options);
    Composite.add(world, this.body);

    setTimeout(() => {
      this.destroy();
    }, 10000);
  }

  show() {
    let pos = this.body.position;
    let angle = this.body.angle;
    sk.push();
    sk.translate(pos.x, pos.y);
    sk.rotate(angle);
    sk.rectMode(sk.CENTER);
    sk.strokeWeight(3);
    sk.stroke(0);
    sk.fill(this.color.r, this.color.g, this.color.b);
    sk.ellipse(0, 0, this.r * 2);
    sk.fill(0);
    sk.noStroke();
    sk.textAlign(sk.CENTER);
    sk.textSize(40);
    sk.textFont(font);
    sk.text(this.letter, 0, this.r / 2);
    sk.pop();
  }

  destroy() {
    Composite.remove(world, this.body);
    const targetIndex = particles.findIndex((p) => p.index === this.index);
    particles.splice(targetIndex, 1);
  }
}

class Boundary {
  constructor(x, y, w, h, a) {
    this.x = x;
    this.y = y;
    this.w = w;
    this.h = h;
    let options = {
      friction: 0,
      restitution: 0.6,
      angle: a,
      isStatic: true,
    };
    this.body = Bodies.rectangle(this.x, this.y, this.w, this.h, options);
    Composite.add(world, this.body);
  }

  show() {
    let pos = this.body.position;
    let angle = this.body.angle;
    sk.push();
    sk.translate(pos.x, pos.y);
    sk.rotate(angle);
    sk.rectMode(sk.CENTER);
    sk.fill(0);
    // sk.rect(0, 0, this.w, this.h);
    // sk.text(this.letter, 0, 0, this.w, this.h);
    sk.pop();
  }
}
</script>

<template>
  <div class="canvas-container" ref="container"></div>
</template>

<style scoped></style>
