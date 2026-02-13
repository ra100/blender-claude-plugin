# Blender 5.0 Shader Nodes - Complete Reference

Approximately 95 shader node implementations across 8 top-level categories. Supported in both Cycles and EEVEE render engines (with noted exceptions).

## Socket Types

Shader (Closure), Color (RGBA), Float, Vector, Normal, String, Image, Object

---

## 1. INPUT NODES

| Node               | Type String                | Description                                                                                                                                                   |
| ------------------ | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ambient Occlusion  | ShaderNodeAmbientOcclusion | Computes ambient occlusion at each surface point                                                                                                              |
| Attribute          | ShaderNodeAttribute        | Retrieves attributes (UV maps, vertex colors, custom attributes) by name                                                                                      |
| Bevel              | ShaderNodeBevel            | Computes rounded edge normals based on ray distance (Cycles only)                                                                                             |
| Camera Data        | ShaderNodeCameraData       | Outputs camera-relative data (view vector, view Z depth, view distance)                                                                                       |
| Color              | ShaderNodeRGB              | Constant RGBA color picker                                                                                                                                    |
| Curves Info        | ShaderNodeHairInfo         | Hair/curves rendering data (Is Strand, Intercept, Length, Thickness, etc.)                                                                                    |
| Fresnel            | ShaderNodeFresnel          | Computes Fresnel reflectance factor based on viewing angle and IOR                                                                                            |
| Geometry           | ShaderNodeNewGeometry      | Outputs geometric data (position, normal, tangent, true normal, incoming, parametric, backfacing, pointiness, random per island)                              |
| Layer Weight       | ShaderNodeLayerWeight      | Blend/facing weight for layering shaders (Blend and Facing outputs)                                                                                           |
| Light Path         | ShaderNodeLightPath        | Outputs booleans for current ray type (Is Camera, Is Shadow, Is Diffuse, Is Glossy, Is Singular, Is Reflection, Is Transmission, Ray Length, Ray Depth, etc.) |
| Object Info        | ShaderNodeObjectInfo       | Outputs object data (Location, Color, Alpha, Object Index, Random)                                                                                            |
| Particle Info      | ShaderNodeParticleInfo     | Outputs particle data (Index, Random, Age, Lifetime, Location, Size, Velocity, Angular Velocity)                                                              |
| Point Info         | ShaderNodePointInfo        | Outputs point cloud data (Position, Radius, Random)                                                                                                           |
| RGB                | ShaderNodeRGB              | Constant color output                                                                                                                                         |
| Tangent            | ShaderNodeTangent          | Outputs tangent direction for anisotropic shading (radial or UV map)                                                                                          |
| Texture Coordinate | ShaderNodeTexCoord         | Outputs various coordinate systems (Generated, Normal, UV, Object, Camera, Window, Reflection)                                                                |
| UV Map             | ShaderNodeUVMap            | Outputs specific UV map coordinates by name                                                                                                                   |
| Value              | ShaderNodeValue            | Constant float value                                                                                                                                          |
| Color Attribute    | ShaderNodeVertexColor      | Reads vertex color / color attribute data by name                                                                                                             |
| Volume Info        | ShaderNodeVolumeInfo       | Outputs volume data (Color, Density, Flame, Temperature)                                                                                                      |
| Wireframe          | ShaderNodeWireframe        | Outputs a mask for wireframe rendering (edge distance factor)                                                                                                 |
| UV Along Stroke    | ShaderNodeUVAlongStroke    | UV coordinates along Freestyle strokes (Line Style shading only)                                                                                              |
| Raycast            | ShaderNodeRaycast          | Casts a ray and returns hit information (Cycles only)                                                                                                         |

---

## 2. OUTPUT NODES

