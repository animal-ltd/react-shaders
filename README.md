# **React Shaders** [![lusat minzip package size](https://img.shields.io/bundlephobia/minzip/react-shaders?label=zipped)](https://www.npmjs.com/package/react-shaders) [![lusat package version](https://img.shields.io/npm/v/react-shaders.svg?colorB=green)](https://www.npmjs.com/package/react-shaders) [![lusat license](https://img.shields.io/npm/l/react-shaders.svg?colorB=lightgrey)](https://github.com/rysanacom/react-shaders/blob/main/LICENSE)

Modern GLSL/WebGL bindings & components for React and Typescript.

```bash
npm i react-shaders
```

<p align="center">
  <hr />
  <p align="center">
    <a href="https://rysana.com/log?topic=react-shaders">Log</a>
    ·
    <a href="https://rysana.com/docs/react-shaders">Website</a>
    ·
    <a href="https://rysana.com/docs/react-shaders">Docs</a>
    ·
    <a href="https://rysana.com">Rysana</a>
  </p>
  <hr />
</p>

## Usage

**React Shaders** is [Rysana's](https://rysana.com) open source library for creating GLSL/WebGL shaders in React and Typescript, with support for modern shader bindings like those in Shadertoy and Rysana. `react-shaders` is built on the combined work of [Morgan Villedieu's `shadertoy-react`](https://github.com/mvilledieu/shadertoy-react) and some modifications by [Rysana](https://rysana.com).

You can install Lusat with `npm`, `pnpm`, or `yarn`:

```bash
npm i react-shaders
```

```bash
pnpm i react-shaders
```

```bash
yarn add react-shaders
```

## `<Shader />` component

The `<Shader />` component is a lightweight React component for creating responsive, full canvas shaders through WebGL to any `react-dom` app. It supports the [Shadertoy](https://www.shadertoy.com)/[Rysana](https://rysana.com/shader-playground) GLSL syntax, as well as the classic GLSL syntax, allowing you to easily copy and paste shaders from Shadertoy or Rysana Shader Playground into your applications with no need to modify the code.

It also supports a wide range of built-in uniforms like playback time, resolution, mouse position, and even phone gyroscope position, and allows you to pass custom uniforms as props.

### How it works

`<Shader />` uses WebGL on a `<canvas />` and renders a material on a full viewport quad composed of 2 triangles. The canvas size matches the CSS size of your element, by default it at `100% 100%` of your parent element size, but this can be changed by passing a custom CSS `style={}` prop to your component. It also makes sure that anything that is not used in your shader is not activated in JavaScript to avoid useless event listeners, etc. so if you don't use the `iMouse` uniform the mouse event listener will not be activated and the `iMouse` uniform will not be added and passed to your shader.

### Playground

We're working on a more direct playground for this library, but you can try out the underlying library on [Rysana Shader Playground](https://rysana.com/shader-playground) for the time being.

### How to use it

#### Basic example

```jsx
import { Shader } from 'react-shaders'

const Example = <Shader fs={fs} />
```

Example of working shader with Modern syntax:

```glsl
void mainImage(out vec4 fragColor, in vec2 fragCoord) {
  vec2 uv = fragCoord.xy/iResolution.xy;
  vec3 col = .5 + .5*cos(iTime+uv.xyx+vec3(0,2,4));
  fragColor = vec4(col,1.);
}
```

Example of working shader with classic GLSL syntax:

```glsl
void main(void) {
  vec2 uv = gl_FragCoord.xy/iResolution.xy;
  vec3 col = .5 + .5*cos(iTime+uv.xyx+vec3(0,2,4));
  gl_FragColor = vec4(col,1.);
}
```

### Available props

Here are a few built in react props you can pass to your component. Feel free to suggest more.

- `fs` -- A string containing your fragment shader.
- `textures` -- An array of textures objects following that structure `{url: ... , minFilter: , magFilter: , wrapS: , wrapT: }` the format supported are (`.jpg, .jpeg, .png, .bmp`) for images, and (`.mp4, .3gp, .webm, .ogv`) for videos. Textures needs to be squared otherwise they will be automatically squared for you. `minFilter`, `magFilter`, `wrapS`, `wrapT` are optionals, by default their values are set to `{url: ... , minFilter: LinearMipMapLinearFilter, magFilter: LinearFilter, wrapS: ReapeatWrapping, wrapT: ReapeatWrapping}`.
- `uniforms` -- An object containing uniforms objects following that structure `{ uTest: {type: , value: }, uTest2: {type: , value: }, uTest3: {type: , value: } ... }`. To know more about the supported types please refer to the [custom uniforms section](#Custom-uniforms).
- `clearColor` -- An array `[red, green, blue, alpha]` Specifies the color values used when clearing color buffers method of the [WebGL API](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/clearColor) by default `[0, 0, 0, 1]`.
- `precision` -- GLSL Precision qualifier, by default highp, it can be lowp, mediump, highp depending on how much precision the GPU uses when calculating floats.
- `devicePixelRatio` -- A value passed to set the [pixel density](https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio) of the canvas. By default `1`.
- `contextAttributes` -- To customize your [WebGL context attributes.](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/getContext)
- `style` -- Pass a [React Inline style](https://reactjs.org/docs/dom-elements.html#style) to customize the style of your canvas.
- `onDoneLoadingTextures` -- Callback called once all textures are done being loaded, usefull when you want to wait for your shader to have all the needed texture before seeing it on screen. Using that callback you could for example simply fade the canvas in using css.
- `lerp` -- A value in between `0` - `1` used to lerp the mouse position in your fragment shader.

## Uniforms

#### Shadertoy built-ins:

Built in uniforms are uniforms that are being passed automatically to your shader without having you doing anything. You can start using every single one of them without having to do anything. We are taking care of that for you.

- `uniform float iTime` -- Shader playback time (in seconds).
- `uniform float iTimeDelta` -- Render time (in seconds).
- `uniform int iFrame` -- Shader playback frame.
- `uniform vec2 iResolution` -- Viewport resolution (in pixels).
- `uniform vec4 iDate` -- (Year, month, day, time in seconds).
- `uniform vec4 iMouse` -- Mouse pixel coords. xy: current (if MLB down), zw: click.
- `uniform sampler2D iChannel^n` -- The textures input channel you've passed; numbered in the same order as the textures passed as prop in your react component.
- `uniform vec3 iChannelResolution[n]` -- An array containing the texture channel resolution (in pixels).

#### Other built-ins:

- `uniform vec4 iDeviceOrientation` -- Raw data from [device orientation](https://developer.mozilla.org/en-US/docs/Web/API/Detecting_device_orientation) where respectively x: Alpha, y: Beta, z: Gamma and w: [window.orientation](https://developer.mozilla.org/en-US/docs/Web/API/Window/orientation).
- `#define DPR 1.0` -- The canvas device pixel ratio (1.0 by default or props.devicePixelRatio).

#### Custom uniforms:

You can add your own uniforms by passing a `uniforms` prop containing uniform objects. Here is a list of the supported uniforms and their respective formats. **_Note:_** If you want to pass multiple Vectors, Matrices, Ints, Floats, make sure to pass flat arrays as shown below.

| Type        | GLSL Type                | Uniforms values in JS                                                                                                                                                                                                                          | Read in GLSL                                    |
| ----------- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| `1f`        | `float`                  | `val`                                                                                                                                                                                                                                          | `uValue`                                        |
| `2f`        | `vec2`                   | `[x, y]`                                                                                                                                                                                                                                       | `uValue.xy`                                     |
| `3f`        | `vec3`                   | `[x, y, z]`                                                                                                                                                                                                                                    | `uValue.xyz`                                    |
| `4f`        | `vec4`                   | `[x, y, z, w]`                                                                                                                                                                                                                                 | `uValue.xyzw`                                   |
| `1fv`       | `float` or `float` array | `val` or `[val, val, ...]`                                                                                                                                                                                                                     | `uValue` or `uValue[n]`                         |
| `2fv`       | `vec2` or `vec2` array   | `[x, y]` or `[x, y, x, y, ...]`                                                                                                                                                                                                                | `uValue.xy` or `uValue[n].xy`                   |
| `3fv`       | `vec3` or `vec3` array   | `[x, y, z]` or `[x, y, z, x, y, z, ...]`                                                                                                                                                                                                       | `uValue.xyz` or `uValue[n].xyz`                 |
| `4fv`       | `vec4` or `vec4` array   | `[x, y, z, w]` or `[x, y, z, w, x, y, z, w ...]`                                                                                                                                                                                               | `uValue.xyzw` or `uValue[n].xyzw`               |
| `1i`        | `int`                    | `val`                                                                                                                                                                                                                                          | `uValue`                                        |
| `2i`        | `ivec2`                  | `[x, y]`                                                                                                                                                                                                                                       | `uValue.xy`                                     |
| `3i`        | `ivec3`                  | `[x, y, z]`                                                                                                                                                                                                                                    | `uValue.xyz`                                    |
| `4i`        | `ivec4`                  | `[x, y, z, w]`                                                                                                                                                                                                                                 | `uValue.xyzw`                                   |
| `1iv`       | `int` or `int` array     | `val` or `[val, val, val, ...]`                                                                                                                                                                                                                | `uValue` or `uValue[n]`                         |
| `2iv`       | `ivec2` or `ivec2` array | `[x, y]` or `[x, y, x, y, ...]`                                                                                                                                                                                                                | `uValue.xy` or `uValue[n].xy`                   |
| `3iv`       | `ivec3` or `ivec3` array | `[x, y, z]` or `[x, y, z, x, y, z, ...]`                                                                                                                                                                                                       | `uValue.xyz` or `uValue[n].xyz`                 |
| `4iv`       | `ivec4` or `ivec4` array | `[x, y, z, w]` or `[x, y, z, w, x, y, z, w ...]`                                                                                                                                                                                               | `uValue.xyzw` or `uValue[n].xyzw`               |
| `Matrix2fv` | `mat2` or `mat2` array   | `[m00, m01, m10, m11]` or `[m00, m01, m10, m11, m00, m01, m10, m11 ...]`                                                                                                                                                                       | `uValue[0->1][0->1]` or `uValue[n][0->1][0->1]` |
| `Matrix3fv` | `mat3` or `mat3` array   | `[m00, m01, m02, m10, m11, m12, m20, m21, m22]` or `[m00, m01, m02, m10, m11, m10, m12, m20, m21, m22, m00, m01, m02, m10, m11, m10, m12, m20, m21, m22 ...]`                                                                                  | `uValue[0->2][0->2]` or `uValue[n][0->2][0->2]` |
| `Matrix4fv` | `mat4` or `mat4` array   | `[m00, m01, m02, m03, m10, m11, m10, m12, m20, m21, m22, m30, m31, m32, m33]` or `[m00, m01, m02, m03, m10, m11, m10, m12, m20, m21, m22, m30, m31, m32, m33,  m00, m01, m02, m03, m10, m11, m10, m12, m20, m21, m22, m30, m31, m32, m33 ...]` | `uValue[0->3][0->3]` or `uValue[n][0->3][0->3]` |

How to do it:

```jsx
import { Shader } from 'react-shaders'

const Example = () => (
  <Shader
    fs={fs}
    uniforms={{
      // A single float
      uScrollY: { type: '1f', value: scrollY },
      // Five floats in an array
      uTestArrayFloats: {
        type: '1fv',
        value: [0.2, 0.4, 0.5, 0.5, 0.6],
      },
      // Two vec2s in an array
      uTestArrayVecs2: { type: '2fv', value: [0.2, 0.4, 0.5, 0.5] },
      // A single mat2
      uTestMatrix: {
        type: 'Matrix2fv',
        value: [0, 1, 2, 3],
      },
    }}
  />
)
```

Example of shader you could write using these custom uniforms:

```glsl
  void mainImage(out vec4 fragColor, in vec2 fragCoord) {
    // You can then directly use uScrollY, uTestMatrix, uTestArrayFloats without having to worry about anything else.
    gl_FragColor = vec4(uScrollY, uTestMatrix[0][0], uTestArrayFloats[0], uTestArrayVecs2[0].xy);
  }
```

#### Working with textures:

You can pass an array of texture objects. A callback is available and called once all the textures are done loading. Each texture gets a uniform name `iChannel{n}` following the same order that in the prop passed to the react component, you can then directly use `iChannel{n}` in your shader.

```jsx
import React from 'react'
import { LinearFilter, RepeatWrapping, Shader } from 'react-shaders'

const Example = () => (
  <Shader fs={fs} textures={[{ url: './my-texture.png' }]} />
)
```

In your shader you can directly do for example:

```glsl
void mainImage(out vec4 fragColor, in vec2 fragCoord) {
  vec2 uv = fragCoord.xy / iResolution.xy;
  vec4 texture = texture(iChannel0, uv);
  gl_FragColor = texture;
}
```

##### Texture Filtering:

By default all of your textures are being squared if they aren't, then the default Texture Filtering and Wrapping are being applied to them, using `react-shaders` you can apply your own filters. `react-shaders` contains all the WebGL texture filtering constants and texture wrapping constants. So you can easily import them in your code and make sure to pass the right one to your texture options.

**Example of optionnal texture related imports:**

```jsx
import {
  ClampToEdgeWrapping,
  LinearFilter,
  LinearMipMapLinearFilter,
  LinearMipMapNearestFilter,
  MirroredRepeatWrapping,
  NearestFilter,
  NearestMipMapLinearFilter,
  NearestMipMapNearestFilter,
  RepeatWrapping,
  Shaders,
} from 'react-shaders'
```

**Example of usage of optionnal texture filtering:**

```jsx
import React from 'react'
import { LinearFilter, RepeatWrapping, Shader } from 'react-shaders'

const Example = () => (
  <Shader
    fs={fs}
    textures={[
      {
        url: './my-texture-1.png',
        minFilter: LinearFilter,
        magFilter: LinearFilter,
        wrapS: RepeatWrapping,
        wrapT: RepeatWrapping,
      },
      {
        url: './my-texture-2.png',
        minFilter: LinearFilter,
        magFilter: LinearFilter,
        wrapS: RepeatWrapping,
        wrapT: RepeatWrapping,
      },
    ]}
  />
)
```

## What's next ordered by priority (`shadertoy-react`)

- Module Support for props IntelliSense.
- Dynamically load new texture when textures props changes.
- Add lazy loading logic with 1x 2x 3x etc. so your shader can receive `<img />` like src files.
- Add support for #define constantes in shader from prop.
- Add camera feed as a texture.
- Add support for Data texture.
- Add support for WebGL2 and GLSL 3.0.
- Add support to multi passes as Shadertoy is doing.
- Add support for Cube texture.
- Add support for keyboard uniforms / inputs.
- Add support for iChannelTime.
- ~~Add possibility to specify gl clearColor in a prop~~ v1.0.4
- ~~Add shader precision as react prop.~~ v1.0.2
- ~~Add support for classic syntax (void main(void)) etc.~~ v1.0.2
- ~~Add support for custom uniforms.~~ v1.0.1
- ~~Add props for optionnal mouse lerping.~~ v1.0.0
- ~~Add built in uniform for phone device orientation / gyroscope based effects.~~ v1.0.0
- ~~Add support for iDate.~~ v1.0.0
- ~~Add support for video textures.~~ v1.0.0
- ~~Add support for iChannelResolution.~~ v1.0.0

## Contributors

### Credits to original authors:

- **Morgan Villedieu** [@mvilledieu](https://github.com/mvilledieu): Code, creator of [shadertoy-react](https://github.com/mvilledieu/shadertoy-react)
- **Charlie Hoey** [@flimshaw](https://github.com/flimshaw): Code, contributed to [shadertoy-react](https://github.com/mvilledieu/shadertoy-react)
- **Iñigo Quilez & Pol Jeremias**: Shadertoy GL syntax, creators of [Shadertoy](https://www.shadertoy.com)

### Credits to contributors:

- **John** [@jrysana](https://github.com/jrysana): Code
- **aremorov** [@aremorov](https://github.com/aremorov): Code
