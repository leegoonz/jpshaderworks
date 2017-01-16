###Code generation options### 
- by default generated surface shader code tries to handle all possible lighting/shadowing/lightmap scenarios. However in some cases you know you won’t need some of them, and it is possible to adjust generated code to skip them. This can result in smaller shaders that are faster to load.

- **exclude_path:deferred, exclude_path:forward, exclude_path:prepass** - Do not generate passes for given rendering path (Deferred Shading, Forward and Legacy Deferred respectively).

- **noshadow** - Disables all shadow receiving support in this shader.

- **noambient** - Do not apply any ambient lighting or light probes.

- **novertexlights** - Do not apply any light probes or per-vertex lights in Forward rendering.

- **nolightmap** - Disables all lightmapping support in this shader.

- **nodynlightmap** - Disables runtime dynamic global illumination support in this shader.

- **nodirlightmap** - Disables directional lightmaps support in this shader.

- **nofog** - Disables all built-in Fog support.

- **nometa** - Does not generate a “meta” pass (that’s used by lightmapping & dynamic global illumination to extract surface information).

- **noforwardadd** - Disables Forward rendering additive pass. This makes the shader support one full directional light, with all other lights computed per-vertex/SH. Makes shaders smaller as well.


###Miscellaneous options###

- **softvegetation **- Makes the surface shader only be rendered when Soft Vegetation is on.

- **interpolateview **- Compute view direction in the vertex shader and interpolate it; instead of computing it in the pixel shader. This can make the pixel shader faster, but uses up one more texture interpolator.

- **halfasview **- Pass half-direction vector into the lighting function instead of view-direction. Half-direction will be computed and normalized per vertex. This is faster, but not entirely correct.

- **approxview **- Removed in Unity 5.0. Use interpolateview instead.
dualforward - Use dual lightmaps in forward rendering path.