# SparkRenderer - Complete Constructor Options

## Required

| Parameter | Type | Description |
|-----------|------|-------------|
| `renderer` | `THREE.WebGLRenderer` | WebGL renderer instance. Use `antialias: false` for best performance |

## Optional - Rendering

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `premultipliedAlpha` | `boolean` | `true` | Premultiplied alpha for RGB accumulation |
| `encodeLinear` | `boolean` | `false` | Linear RGB encoding for environment maps |
| `clock` | `THREE.Clock` | `new THREE.Clock()` | Time synchronization |
| `autoUpdate` | `boolean` | `true` | Auto-update splat collection each frame |
| `preUpdate` | `boolean` | `true` | Update before rendering (`false` for WebXR) |
| `maxStdDev` | `number` | `Math.sqrt(8)` | Max standard deviations for Gaussian rendering |
| `minPixelRadius` | `number` | `0.0` | Minimum splat pixel radius |
| `maxPixelRadius` | `number` | `512.0` | Maximum splat pixel radius |
| `accumExtSplats` | `boolean` | `false` | Extended encoding for intermediary splats |
| `covSplats` | `boolean` | `false` | Covariance encoding (experimental, needs accumExtSplats) |
| `minAlpha` | `number` | `0.5/255` | Minimum alpha threshold |
| `enable2DGS` | `boolean` | `false` | 2D Gaussian splatting mode |
| `preBlurAmount` | `number` | `0.0` | 2D splat covariance diagonal addition |
| `blurAmount` | `number` | - | Anti-aliasing blur with opacity adjust |
| `focalDistance` | `number` | `0.0` | Depth-of-field focal plane distance |
| `apertureAngle` | `number` | `0.0` | Full-width aperture angle (radians) |
| `falloff` | `number` | `1.0` | Gaussian kernel falloff (0=flat, 1=normal) |
| `clipXY` | `number` | `1.4` | X/Y clipping boundary factor |
| `focalAdjustment` | `number` | `1.0` | Projected splat scale (2.0 matches PlayCanvas) |

## Optional - Sorting

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `sortRadial` | `boolean` | `true` | Sort by distance (true) or Z-depth (false) |
| `minSortIntervalMs` | `number` | `0` | Minimum ms between sort calls |

## Optional - Level-of-Detail

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `enableLod` | `boolean` | `true` | Enable LoD rendering |
| `enableDriveLod` | `boolean` | `true` | Drive LoD updates |
| `lodSplatCount` | `number` | platform | Target splat count budget |
| `lodSplatScale` | `number` | `1.0` | Budget multiplier |
| `lodRenderScale` | `number` | `1.0` | Min screen pixel size for LoD splats |
| `pagedExtSplats` | `boolean` | `false` | Extended encoding for paged splats |
| `maxPagedSplats` | `number` | platform | Paged allocation (multiple of 65536) |
| `numLodFetchers` | `number` | `3` | Parallel chunk fetchers (max 4) |

## Optional - Foveation

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `coneFov0` | `number` | `90.0` | Full-detail cone angle (degrees) |
| `coneFov` | `number` | `120.0` | Reduced-detail cone angle (degrees) |
| `coneFoveate` | `number` | `0.4` | Foveation scale at coneFov edge |
| `behindFoveate` | `number` | `0.2` | Foveation scale behind viewer |

## Optional - Render Target

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `target.width` | `number` | - | Offline render target width |
| `target.height` | `number` | - | Offline render target height |
| `target.doubleBuffer` | `boolean` | `false` | Double buffering |
| `target.superXY` | `number` | `1` | Super-sampling factor (1-4) |

## Optional - Custom Shaders

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `extraUniforms` | `object` | - | Extra shader uniform values |
| `vertexShader` | `string` | - | Custom vertex shader |
| `fragmentShader` | `string` | - | Custom fragment shader |
| `transparent` | `boolean` | `true` | Render in transparent pass |
| `depthTest` | `boolean` | `true` | Enable depth testing |
| `depthWrite` | `boolean` | `false` | Enable depth writing |

## Instance Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `dispose()` | `void` | Free all resources |
| `update({ scene, camera })` | `Promise` | Manually update, awaitable for sort |
| `render(scene, camera)` | `void` | Render to canvas |
| `renderTarget({ scene, camera })` | `void` | Render to offline target |
| `readTarget()` | `Uint8Array` | Read target as packed RGBA |
| `renderReadTarget({ scene, camera })` | `Uint8Array` | Render + read combined |
| `renderCubeMap({ scene, worldCenter })` | `void` | Render to cube map |
| `readCubeTargets()` | `array` | Read 6 cube faces |
| `renderEnvMap({ scene, worldCenter })` | `THREE.Texture` | Render environment map |
| `recurseSetEnvMap(root, envMap)` | `void` | Set envMap on MeshStandardMaterial tree |

## Instance Properties

| Property | Type | Description |
|----------|------|-------------|
| `time` | `number` | Current time |
| `deltaTime` | `number` | Frame delta |
| `autoUpdate` | `boolean` | Auto-update toggle |

## Platform Default lodSplatCount

| Platform | Budget |
|----------|--------|
| Quest/Oculus | 500,000 |
| Vision Pro | 750,000 |
| Android | 1,000,000 |
| iOS | 1,500,000 |
| Desktop | 2,500,000 |
