---
name: sparkjs
description: Build 3D Gaussian Splat applications using the SparkJS library (@sparkjsdev/spark). This skill should be used when users want to create, modify, or debug web-based 3D Gaussian splatting scenes with Three.js integration, including loading splat files, procedural splat generation, splat editing with SDF shapes, Level-of-Detail streaming, custom Dyno shaders, and performance optimization.
---

# SparkJS - 3D Gaussian Splat Rendering Library

SparkJS is a JavaScript library for rendering and manipulating 3D Gaussian splats in web browsers. It integrates with Three.js to provide high-performance visualization of splat-based 3D models.

- Documentation: https://sparkjs.dev/docs/
- Current version: 2.0.0
- CDN: `https://sparkjs.dev/releases/spark/2.0.0/spark.module.js`
- npm: `@sparkjsdev/spark`

## When to Use

Use this skill when:
- Creating a new 3D Gaussian splatting web application
- Loading and displaying `.ply`, `.spz`, `.splat`, `.ksplat`, `.sog`, `.rad` splat files
- Generating procedural splats (grids, spheres, text, images, particles)
- Editing splats with SDF shapes (lighting, deletion, deformation)
- Implementing Level-of-Detail (LoD) for large splat scenes
- Writing custom Dyno shader graphs for GPU computation
- Optimizing splat rendering performance
- Adding camera/navigation controls (FPS, pointer, gamepad)

## Quick Start Template

Use this as a starting point for any SparkJS project:

```html
<style> body { margin: 0; } </style>
<script type="importmap">
  {
    "imports": {
      "three": "https://cdnjs.cloudflare.com/ajax/libs/three.js/0.180.0/three.module.js",
      "@sparkjsdev/spark": "https://sparkjs.dev/releases/spark/2.0.0/spark.module.js"
    }
  }
</script>
<script type="module">
  import * as THREE from "three";
  import { SparkRenderer, SplatMesh } from "@sparkjsdev/spark";

  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(60, innerWidth / innerHeight, 0.01, 1000);
  const renderer = new THREE.WebGLRenderer();
  renderer.setSize(innerWidth, innerHeight);
  document.body.appendChild(renderer.domElement);

  const spark = new SparkRenderer({ renderer });
  scene.add(spark);

  const splat = new SplatMesh({ url: "https://sparkjs.dev/assets/splats/butterfly.spz" });
  splat.quaternion.set(1, 0, 0, 0);
  splat.position.set(0, 0, -3);
  scene.add(splat);

  renderer.setAnimationLoop(function animate(time) {
    renderer.render(scene, camera);
  });
</script>
```

## Core Architecture

### SparkRenderer

The main renderer, added to the Three.js scene. Required for all splat rendering.

```javascript
const spark = new SparkRenderer({
  renderer,                    // THREE.WebGLRenderer (required)
  maxStdDev: Math.sqrt(8),     // Gaussian render extent
  sortRadial: true,            // true=distance, false=Z-depth
  enableLod: true,             // Level-of-Detail
  lodSplatScale: 1.0,          // LoD detail multiplier
  pagedExtSplats: false,       // Extended encoding for paged splats
  enable2DGS: false,           // 2D Gaussian splatting
});
scene.add(spark);
```

Key methods:
- `dispose()` - Free resources
- `update({ scene, camera })` - Manual splat update (awaitable)
- `render(scene, camera)` - Render to canvas
- `renderTarget({ scene, camera })` - Render to offline target
- `readTarget()` - Read target as Uint8Array RGBA
- `renderEnvMap({ scene, worldCenter })` - Render environment map

### SplatMesh

Represents a splat model. Subclass of `THREE.Object3D`.

```javascript
const splat = new SplatMesh({
  url: "./model.spz",          // Load from URL
  // OR
  packedSplats: myPackedSplats, // From PackedSplats instance
  // OR
  constructSplats: (splats) => { /* procedural */ },

  maxSplats: 100000,           // Reserve capacity
  extSplats: false,            // Float32 precision
  lod: true,                   // Generate LoD
  paged: true,                 // Streaming from .rad

  onProgress: (e) => {},       // Download progress
  onLoad: (mesh) => {},        // Init complete
  onFrame: ({ mesh, time, deltaTime }) => {}, // Per-frame
});
scene.add(splat);
```

Key properties:
- `position`, `quaternion`, `rotation`, `scale` - Transform (uniform scale only)
- `recolor: THREE.Color` - Tint multiplier
- `opacity: number` - Global opacity (0-1)
- `maxSh: number` - Spherical Harmonics level (0-3)
- `initialized: Promise<SplatMesh>` - Await initialization
- `numSplats: number` - Current splat count

