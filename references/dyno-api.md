# Dyno Standard Library - Complete API Reference

The Dyno system is SparkJS's shader graph system that compiles JavaScript-style computation graphs to GLSL for GPU execution. Regular JavaScript operators (`+`, `-`, `*`) cannot be used; use `dyno.add()`, `dyno.sub()`, `dyno.mul()` etc. instead.

## Core Types

- `DynoVal<T>` - A value in the GPU computation graph
- `Dyno<InTypes, OutTypes>` - Abstract block with typed inputs/outputs
- `DynoBlock<InTypes, OutTypes>` - Module-like Dyno containing internal subgraphs

### Built-in DynoTypes

`"bool"`, `"int"`, `"uint"`, `"float"`, `"vec2"`, `"vec3"`, `"vec4"`, `"ivec2"`, `"ivec3"`, `"ivec4"`, `"uvec2"`, `"uvec3"`, `"uvec4"`, `"bvec2"`, `"bvec3"`, `"bvec4"`, `"mat2"`, `"mat3"`, `"mat4"`, `"sampler2D"`, `"sampler2DArray"`, `"sampler3D"`, `"samplerCube"`

### Custom Types

`Gsplat`, `TPackedSplats`, `SdfArray`, `TRgbaArray`, `SplatSkinning`

## Value Creation

```javascript
// Literal GLSL string
dynoLiteral("float", "1.0")
dynoLiteral("vec3", "vec3(1.0, 2.0, 3.0)")

// JS value to GLSL
dynoConst("float", 1.0)
dynoConst("vec3", new THREE.Vector3(1.0, 2.0, 3.0))
```

## DynoBlock Usage

```javascript
const myBlock = dyno.dynoBlock(
  { index: "int" },           // Input types
  { gsplat: "Gsplat" },       // Output types
  ({ index }) => {             // Closure
    let gsplat = dyno.readPackedSplat(myPackedSplats, index);
    gsplat = dyno.combineGsplat({ gsplat, opacity: 1.0 });
    return { gsplat };
  }
);
```

## Data Type Conversion

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| `bool(v)` | any | `bool` | Convert to boolean |
| `int(v)` | any | `int` | Convert to signed integer |
| `uint(v)` | any | `uint` | Convert to unsigned integer |
| `float(v)` | any | `float` | Convert to float |
| `bvecN(v)` | any | `bvecN` | Convert to boolean vector (N=2,3,4) |
| `ivecN(v)` | any | `ivecN` | Convert to signed int vector |
| `uvecN(v)` | any | `uvecN` | Convert to unsigned int vector |
| `vecN(v)` | any | `vecN` | Convert to float vector |
| `matN(v)` | any | `matN` | Convert to NxN matrix |
| `floatBitsToInt(v)` | `float` | `int` | Reinterpret float bits as int |
| `floatBitsToUint(v)` | `float` | `uint` | Reinterpret float bits as uint |
| `intBitsToFloat(v)` | `int` | `float` | Reinterpret int bits as float |
| `uintBitsToFloat(v)` | `uint` | `float` | Reinterpret uint bits as float |
| `packSnorm2x16(v)` | `vec2` | `uint` | Pack vec2 (-1..1) to uint32 |
| `unpackSnorm2x16(v)` | `uint` | `vec2` | Unpack uint32 to vec2 (-1..1) |
| `packUnorm2x16(v)` | `vec2` | `uint` | Pack vec2 (0..1) to uint32 |
| `unpackUnorm2x16(v)` | `uint` | `vec2` | Unpack uint32 to vec2 (0..1) |
| `packHalf2x16(v)` | `vec2` | `uint` | Pack vec2 as two float16 |
| `unpackHalf2x16(v)` | `uint` | `vec2` | Unpack uint32 as two float16 |
| `uintToRgba8(v)` | `uint` | `vec4` | Decode uint32 as 4x uint8 RGBA |

## Logic Operations

| Function | Description |
|----------|-------------|
| `and(a, b)` | Logical/bitwise AND |
| `or(a, b)` | Logical/bitwise OR |
| `xor(a, b)` | Logical/bitwise XOR |
| `not(a)` | Logical/bitwise NOT |
| `lessThan(a, b)` | Less than |
| `lessThanEqual(a, b)` | Less than or equal |
| `greaterThan(a, b)` | Greater than |
| `greaterThanEqual(a, b)` | Greater than or equal |
| `equal(a, b)` | Equal |
| `notEqual(a, b)` | Not equal |
| `any(bvec)` | True if any component is true |
| `all(bvec)` | True if all components are true |
| `select(cond, a, b)` | Ternary select |
| `compXor(a)` | Component-wise XOR |

## Math Operations

| Function | Description |
|----------|-------------|
| `add(a, b)` | Addition |
| `sub(a, b)` | Subtraction |
| `mul(a, b)` | Multiplication |
| `div(a, b)` | Division |
| `imod(a, b)` | Integer modulus |
| `mod(a, b)` | Float modulus |
| `modf(a)` | Separate fractional and integer parts |
| `neg(a)` | Negation |
| `abs(a)` | Absolute value |
| `sign(a)` | Sign (-1, 0, 1) |
| `floor(a)` | Floor |
| `ceil(a)` | Ceiling |
| `trunc(a)` | Truncate toward negative infinity |
| `round(a)` | Round to nearest integer |
| `fract(a)` | Fractional part |
| `pow(a, b)` | Power (a^b) |
| `exp(a)` | e^a |
| `exp2(a)` | 2^a |
| `log(a)` | Natural logarithm |
| `log2(a)` | Base-2 logarithm |
| `sqr(a)` | Square (a*a) |
| `sqrt(a)` | Square root |
| `inversesqrt(a)` | 1/sqrt(a) |
| `min(a, b)` | Minimum |
| `max(a, b)` | Maximum |
| `clamp(a, min, max)` | Clamp |
| `mix(a, b, t)` | Linear interpolation |
| `step(edge, x)` | Step function |
| `smoothstep(e0, e1, x)` | Smooth Hermite interpolation |
| `isNan(a)` | NaN check |
| `isInf(a)` | Infinity check |