| Node              | Type String               | Description                                                  |
| ----------------- | ------------------------- | ------------------------------------------------------------ |
| Material Output   | ShaderNodeOutputMaterial  | Final material output with Surface, Volume, and Displacement |
| Light Output      | ShaderNodeOutputLight     | Light shader output                                          |
| World Output      | ShaderNodeOutputWorld     | World/environment shader output                              |
| AOV Output        | ShaderNodeOutputAOV       | Outputs custom render passes (Arbitrary Output Variables)    |
| Line Style Output | ShaderNodeOutputLineStyle | Freestyle line style output                                  |

---

## 3. SHADER NODES (BSDFs and Closures)

| Node                  | Type String                    | Description                                                                                                                                                                                                                                                                                                             |
| --------------------- | ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Principled BSDF       | ShaderNodeBsdfPrincipled       | All-in-one physically-based shader. Inputs: Base Color, Metallic, Roughness, IOR, Transmission, Subsurface Weight/Radius/Scale/IOR/Anisotropy, Specular IOR Level/Tint, Anisotropic/Rotation, Coat Weight/Roughness/IOR/Tint, Sheen Weight/Roughness/Tint, Emission Color/Strength, Alpha, Normal, Coat Normal, Tangent |
| Diffuse BSDF          | ShaderNodeBsdfDiffuse          | Lambertian diffuse reflection. Inputs: Color, Roughness, Normal                                                                                                                                                                                                                                                         |
| Glossy BSDF           | ShaderNodeBsdfGlossy           | Specular/glossy reflection (GGX, Beckmann, Ashikhmin-Shirley, Multi-GGX). Inputs: Color, Roughness, Anisotropy, Rotation, Normal, Tangent                                                                                                                                                                               |
| Glass BSDF            | ShaderNodeBsdfGlass            | Combined reflection + refraction. Inputs: Color, Roughness, IOR, Normal                                                                                                                                                                                                                                                 |
| Metallic BSDF         | ShaderNodeBsdfMetallic         | Physically-based metallic shader using complex IOR. Inputs: Base Color, Edge Tint, Roughness, Anisotropy, Rotation, Normal, Tangent (new in 5.0)                                                                                                                                                                        |
| Refraction BSDF       | ShaderNodeBsdfRefraction       | Pure refraction. Inputs: Color, Roughness, IOR, Normal                                                                                                                                                                                                                                                                  |
| Translucent BSDF      | ShaderNodeBsdfTranslucent      | Diffuse transmission through surface. Inputs: Color, Normal                                                                                                                                                                                                                                                             |
| Transparent BSDF      | ShaderNodeBsdfTransparent      | Pure transparency (no refraction). Inputs: Color                                                                                                                                                                                                                                                                        |
| Sheen BSDF            | ShaderNodeBsdfSheen            | Micro-surface detail reflection (cloth, dust). Inputs: Color, Roughness, Normal                                                                                                                                                                                                                                         |
| Subsurface Scattering | ShaderNodeSubsurfaceScattering | Light scattering beneath surface (skin, wax, marble). Inputs: Color, Scale, Radius, IOR, Roughness, Anisotropy, Normal                                                                                                                                                                                                  |
| Emission              | ShaderNodeEmission             | Light-emitting surface. Inputs: Color, Strength                                                                                                                                                                                                                                                                         |
| Hair BSDF             | ShaderNodeBsdfHair             | Hair/fur shading (reflection + transmission). Inputs: Color, Offset, Roughness U/V, Tangent                                                                                                                                                                                                                             |
| Principled Hair BSDF  | ShaderNodeBsdfHairPrincipled   | Physically-based hair shader. Inputs: Color, Melanin/Melanin Redness/Tint, Roughness, Radial Roughness, Coat, IOR, Offset, Random Roughness, Random Color, Random                                                                                                                                                       |
| Toon BSDF             | ShaderNodeBsdfToon             | Non-photorealistic toon shading (Diffuse or Glossy). Inputs: Color, Size, Smooth, Normal                                                                                                                                                                                                                                |
| Volume Absorption     | ShaderNodeVolumeAbsorption     | Light absorption within volume. Inputs: Color, Density                                                                                                                                                                                                                                                                  |
| Volume Scatter        | ShaderNodeVolumeScatter        | Light scattering within volume. Inputs: Color, Density, Anisotropy                                                                                                                                                                                                                                                      |
| Principled Volume     | ShaderNodeVolumePrincipled     | All-in-one volume shader. Inputs: Color, Color Attribute, Density, Density Attribute, Anisotropy, Absorption Color, Emission Strength, Emission Color, Blackbody Intensity, Temperature, Temperature Attribute                                                                                                          |
| Volume Coefficients   | ShaderNodeVolumeCoefficients   | Low-level volume coefficients for advanced volume control (new in 5.0)                                                                                                                                                                                                                                                  |
| Ray Portal BSDF       | ShaderNodeBsdfRayPortal        | Teleports rays to another location/direction (Cycles only). Inputs: Color, Position, Direction                                                                                                                                                                                                                          |
| Mix Shader            | ShaderNodeMixShader            | Blends two shaders by a factor. Inputs: Fac, Shader, Shader                                                                                                                                                                                                                                                             |
| Add Shader            | ShaderNodeAddShader            | Adds two shader outputs together. Inputs: Shader, Shader                                                                                                                                                                                                                                                                |
| Holdout               | ShaderNodeHoldout              | Makes surface a holdout mask (transparent in render, blocks light)                                                                                                                                                                                                                                                      |
| Background            | ShaderNodeBackground           | World background shader. Inputs: Color, Strength                                                                                                                                                                                                                                                                        |
| Specular BSDF (EEVEE) | ShaderNodeEeveeSpecular        | EEVEE-specific specular shader with direct control over specular color                                                                                                                                                                                                                                                  |

