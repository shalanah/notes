# Creative Coding in Canvas & WebGL

## Inspiration
- Sol Lewitt :)
  - http://solvingsol.com/solutions/
- Weird Type by Zach Lieberman
- Anders Hoff @inconvergent
- Phillip Schütte
- Paper Planes by Active Theory
- Nervous System - Generative puzzles, housewares
- Simple shape 3d 44 Festival SESC branding

Tidbit: WebGL faster than Canvas
- Look into https://two.js.org/

## Links
- [Repo](https://github.com/mattdesl/workshop-generative-art)
- [Cheat Sheet](https://github.com/mattdesl/workshop-generative-art/blob/master/docs/cheat-sheet.md)
- [Canvas Sketch](https://github.com/mattdesl/canvas-sketch/)
- [Canvas Sketch Utils](https://github.com/mattdesl/canvas-sketch-util/)

## Setup
#### Install canvas-sketch globally
`npm install canvas-sketch-cli -g`
#### Create directory
`mkdir ....`
#### Init project and open browser
`canvas-sketch sketch.js --new --open`
#### Save img to downloads
`Command - S`
#### Settings
Can use print values
```
const settings = {
  dimensions: 'A3',
  orientation: 'landscape',
  pixelsPerInch: 300,
  units: 'in' // opt set units to something other than px
  //dimensions: [ 2048, 2048 ]
}
```
#### Want resolution independent...
Use % of width/height

## Canvas/randomness utils
`npm i canvas-sketch-util`
#### Lerp interpolation
`const {lerp} = require('canvas-sketch-util/math')`

#### Randomness :)
Control how random
- `const random = require('canvas-sketch-util/random')`
- `random.setSeed(10)`
- `random.value()`
- `const randomSeed = random.getRandomSeed()`

#### Pick randomly from an array
`random.pick([...])`

#### Randomly pick in a range up to max
`random.rangeFloor(0,5)`

#### Mix up array
`random.shuffle(...)

#### Colors
`const palettes = require('nice-color-palettes')`
`const palette = random.pick(palettes)`

## Noise
```
// output -1 through 1
// outputs are close to inputs and always the same for a given input
// frequency very similiar to scaling (zoom in / out)
const frequency = 5
v = noise2D(x, y) * frequency
v = noise3D(x,y,z)
v = noise4D(x,y,z,w)
```

## Three.js
- [Oat to the Goat](http://oatthegoat.co.nz/)
- [Geometries](https://threejs.org/examples/#webgl_geometries)

#### Materials
- `MeshBasicMaterial`
  - one color
  - `[wireframe]` mode
- `MeshNormalMaterial`
  - trippy coloring of mesh good for debugging
- `MeshStandardMaterial`
  - Based on lighting set up
- Flat or smooth shading options

#### Geometry
Some Opts:
- `BoxGeometry`

#### Mesh
Wrapper for geometry and material

#### Scene
Workspace

#### Camera
- `PerspectiveCamera` aerial view 2-pt perspective
- `Orthographic Camera` flatter looking... no vanishing pts

### Setup
`canvas-sketch webgl.js --new --template=three`

#### Basic template that it comes with:
```js
const canvasSketch = require('canvas-sketch');

// Ensure ThreeJS is in global scope for the 'examples/'
global.THREE = require('three')

// Include any additional ThreeJS examples below
require('three/examples/js/controls/OrbitControls')

const settings = {
  // Make the loop animated
  animate: true,
  // Get a WebGL canvas rather than 2D
  context: 'webgl',
  // Turn on MSAA
  attributes: { antialias: true } // smooth edges
}

const sketch = ({ context }) => {
  // Create a renderer
  const renderer = new THREE.WebGLRenderer({
    context
  })

  // WebGL background color
  renderer.setClearColor(
    '#000',
    1 // and ALPHA
  )

  const camera = new THREE.PerspectiveCamera(
    45, // field of view first argument (deg)
    1, // aspect ratio
    0.01, // near
    100 // far plane
  )
  camera.position.set(2, 2, -4)
  camera.lookAt(new THREE.Vector3()) // the center

  // Setup camera controller
  const controls = new THREE.OrbitControls(camera)

  // Setup your scene
  const scene = new THREE.Scene()


  // MeshBasicMaterial... doesn't respond to light
  const mesh = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshStandardMaterial({ // responds to lights
      color: 'white',
      roughness: 0.75,
      flatShading: true
    })
  )
  scene.add(mesh)

  // Specify an ambient/unlit colour
  scene.add(new THREE.AmbientLight('#59314f'))

  // Add some light
  const light = new THREE.PointLight('#45caf7', 1, 15.5)
  light.position.set(2, 2, -4).multiplyScalar(1.5)
  scene.add(light)

  // draw each frame
  return {
    // Handle resize events here
    resize ({ pixelRatio, viewportWidth, viewportHeight }) {
      renderer.setPixelRatio(pixelRatio)
      renderer.setSize(viewportWidth, viewportHeight)
      camera.aspect = viewportWidth / viewportHeight
      camera.updateProjectionMatrix()
    },
    // Update & render your scene here
    render ({ time }) {
      mesh.rotation.y = time * (10 * Math.PI / 180)
      controls.update()
      renderer.render(scene, camera)
    },
    // Dispose of events & renderer for cleaner hot-reloading
    unload () {
      controls.dispose()
      renderer.dispose()
    }
  };
};

canvasSketch(sketch, settings)
```

### Orthographic Camera
```js
// CAMERA
const camera = new THREE.OrthographicCamera();
```
```js
// IN RESIZE
// ISOMETRIC THREEJS CAMERA INSERT FROM CHEAT SHEET
const aspect = viewportWidth / viewportHeight;

// Ortho zoom
const zoom = 1.0; // bigger means zoom out

// Bounds
camera.left = -zoom * aspect;
camera.right = zoom * aspect;
camera.top = zoom;
camera.bottom = -zoom;

// Near/Far
camera.near = -100;
camera.far = 100;

// Set position & look at world center
camera.position.set(zoom, zoom, zoom);
camera.lookAt(new THREE.Vector3());
```

### Exporting gif
Specify where imgs should go
```js
const settings = {
  animate: true,
  context: 'webgl',
  dimensions: [512, 512], // for gif
  fps: 24, // for gif
  duration: 2, // for our gif time
  attributes: { antialias: true }
};
render ({ playhead }) { // playhead goes hand and hand with duration (0-1)
  scene.rotation.z = playhead * Math.PI * 2 // seamless... can also use easing functions here like sine... `npm i eases`, `bezier-easing`
  renderer.render(scene, camera);
}
```

`canvas-sketch webgl.js --output=tmp/`
Cmd-Shift-S for imgs
``
- FFMPEG
- giftool.surge.sh
  - Drop folder of assets and will generate a gif

### Shaders
How does each pixel get colored?
```glsl
precision highp float; // highp high precision points
varying vec2 vUv; // 

// Variables from Javascript
uniform float time 

void main () {
  // Output color
  gl_FragColor = vec4(1.0)
}
```

#### Grayscale shader black to white horizontally
```glsl
precision highp float; // highp high precision points
varying vec2 vUv; // uv coordinate (0-1 coords)

// Main func
void main () {
  // Create a RGB color
  vec3 color = vec3(vUv.x);

  // Opacity
  float alpha = 1.0;

  // Output color
  gl_FragColor = vec4(vec3(vUv.x), 1.0)
}
```
### Setup
`canvas-sketch shader.js --new --template=shader`

#### Default file
```js
const canvasSketch = require('canvas-sketch');
const createShader = require('canvas-sketch-util/shader'); // for full screen shader quickly
const glsl = require('glslify');

// Setup our sketch
const settings = {
  context: 'webgl',
  animate: true
};

// Your glsl code
const frag = glsl(`
  precision highp float; 

  uniform float time;
  varying vec2 vUv;

  void main () {
    vec3 color = 0.5 + 0.5 * cos(time + vUv.xyx + vec3(0.0, 2.0, 4.0));
    gl_FragColor = vec4(color, 1.0);
  }
`);

// Your sketch, which simply returns the shader
const sketch = ({ gl }) => {
  // Create the shader and return it
  return createShader({
    // Pass along WebGL context
    gl,
    // Specify fragment and/or vertex shader strings
    frag,
    // Specify additional uniforms to pass down to the shaders
    uniforms: {
      // Expose props from canvas-sketch
      time: ({ time }) => time
    }
  });
};

canvasSketch(sketch, settings);
```

Red
```glsl
const frag = glsl(`
void main () {
  gl_FragColor = vec4(1.0, 0.0, 0.0, 1.0);
}
```
Grayscale
```glsl
void main () {
  gl_FragColor = vec4(vec3(vUv.y), 1.0);
}
```
White to black over time
```glsl
void main () {
  vec3 color = vec3(sin(time) + 1.0);
  gl_FragColor = vec4(color, 1.0);
}
```
Gradient from one color to the next
```glsl
void main () {
  vec3 colorA = vec3(1.0,0.0,0.0);
  vec3 colorB = vec3(0.0,1.0,0.0);
  vec3 color = mix(colorA, colorB, vUv.x);
  gl_FragColor = vec4(color, 1.0);
}
```
Black circle in gradient
```glsl
void main () {
  vec3 colorA = vec3(1.0,0.0,0.0);
  vec3 colorB = vec3(0.0,0.0,1.0);

  vec2 center = vUv - 0.5; /* same as vec2(0.5, 0.5) */
  float dist = length(center);

  float alpha = 1.0
  vec3 color = mix(colorA, colorB, vUv.x);
  gl_FragColor = vec4(color, dist > 0.25 ? 1.0 : 0.0);
}
```
White bg and fixed circle
```js
// Your glsl code
const frag = glsl(`
  precision highp float;

  uniform float time;
  uniform float aspect;
  varying vec2 vUv;

  void main () {
    vec3 colorA = vec3(1.0,0.0,0.0);
    vec3 colorB = vec3(0.0,0.0,1.0);

    vec2 center = vUv - 0.5; /* same as vec2(0.5, 0.5) */
    center.x *= aspect;
    float dist = length(center);

    vec3 color = mix(colorA, colorB, vUv.x);
    gl_FragColor = vec4(color, dist > 0.25 ? 0.0 : 1.0);
  }
`)

// Your sketch, which simply returns the shader
const sketch = ({ gl }) => {
  // Create the shader and return it

  return createShader({
    // Pass along WebGL context
    clearColor: '#fff',
    gl,
    // Specify fragment and/or vertex shader strings
    frag,
    // Specify additional uniforms to pass down to the shaders
    uniforms: {
      // Expose props from canvas-sketch
      time: ({ time }) => time,
      aspect: ({ width, height }) => width / height 
    }
  });
};
```
Masked circle 
```glsl
precision highp float;

uniform float time;
uniform float aspect;
varying vec2 vUv;

void main () {
  vec3 colorA = vec3(1.0,0.0,0.0);
  vec3 colorB = vec3(0.0,0.0,1.0);

  vec2 center = vUv - 0.5; /* same as vec2(0.5, 0.5) */
  center.x *= aspect;
  float dist = length(center);
  float alpha = smoothstep(0.4, 0.399, dist);
  vec3 color = mix(colorA, colorB, vUv.x);
  gl_FragColor = vec4(color, alpha);
}
```
Playing around
```glsl
precision highp float;

uniform float time;
uniform float aspect;
varying vec2 vUv;

void main () {
  vec3 colorA = sin(time) + vec3(1.0,0.0,0.0);
  vec3 colorB = vec3(0.0,0.0,1.0) - cos(time);

  vec2 center = vUv - 0.5; /* same as vec2(0.5, 0.5) */
  center.x *= aspect;
  float dist = length(center);
  float alpha = smoothstep(0.4, 0.399, dist);
  vec3 color = mix(colorA, colorB, vUv.y - vUv.x * vUv.x * vUv.x - vUv.y * vUv.x);
  gl_FragColor = vec4(color, alpha);
}
```