## Trigonometry

| Function | Description |
|----------|-------------|
| `radians(deg)` | Degrees to radians |
| `degrees(rad)` | Radians to degrees |
| `sin(x)` | Sine |
| `cos(x)` | Cosine |
| `tan(x)` | Tangent |
| `asin(x)` | Arcsine |
| `acos(x)` | Arccosine |
| `atan(x)` | Arctangent |
| `atan2(y, x)` | Arctangent of y/x |
| `sinh(x)` | Hyperbolic sine |
| `cosh(x)` | Hyperbolic cosine |
| `tanh(x)` | Hyperbolic tangent |
| `asinh(x)` | Inverse hyperbolic sine |
| `acosh(x)` | Inverse hyperbolic cosine |
| `atanh(x)` | Inverse hyperbolic tangent |

## Linear Algebra

| Function | Description |
|----------|-------------|
| `length(a)` | Vector length |
| `distance(a, b)` | Distance between vectors |
| `dot(a, b)` | Dot product |
| `cross(a, b)` | Cross product (vec3 only) |
| `normalize(a)` | Normalize vector |
| `faceforward(a, b, c)` | Conditional flip |
| `reflectVec(i, n)` | Reflect around normal |
| `refractVec(i, n, eta)` | Refract through surface |
| `split(a)` | Split vector into components |
| `combine(...)` | Create vector from components |
| `projectH(a)` | Homogeneous projection |
| `extendVec(a, b)` | Extend vector by one component |
| `swizzle(a, select)` | Component selection |
| `compMult(a, b)` | Component-wise multiply |
| `outer(a, b)` | Outer product |
| `transpose(a)` | Matrix transpose |
| `determinant(a)` | Matrix determinant |
| `inverse(a)` | Matrix inverse |

## Texture Operations

| Function | Description |
|----------|-------------|
| `textureSize(tex, lod?)` | Get texture dimensions |
| `texture(tex, coord, bias?)` | Sample at continuous coordinate |
| `texelFetch(tex, coord, lod?)` | Fetch discrete texel |

## Transform Operations

| Function | Description |
|----------|-------------|
| `transformPos(pos, { scale, scales, rotate, translate })` | Transform 3D position |
| `transformDir(dir, { scale, scales, rotate })` | Transform 3D direction |
| `transformQuat(quat, { rotate })` | Rotate quaternion |

## Uniform Variables

Create GPU uniforms from JS values:

| Function | Description |
|----------|-------------|
| `dynoBool(v)` | Boolean uniform |
| `dynoInt(v)` | Signed int uniform |
| `dynoUint(v)` | Unsigned int uniform |
| `dynoFloat(v)` | Float uniform |
| `dynoBvecN(v)` | Boolean vector uniform |
| `dynoIvecN(v)` | Int vector uniform |
| `dynoUvecN(v)` | Uint vector uniform |
| `dynoVecN(v)` | Float vector uniform |
| `dynoMatN(v)` | Matrix uniform |
| `dynoSampler2D(tex)` | 2D float texture sampler |
| `dynoUsampler2D(tex)` | 2D uint texture sampler |
| `dynoIsampler2D(tex)` | 2D int texture sampler |
| `dynoSampler2DArray(tex)` | 2D float texture array sampler |
| `dynoSampler3D(tex)` | 3D float texture sampler |
| `dynoSamplerCube(tex)` | Cube float texture sampler |
| `dynoSampler2DShadow(tex)` | 2D shadow sampler |
| `dynoSamplerCubeShadow(tex)` | Cube shadow sampler |

## Hashing & Random Numbers

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| `pcgMix(v)` | scalar/vector | `uint` | Mix into PCG seed |
| `pcgNext(state)` | `uint` | `uint` | Advance PCG state |
| `pcgHash(state)` | `uint` | `uint` | Hash PCG state |
| `hash(v)` | scalar/vector | `uint` | Hash to uint32 |
| `hash2(v)` | scalar/vector | `uvec2` | Hash to uvec2 |
| `hash3(v)` | scalar/vector | `uvec3` | Hash to uvec3 |
| `hash4(v)` | scalar/vector | `uvec4` | Hash to uvec4 |
| `hashFloat(v)` | scalar/vector | `float` | Hash to float |
| `hashVec2(v)` | scalar/vector | `vec2` | Hash to vec2 |
| `hashVec3(v)` | scalar/vector | `vec3` | Hash to vec3 |
| `hashVec4(v)` | scalar/vector | `vec4` | Hash to vec4 |

## Splat Data Operations

| Function | Description |
|----------|-------------|
| `numPackedSplats(packed)` | Get splat count |
| `readPackedSplat(packed, index)` | Read splat by index |
| `readPackedSplatRange(packed, index, base, count)` | Read splat from range |
| `splitGsplat(gsplat)` | Split into components (center, scales, quaternion, opacity, color) |
| `combineGsplat({ gsplat?, center?, scales?, quaternion?, opacity?, color? })` | Create/modify Gsplat |
| `gsplatNormal(gsplat)` | Get normal (smallest-scale axis) |
| `transformGsplat(gsplat, { scale, rotate, translate, recolor })` | Transform Gsplat |