---

## 4. TEXTURE NODES

| Node                | Type String               | Description                                                                                                                                                 |
| ------------------- | ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Image Texture       | ShaderNodeTexImage        | Loads and samples an image texture. Settings: Interpolation, Projection, Extension                                                                          |
| Environment Texture | ShaderNodeTexEnvironment  | Loads HDR/EXR environment maps for world lighting                                                                                                           |
| Sky Texture         | ShaderNodeTexSky          | Procedural physically-based sky (Preetham, Hosek-Wilkie, Nishita). Inputs: Sun direction, Turbidity                                                         |
| Noise Texture       | ShaderNodeTexNoise        | Perlin/fBm noise. Inputs: Vector, Scale, Detail, Roughness, Lacunarity, Distortion. Outputs: Fac, Color                                                     |
| Voronoi Texture     | ShaderNodeTexVoronoi      | Voronoi/Worley cell patterns. Modes: F1, F2, Smooth F1, Distance to Edge, N-Sphere Radius. Inputs: Vector, Scale, Detail, Roughness, Lacunarity, Randomness |
| Wave Texture        | ShaderNodeTexWave         | Procedural bands or rings. Inputs: Vector, Scale, Distortion, Detail, Detail Scale, Detail Roughness, Phase Offset                                          |
| Musgrave Texture    | ShaderNodeTexMusgrave     | (Deprecated in 4.x+, merged into Noise Texture) fBm, Multifractal, Hetero Terrain, Hybrid, Ridged                                                           |
| Magic Texture       | ShaderNodeTexMagic        | Psychedelic turbulent patterns. Inputs: Vector, Scale, Distortion                                                                                           |
| Checker Texture     | ShaderNodeTexChecker      | Checkerboard pattern. Inputs: Vector, Color1, Color2, Scale                                                                                                 |
| Brick Texture       | ShaderNodeTexBrick        | Procedural brick wall pattern. Inputs: Vector, Color1, Color2, Mortar, Scale, Mortar Size, etc.                                                             |
| Gradient Texture    | ShaderNodeTexGradient     | Gradient patterns (Linear, Quadratic, Easing, Diagonal, Spherical, Radial)                                                                                  |
| White Noise Texture | ShaderNodeTexWhiteNoise   | Per-point random values. Dimensions: 1D, 2D, 3D, 4D. Outputs: Value, Color                                                                                  |
| Gabor Texture       | ShaderNodeTexGabor        | Directional anisotropic noise (new in recent versions). Inputs: Vector, Scale, Frequency, Anisotropy, Orientation                                           |
| IES Texture         | ShaderNodeTexIES          | IES light distribution profile for photometric lights                                                                                                       |
| Point Density       | ShaderNodeTexPointDensity | Generates texture from point cloud density (particles, vertices)                                                                                            |

