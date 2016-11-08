WebGL Deferred Shading
======================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 5**

* (TODO) YOUR NAME HERE
* Tested on: (TODO) **Google Chrome 222.2** on
  Windows 22, i7-2222 @ 2.22GHz 22GB, GTX 222 222MB (Moore 2222 Lab)

### Live Online

[![](img/thumb.png)](http://159.203.88.91/WebGL-Deferred-Shading)

### Demo Video/GIF

[![](img/video.png)]

## Deferred Shading
In deferred shading, the lighting step is postponed to a later step. Instead, in the first pass through, all geometry data is accumulated into texture buffers known as G-buffers. This is then later on used in more complex lighting operations. The benefit of doing this is that all the fragments that are in the G-buffers will ultimately be used in a single lighting pass (the depth-test has already been applied to these fragments). This ensures that we dont run multiple lighting passes over objects that might never make it to a pixel.

## Shaders
### Default
The default lighting is simply a product of the attenuation and the color from the texture. The attenuation is the falloff of the light, which in our case is linear.

### Blinn-Phong
Blinn-phong is the standard baseline shader. https://www.wikiwand.com/en/Blinn%E2%80%93Phong_shading_model

### Ramp-Shading
Ramp shading is a non-photorealistic shader that "bands" together colors of similar value and rounds them to the nearest band. In the demo, you can tune the number of bands that are used. I did an experiment where I included ramp shading as a deferred shader and compared it to the post-processing version. Instead of banding the colors themselves, in this shader, I banded the lambertian and specular coefficients and then applied it to the colors. As a result, we have a more smoother transition between color bands. The main performance difference is negligible–the deferred shader computes the bands on the fly within that same shader.

## Post Process Effects
### Edge Highlights
Edge highlights are performed in two ways. One through a single-pass approximated sobel filter and one through a two-pass sobel filter. The latter method uses the standard horizontal and vertical sobel operators shown above. The latter combines the two as a component-wise sum. The rationale behind doing this combination is that the two passes are additively blended together and so we leverage the associativity and commutativity of the two sobel operators to combine them. This is not entirely accurate since the blending isn't necessarily factored in, but the end result looks nice and runs faster (by one shader load and execution).

### Ramp-Shading (Post-process)
In this version of the Ramp-shading, we are directly looking at the final result of the fragments and band the color values independently through their components.

### Depth of Field Blur
Similar to edge highlights, DoF is also applying a filter over the final fragments. There is a slider that adjusts the focus of the DoF.  Due to limitations of glsl/WebGL, I couldn't make the kernel size adjustable (no dynamic array allocation). The algorithm used to determine blurring rate is taking a focal value (which ranges from 0 to 1) and the depth value and using a quadratic falloff value as a standard deviation for a gaussian kernel. The reason we use a falloff value raised to a power is to widen the depth range that is within focus. We also needed to scale the value to have it map to reasonable standard deviation values.

### Credits

* [Three.js](https://github.com/mrdoob/three.js) by [@mrdoob](https://github.com/mrdoob) and contributors
* [stats.js](https://github.com/mrdoob/stats.js) by [@mrdoob](https://github.com/mrdoob) and contributors
* [webgl-debug](https://github.com/KhronosGroup/WebGLDeveloperTools) by Khronos Group Inc.
* [glMatrix](https://github.com/toji/gl-matrix) by [@toji](https://github.com/toji) and contributors
* [minimal-gltf-loader](https://github.com/shrekshao/minimal-gltf-loader) by [@shrekshao](https://github.com/shrekshao)
