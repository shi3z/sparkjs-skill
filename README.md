# sparkjs-skill

A [Claude Code](https://claude.ai/claude-code) skill that adds comprehensive knowledge of the [SparkJS](https://sparkjs.dev/) 3D Gaussian Splat rendering library.

## Overview

SparkJS is a JavaScript library integrated with Three.js for rendering and manipulating 3D Gaussian Splats in web browsers. With this skill installed, Claude Code understands the full SparkJS API, design patterns, and optimization techniques, enabling accurate code generation and debugging.

## Features Covered

- Full API for **SparkRenderer** / **SplatMesh** / **PackedSplats** / **ExtSplats**
- Loading splat files in `.ply`, `.spz`, `.splat`, `.ksplat`, `.sog`, `.rad` formats
- **SDF-based splat editing** (sphere, box, plane, ellipsoid, cylinder, capsule, cone)
- **Procedural splat generation** (grid, text, image, particles)
- **Level-of-Detail (LoD)** streaming and tuning
- **Dyno shader graph system** (117+ stdlib functions reference included)
- **SparkControls** (FPS / pointer / gamepad / mobile touch)
- Per-platform performance optimization guide

## Installation

### Method 1: git clone (recommended)

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/shi3z/sparkjs-skill.git ~/.claude/skills/sparkjs
```

### Method 2: Per-project setup

Clone into your project's `.claude/skills/` directory to scope the skill to a specific project:

```bash
mkdir -p .claude/skills
git clone https://github.com/shi3z/sparkjs-skill.git .claude/skills/sparkjs
```

### Method 3: Manual copy

Download `SKILL.md` and the `references/` directory, then place them at `~/.claude/skills/sparkjs/`:

```
~/.claude/skills/sparkjs/
├── SKILL.md
└── references/
    ├── dyno-api.md
    └── spark-renderer-options.md
```

### Updating

```bash
cd ~/.claude/skills/sparkjs && git pull
```

## File Structure

```
sparkjs/
├── SKILL.md                           # Main skill definition
│   ├── Quick Start template
│   ├── Core API reference
│   ├── SDF editing & procedural generation
│   ├── LoD, performance & controls
│   └── Dyno shader overview
└── references/
    ├── dyno-api.md                    # Dyno stdlib full function reference
    └── spark-renderer-options.md      # SparkRenderer all constructor options
```

## Usage

Once installed, the skill triggers automatically when you ask Claude Code anything related to SparkJS.

### Example Prompts

- "Create a page that displays a butterfly model with SparkJS"
- "Add raycasting to a SplatMesh"
- "Delete splats inside a sphere region using SDF"
- "Enable LoD streaming to improve performance"
- "Write a custom Dyno shader"

## Quick Start

Minimal SparkJS code:

```html
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

  renderer.setAnimationLoop(() => renderer.render(scene, camera));
</script>
```

## License

MIT