---

## 5. COLOR NODES

| Node                 | Type String              | Description                                                                                                                                                                                |
| -------------------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Mix Color            | ShaderNodeMix            | Blends two colors using various modes (Mix, Darken, Multiply, Screen, Dodge, Burn, Add, Subtract, Overlay, Soft Light, Linear Light, Difference, Exclusion, Hue, Saturation, Value, Color) |
| Color Ramp           | ShaderNodeValToRGB       | Maps float to color via customizable gradient with multiple stops                                                                                                                          |
| RGB Curves           | ShaderNodeRGBCurve       | Per-channel curve adjustment (R, G, B, Combined)                                                                                                                                           |
| Invert Color         | ShaderNodeInvert         | Inverts color (1 - color). Inputs: Fac, Color                                                                                                                                              |
| Hue/Saturation/Value | ShaderNodeHueSaturation  | Adjusts hue, saturation, value, and fac of a color                                                                                                                                         |
| Brightness/Contrast  | ShaderNodeBrightContrast | Adjusts brightness and contrast                                                                                                                                                            |
| Gamma                | ShaderNodeGamma          | Applies gamma correction                                                                                                                                                                   |
| Light Falloff        | ShaderNodeLightFalloff   | Controls light attenuation curves (Quadratic, Linear, Constant)                                                                                                                            |
| Shader to RGB        | ShaderNodeShaderToRGB    | Converts shader/BSDF output to color (EEVEE only, enables toon shading)                                                                                                                    |
| Combine Color        | ShaderNodeCombineColor   | Combines channels into color (RGB, HSV, HSL modes)                                                                                                                                         |
| Separate Color       | ShaderNodeSeparateColor  | Splits color into channels (RGB, HSV, HSL modes)                                                                                                                                           |

---

## 6. VECTOR NODES

| Node                | Type String                  | Description                                                                                                                                                                                                                                                            |
| ------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Bump                | ShaderNodeBump               | Generates bump-mapped normals from a height texture. Inputs: Strength, Distance, Height, Normal                                                                                                                                                                        |
| Displacement        | ShaderNodeDisplacement       | Generates displacement for the Displacement output. Inputs: Height, Midlevel, Scale, Normal                                                                                                                                                                            |
| Vector Displacement | ShaderNodeVectorDisplacement | Vector-based displacement (Object, World, or Tangent space). Inputs: Vector, Midlevel, Scale                                                                                                                                                                           |
| Normal              | ShaderNodeNormal             | Generates a fixed normal direction with dot product output                                                                                                                                                                                                             |
| Normal Map          | ShaderNodeNormalMap          | Converts normal map image to shading normals (Tangent, Object, World, Blender Object, Blender World space)                                                                                                                                                             |
| Vector Transform    | ShaderNodeVectorTransform    | Transforms vectors between coordinate spaces (World, Object, Camera)                                                                                                                                                                                                   |
| Vector Curves       | ShaderNodeVectorCurve        | Remaps vector components using curve widgets                                                                                                                                                                                                                           |
| Vector Math         | ShaderNodeVectorMath         | Vector operations: Add, Subtract, Multiply, Divide, Cross Product, Project, Reflect, Dot Product, Distance, Length, Scale, Normalize, Snap, Floor, Ceil, Modulo, Fraction, Absolute, Minimum, Maximum, Wrap, Sine, Cosine, Tangent, Refract, Faceforward, Multiply Add |
| Vector Rotate       | ShaderNodeVectorRotate       | Rotates a vector around an axis (Axis Angle, X/Y/Z Axis, Euler)                                                                                                                                                                                                        |
| Mapping             | ShaderNodeMapping            | Transforms texture coordinates (Point, Texture, Vector, Normal). Inputs: Vector, Location, Rotation, Scale                                                                                                                                                             |
| Radial Tiling       | ShaderNodeRadialTiling       | Tiles textures in a radial pattern (new in recent versions)                                                                                                                                                                                                            |