Key methods:
- `pushSplat(center, scales, quaternion, opacity, color)` - Add a splat
- `forEachSplat(callback)` - Iterate all splats (read-only)
- `getBoundingBox(centersOnly)` - Get bounding box
- `raycast(raycaster, intersects)` - Three.js raycasting
- `dispose()` - Free resources

### PackedSplats

Efficient 16-byte-per-splat data structure.

```javascript
const packed = new PackedSplats({
  url: "./model.ply",
  // OR
  construct: (splats) => {
    splats.pushSplat(center, scales, quat, opacity, color);
  },
  maxSplats: 1000,
});
```

Methods: `getSplat(i)`, `setSplat(i, ...)`, `pushSplat(...)`, `forEachSplat(cb)`, `getTexture()`, `dispose()`

### ExtSplats

32-byte-per-splat format with float32 center coordinates for large scenes.

```javascript
const ext = new ExtSplats({ url: "./large-scene.ply", extSplats: true });
```

## Splat Editing (SDF)

Apply real-time edits using Signed Distance Field shapes:

```javascript
import { SplatEdit, SplatEditSdf, SplatEditSdfType, SplatEditRgbaBlendMode } from "@sparkjsdev/spark";

const edit = new SplatEdit({
  rgbaBlendMode: SplatEditRgbaBlendMode.MULTIPLY,
  sdfSmooth: 0.1,
  softEdge: 0.05,
  sdfs: [
    new SplatEditSdf({
      type: SplatEditSdfType.SPHERE,
      radius: 0.5,
      opacity: 0.0,       // Delete splats in sphere
      color: new THREE.Color(1, 1, 1),
    })
  ]
});
edit.position.set(0, 1, 0);
splatMesh.add(edit);       // Attach as child
```

SDF shape types: `ALL`, `PLANE`, `SPHERE`, `BOX`, `ELLIPSOID`, `CYLINDER`, `CAPSULE`, `INFINITE_CONE`
Blend modes: `MULTIPLY`, `SET_RGB`, `ADD_RGBA`

## Procedural Splats

Built-in constructors for common patterns:

```javascript
import { constructGrid, constructAxes, constructSpherePoints, textSplats, imageSplats } from "@sparkjsdev/spark";
```

- `constructGrid({ ... })` - Regular grid lattice
- `constructAxes({ scale })` - XYZ axis visualization
- `constructSpherePoints({ maxDepth })` - Sphere subdivision
- `textSplats({ text, font, size, color })` - Text to splats
- `imageSplats({ url, subsample })` - Image to splats
- `generators.staticBox(...)` - Random particles in volume
- `generators.snowBox(...)` / `DEFAULT_SNOW` / `DEFAULT_RAIN` - Particle effects

## Controls

```javascript
import { SparkControls } from "@sparkjsdev/spark";

const controls = new SparkControls({
  canvas: renderer.domElement,
  moveSpeed: 1.0,
  rotateSpeed: 2.0,
});

// In animation loop:
controls.update(deltaTime, { position: camera.position, quaternion: camera.quaternion });
```

## Level-of-Detail (LoD)

### Runtime LoD
```javascript
const splat = new SplatMesh({ url: "./model.spz", lod: true });
```

### Pre-built LoD (recommended)
```bash
npm run build-lod -- model.ply --quality
```
```javascript
const splat = new SplatMesh({ url: "./model-lod.rad", paged: true });
```

### Tuning
- `SparkRenderer.lodSplatCount` - Target budget (platform-dependent)
- `SparkRenderer.lodSplatScale` - Budget multiplier
- `SplatMesh.lodScale` - Per-object detail factor

## Performance Tips

- Set `antialias: false` on `THREE.WebGLRenderer`
- For VR: use `maxStdDev: Math.sqrt(5)`
- Splat budgets: Quest 3 ~1M, phones ~1-3M, desktop ~1-20M
- Use LoD and foveation for large scenes
- Evaluate `devicePixelRatio` vs. performance tradeoff

## Dyno Shader System

For custom GPU computation, refer to `references/dyno-api.md` for the full Dyno stdlib (117+ functions). Basic pattern:

```javascript
import { dyno } from "@sparkjsdev/spark";

const myGenerator = dyno.dynoBlock(
  { index: "int" },
  { gsplat: "Gsplat" },
  ({ index }) => {
    let gsplat = dyno.readPackedSplat(packedSplats.dyno, index);
    gsplat = dyno.combineGsplat({ gsplat, opacity: 1.0 });
    return { gsplat };
  }
);
```

## Key Imports

```javascript
import {
  SparkRenderer,
  SplatMesh,
  PackedSplats,
  ExtSplats,
  SplatEdit,
  SplatEditSdf,
  SplatEditSdfType,
  SplatEditRgbaBlendMode,
  SparkControls,
  dyno,
  utils,
  constructGrid,
  constructAxes,
  constructSpherePoints,
  textSplats,
  imageSplats,
  generators,
} from "@sparkjsdev/spark";
```