---

## 7. CONVERTER NODES

| Node           | Type String             | Description                                                                                                                                                                                                                                                                                                                                                                                                                             |
| -------------- | ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Math           | ShaderNodeMath          | Float math operations: Add, Subtract, Multiply, Divide, Multiply Add, Power, Logarithm, Square Root, Inverse Square Root, Absolute, Exponent, Minimum, Maximum, Less Than, Greater Than, Sign, Compare, Smooth Minimum, Smooth Maximum, Round, Floor, Ceil, Truncate, Fraction, Modulo, Floored Modulo, Wrap, Snap, Ping-Pong, Sine, Cosine, Tangent, Arcsine, Arccosine, Arctangent, Arctan2, Sinh, Cosh, Tanh, To Radians, To Degrees |
| Color Ramp     | ShaderNodeValToRGB      | Maps float to color gradient (also listed under Color)                                                                                                                                                                                                                                                                                                                                                                                  |
| Combine XYZ    | ShaderNodeCombineXYZ    | Combines three floats into a vector                                                                                                                                                                                                                                                                                                                                                                                                     |
| Separate XYZ   | ShaderNodeSeparateXYZ   | Splits vector into X, Y, Z float components                                                                                                                                                                                                                                                                                                                                                                                             |
| Combine Color  | ShaderNodeCombineColor  | Combines channels into color (RGB, HSV, HSL)                                                                                                                                                                                                                                                                                                                                                                                            |
| Separate Color | ShaderNodeSeparateColor | Splits color into channel floats (RGB, HSV, HSL)                                                                                                                                                                                                                                                                                                                                                                                        |
| Map Range      | ShaderNodeMapRange      | Remaps value from one range to another (Linear, Stepped, Smooth Step, Smoother Step)                                                                                                                                                                                                                                                                                                                                                    |
| Float Curve    | ShaderNodeFloatCurve    | Remaps float using a curve widget                                                                                                                                                                                                                                                                                                                                                                                                       |
| Clamp          | ShaderNodeClamp         | Restricts value between min and max (Min Max or Range)                                                                                                                                                                                                                                                                                                                                                                                  |
| RGB to BW      | ShaderNodeRGBToBW       | Converts color to grayscale luminance value                                                                                                                                                                                                                                                                                                                                                                                             |
| Shader to RGB  | ShaderNodeShaderToRGB   | Converts shader to color (EEVEE only)                                                                                                                                                                                                                                                                                                                                                                                                   |
| Blackbody      | ShaderNodeBlackbody     | Converts temperature (Kelvin) to RGB color                                                                                                                                                                                                                                                                                                                                                                                              |
| Wavelength     | ShaderNodeWavelength    | Converts light wavelength (nm) to RGB color                                                                                                                                                                                                                                                                                                                                                                                             |
| Mix            | ShaderNodeMix           | Interpolates between values (Float, Vector, Color, Rotation modes)                                                                                                                                                                                                                                                                                                                                                                      |

---

## 8. SCRIPT NODE

| Node   | Type String      | Description                                                                                 |
| ------ | ---------------- | ------------------------------------------------------------------------------------------- |
| Script | ShaderNodeScript | Open Shading Language (OSL) script node (Cycles CPU only, GPU supported in recent versions) |

---

## 9. UTILITY / GROUP NODES

These appear in the shader node Add menu as utility entries for organization and advanced workflows.

| Node / Zone      | Description                                                                 |
| ---------------- | --------------------------------------------------------------------------- |
| Repeat Zone      | Looping construct for iterative shader computations                         |
| Closure Zone     | Encapsulates a shader closure for modular reuse (new in 5.0)                |
| Evaluate Closure | Evaluates a closure created by a Closure Zone (new in 5.0)                  |
| Combine Bundle   | Combines multiple values into a bundle (new in 5.0)                         |
| Separate Bundle  | Extracts values from a bundle (new in 5.0)                                  |
| Join Bundle      | Joins bundles together (new in 5.0)                                         |
| Menu Switch      | Menu-driven switch between multiple inputs (`GeometryNodeMenuSwitch`)       |
| Node Group       | User-defined reusable node groups                                           |
| Frame            | Visual grouping frame for organizing nodes (`NodeFrame`)                    |
| Reroute          | Route a connection through a point for cleaner graph layout (`NodeReroute`) |

---

## Principled BSDF - Detailed Input Reference

The most commonly used shader node. All inputs:

### Surface

| Input      | Type   | Default         | Description                                             |
| ---------- | ------ | --------------- | ------------------------------------------------------- |
| Base Color | Color  | (0.8, 0.8, 0.8) | Diffuse/albedo color                                    |
| Metallic   | Float  | 0.0             | 0 = dielectric, 1 = metal (uses Base Color as specular) |
| Roughness  | Float  | 0.5             | Microsurface roughness (0 = mirror, 1 = fully rough)    |
| IOR        | Float  | 1.5             | Index of refraction                                     |
| Alpha      | Float  | 1.0             | Surface opacity (0 = transparent, 1 = opaque)           |
| Normal     | Vector | -               | Surface normal (connect Normal Map or Bump)             |
| Weight     | Float  | 0.0             | Blend weight when layering with other shaders           |

### Subsurface

| Input                 | Type   | Default     | Description                              |
| --------------------- | ------ | ----------- | ---------------------------------------- |
| Subsurface Weight     | Float  | 0.0         | Amount of subsurface scattering          |
| Subsurface Radius     | Vector | (1,0.2,0.1) | Per-channel scattering radius (RGB)      |
| Subsurface Scale      | Float  | 0.05        | Scale of subsurface scattering effect    |
| Subsurface IOR        | Float  | 1.4         | IOR for subsurface scattering            |
| Subsurface Anisotropy | Float  | 0.0         | Direction bias for subsurface scattering |

### Specular

| Input                | Type   | Default | Description                                   |
| -------------------- | ------ | ------- | --------------------------------------------- |
| Specular IOR Level   | Float  | 0.5     | Specular reflection intensity relative to IOR |
| Specular Tint        | Color  | White   | Tints specular reflection                     |
| Anisotropic          | Float  | 0.0     | Amount of anisotropy for specular reflection  |
| Anisotropic Rotation | Float  | 0.0     | Rotation of anisotropic direction             |
| Tangent              | Vector | -       | Tangent direction for anisotropy              |

### Transmission

| Input               | Type  | Default | Description                                 |
| ------------------- | ----- | ------- | ------------------------------------------- |
| Transmission Weight | Float | 0.0     | Amount of light transmitted through surface |

### Coat

| Input          | Type   | Default | Description                         |
| -------------- | ------ | ------- | ----------------------------------- |
| Coat Weight    | Float  | 0.0     | Clear coat layer intensity          |
| Coat Roughness | Float  | 0.03    | Roughness of the clear coat layer   |
| Coat IOR       | Float  | 1.5     | IOR of the coat layer               |
| Coat Tint      | Color  | White   | Color tint of the coat layer        |
| Coat Normal    | Vector | -       | Normal direction for the coat layer |

### Sheen

| Input           | Type  | Default | Description                                |
| --------------- | ----- | ------- | ------------------------------------------ |
| Sheen Weight    | Float | 0.0     | Sheen layer intensity (fabric/velvet look) |
| Sheen Roughness | Float | 0.5     | Roughness of sheen reflection              |
| Sheen Tint      | Color | White   | Color of the sheen layer                   |

### Emission

| Input             | Type  | Default | Description                |
| ----------------- | ----- | ------- | -------------------------- |
| Emission Color    | Color | Black   | Color of emitted light     |
| Emission Strength | Float | 1.0     | Intensity of emitted light |

---

## Render Engine Compatibility

| Feature               | Cycles                        | EEVEE (Next)                   |
| --------------------- | ----------------------------- | ------------------------------ |
| Principled BSDF       | Full support                  | Full support                   |
| Metallic BSDF         | Full support                  | Full support                   |
| Subsurface Scattering | Full (Random Walk)            | Approximated (screen-space)    |
| Shader to RGB         | Not supported                 | Supported (toon/NPR workflows) |
| Bevel node            | Supported                     | Not supported                  |
| Raycast node          | Supported                     | Not supported                  |
| Ray Portal BSDF       | Supported                     | Not supported                  |
| Toon BSDF             | Supported                     | Not supported                  |
| OSL Script            | Supported (CPU + GPU in 4.x+) | Not supported                  |
| Specular BSDF (EEVEE) | Not supported                 | Supported                      |
| Volume rendering      | Full volumetrics              | Basic support                  |
| Adaptive Displacement | Supported (Experimental)      | Not supported (use Normal Map) |

### EEVEE Specific Limitations

- Diffuse BSDF: Roughness not supported (Lambertian only)
- Glossy BSDF: Only GGX distribution
- No true volumetric shadows (approximated)
- Max 8 Object/Instancer attributes per material
- Max 512 View Layer attributes per scene

---

## Blender 4.x/5.0 New Shader Features

### New Nodes (4.0+)

| Node                | Version | Details                                                                                                                  |
| ------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------ |
| Sheen BSDF          | 4.0     | Standalone velvet/cloth sheen shader (previously only a parameter in Principled BSDF)                                    |
| Ray Portal BSDF     | 4.0     | Teleports rays to another location for portals, periscopes, artistic effects (Cycles only)                               |
| Repeat Zone         | 4.0+    | Loop construct for iterative computation in shader graphs                                                                |
| Gabor Texture       | 4.2     | Anisotropic procedural noise for directional patterns (brushed metal, wood grain, fabric)                                |
| Metallic BSDF       | 4.x     | Dedicated metallic shader using complex IOR / edge tint, more physically accurate than Principled BSDF with Metallic=1.0 |
| Volume Coefficients | 4.x/5.0 | Low-level volume node with direct coefficient control over absorption, scattering, emission                              |
| Radial Tiling       | 4.x/5.0 | Tiles textures radially, outputs segment coordinates for radially symmetric patterns                                     |
| Closure Zone        | 5.0     | New closure encapsulation system for modular shader building                                                             |
| Bundle Nodes        | 5.0     | Combine Bundle, Separate Bundle, Join Bundle for grouped data passing                                                    |
| Evaluate Closure    | 5.0     | Evaluates closures created by Closure Zones                                                                              |

### Significant Changes

- **Principled BSDF**: Major refactor in 4.0 with new socket layout, renamed inputs (Subsurface Weight replaces Subsurface, Coat replaces Clearcoat), improved energy conservation
- **Mix node** (`ShaderNodeMix`): Replaced legacy MixRGB node with unified float/vector/color/rotation mixing
- **Combine/Separate Color**: Replaced old Combine/Separate RGB/HSV nodes with unified node supporting RGB, HSV, HSL modes
- **Voronoi Texture**: Gained N-Sphere Radius output and Smooth F1 feature
- **Musgrave Texture**: Fully deprecated (functionality merged into Noise Texture Detail/Roughness/Lacunarity controls)
- **OSL on GPU**: Expanded GPU support for OSL scripts in Cycles
- **EEVEE Next**: New EEVEE rendering engine in 4.x/5.0 supports more shader nodes than legacy EEVEE
