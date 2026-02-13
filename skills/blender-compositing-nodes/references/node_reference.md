# Blender 5.0 Compositor Nodes - Complete Reference

Approximately 70-80 compositor node implementations across 8 top-level categories.

## Socket Data Types

Color (RGBA), Float (Value), Vector, Shader (not used in compositor)

---

## 1. INPUT NODES

| Node           | Type String              | Description                                                                               |
| -------------- | ------------------------ | ----------------------------------------------------------------------------------------- |
| Render Layers  | CompositorNodeRLayers    | Outputs render result and all enabled render passes (Image, Alpha, Z, Normal, Mist, etc.) |
| Image          | CompositorNodeImage      | Loads a still image or image sequence                                                     |
| Movie Clip     | CompositorNodeMovieClip  | Loads a movie clip for compositing/tracking                                               |
| Mask           | CompositorNodeMask       | Loads a 2D mask from the mask editor                                                      |
| Texture        | CompositorNodeTexture    | Generates pattern from a Blender texture data block                                       |
| Color (RGB)    | CompositorNodeRGB        | Constant RGBA color value                                                                 |
| Value          | CompositorNodeValue      | Constant float value                                                                      |
| Bokeh Image    | CompositorNodeBokehImage | Generates a bokeh shape for custom DOF                                                    |
| Time Curve     | CompositorNodeTime       | Outputs a value that varies over time based on a curve                                    |
| Track Position | CompositorNodeTrackPos   | Outputs 2D tracking point position from a movie clip                                      |
| Scene Time     | CompositorNodeSceneTime  | Outputs current frame and seconds                                                         |

---

## 2. OUTPUT NODES

| Node         | Type String               | Description                                            |
| ------------ | ------------------------- | ------------------------------------------------------ |
| Composite    | CompositorNodeComposite   | Final composited output (what gets saved/displayed)    |
| Viewer       | CompositorNodeViewer      | Preview node for backdrop display                      |
| Split Viewer | CompositorNodeSplitViewer | Side-by-side comparison of two images                  |
| File Output  | CompositorNodeOutputFile  | Saves images/passes to disk (supports multi-layer EXR) |

---

## 3. COLOR NODES

| Node                 | Type String                   | Description                                                                 |
| -------------------- | ----------------------------- | --------------------------------------------------------------------------- |
| Mix                  | CompositorNodeMixRGB          | Blends two images with various modes (Add, Multiply, Screen, Overlay, etc.) |
| Alpha Over           | CompositorNodeAlphaOver       | Composites foreground over background using alpha                           |
| Color Balance        | CompositorNodeColorBalance    | Lift/Gamma/Gain or Offset/Power/Slope color correction                      |
| Bright/Contrast      | CompositorNodeBrightContrast  | Adjusts brightness and contrast                                             |
| Hue/Saturation/Value | CompositorNodeHueSat          | Adjusts hue, saturation, and value                                          |
| Color Correction     | CompositorNodeColorCorrection | Per-range (shadows, midtones, highlights) color correction                  |
| Curves (RGB)         | CompositorNodeCurveRGB        | RGB tone curve adjustment                                                   |
| Curves (Combined)    | CompositorNodeCurveVec        | Vector curves adjustment                                                    |
| Gamma                | CompositorNodeGamma           | Gamma correction                                                            |
| Invert               | CompositorNodeInvert          | Inverts colors and/or alpha                                                 |
| Posterize            | CompositorNodePosterize       | Reduces color levels                                                        |
| Tonemap              | CompositorNodeTonemap         | HDR tonemapping (Rh Simple or R/D Filmic)                                   |
| Z Combine            | CompositorNodeZcombine        | Combines images based on Z depth                                            |
| Combine Color        | CompositorNodeCombineColor    | Combines R, G, B, A into color (RGB, HSV, HSL, YCbCr, YUV modes)            |
| Separate Color       | CompositorNodeSeparateColor   | Separates color into components (RGB, HSV, HSL, YCbCr, YUV modes)           |
| Set Alpha            | CompositorNodeSetAlpha        | Replaces or multiplies alpha channel                                        |
| Combine XYZ          | CompositorNodeCombineXYZ      | Combines X, Y, Z floats into vector                                         |
| Separate XYZ         | CompositorNodeSeparateXYZ     | Splits vector into X, Y, Z floats                                           |

---

## 4. FILTER NODES

| Node             | Type String                 | Description                                                                      |
| ---------------- | --------------------------- | -------------------------------------------------------------------------------- |
| Blur             | CompositorNodeBlur          | Gaussian, Box, or other blur types                                               |
| Bilateral Blur   | CompositorNodeBilateralblur | Edge-preserving blur                                                             |
| Directional Blur | CompositorNodeDBlur         | Motion-style directional blur                                                    |
| Vector Blur      | CompositorNodeVecBlur       | Motion blur from speed/vector pass                                               |
| Bokeh Blur       | CompositorNodeBokehBlur     | Custom bokeh-shaped blur                                                         |
| Defocus          | CompositorNodeDefocus       | Depth-of-field blur using Z pass                                                 |
| Denoise          | CompositorNodeDenoise       | OpenImageDenoise denoiser                                                        |
| Despeckle        | CompositorNodeDespeckle     | Removes small noise speckles                                                     |
| Dilate/Erode     | CompositorNodeDilateErode   | Expands or contracts masks/mattes                                                |
| Filter           | CompositorNodeFilter        | Convolution filters (Sharpen, Soften, Laplacian, Sobel, Prewitt, Kirsch, Shadow) |
| Glare            | CompositorNodeGlare         | Bloom, Streaks, Fog Glow, Ghosts effects                                         |
| Inpaint          | CompositorNodeInpaint       | Fills transparent regions by extending surrounding pixels                        |
| Pixelate         | CompositorNodePixelate      | Pixelation effect                                                                |
| Sun Beams        | CompositorNodeSunBeams      | Volumetric sun ray effect                                                        |
| Anti-Aliasing    | CompositorNodeAntiAliasing  | SMAA anti-aliasing filter                                                        |
| Kuwahara         | CompositorNodeKuwahara      | Painterly/oil-painting style filter (new in recent versions)                     |

---

## 5. CONVERTER NODES

| Node                 | Type String                     | Description                                           |
| -------------------- | ------------------------------- | ----------------------------------------------------- |
| Map Range            | CompositorNodeMapRange          | Remaps a value from one range to another              |
| Map Value            | CompositorNodeMapValue          | Offsets, scales, and clamps a value                   |
| Color Ramp           | CompositorNodeValToRGB          | Maps a float value to a color gradient                |
| RGB to BW            | CompositorNodeRGBToBW           | Converts color to grayscale value                     |
| Math                 | CompositorNodeMath              | Mathematical operations on float values               |
| Alpha Convert        | CompositorNodePremulKey         | Converts between premultiplied and straight alpha     |
| Switch View          | CompositorNodeSwitchView        | Switches between stereoscopic views                   |
| ID Mask              | CompositorNodeIDMask            | Creates mask from Object Index or Material Index pass |
| Cryptomatte          | CompositorNodeCryptomatteV2     | Extracts mattes from Cryptomatte render passes        |
| Cryptomatte (Legacy) | CompositorNodeCryptomatte       | Legacy Cryptomatte node                               |
| Convert Color Space  | CompositorNodeConvertColorSpace | Converts between color spaces                         |

---

## 6. MATTE NODES

| Node             | Type String                  | Description                                             |
| ---------------- | ---------------------------- | ------------------------------------------------------- |
| Keying           | CompositorNodeKeying         | Full-featured chroma keying (green/blue screen removal) |
| Keying Screen    | CompositorNodeKeyingScreen   | Generates a keying screen from tracking data            |
| Channel Key      | CompositorNodeChannelMatte   | Keys based on channel difference                        |
| Chroma Key       | CompositorNodeChromaMatte    | Keys based on chroma (color) difference                 |
| Color Key        | CompositorNodeColorMatte     | Keys based on color similarity                          |
| Difference Key   | CompositorNodeDiffMatte      | Keys based on difference from reference image           |
| Distance Key     | CompositorNodeDistanceMatte  | Keys based on color distance                            |
| Luminance Key    | CompositorNodeLumaMatte      | Keys based on luminance/brightness                      |
| Box Mask         | CompositorNodeBoxMask        | Rectangular mask shape                                  |
| Ellipse Mask     | CompositorNodeEllipseMask    | Elliptical mask shape                                   |
| Double Edge Mask | CompositorNodeDoubleEdgeMask | Creates a gradient mask between inner and outer edges   |

---

## 7. VECTOR NODES

| Node      | Type String             | Description                                   |
| --------- | ----------------------- | --------------------------------------------- |
| Normal    | CompositorNodeNormal    | Dot product with a reference normal direction |
| Normalize | CompositorNodeNormalize | Normalizes values to 0-1 range                |
| Map UV    | CompositorNodeMapUV     | Distorts image based on UV coordinates        |

---

## 8. DISTORT NODES

| Node               | Type String                    | Description                                                |
| ------------------ | ------------------------------ | ---------------------------------------------------------- |
| Lens Distortion    | CompositorNodeLensdist         | Barrel/pincushion distortion and chromatic aberration      |
| Movie Distortion   | CompositorNodeMovieDistortion  | Applies/removes lens distortion from tracking data         |
| Translate          | CompositorNodeTranslate        | Moves image in X/Y                                         |
| Rotate             | CompositorNodeRotate           | Rotates image                                              |
| Scale              | CompositorNodeScale            | Scales image (Relative, Absolute, Scene Size, Render Size) |
| Flip               | CompositorNodeFlip             | Flips image horizontally and/or vertically                 |
| Crop               | CompositorNodeCrop             | Crops image to a region                                    |
| Transform          | CompositorNodeTransform        | Combined translate, rotate, scale                          |
| Stabilize 2D       | CompositorNodeStabilize        | Stabilizes footage using tracking data                     |
| Plane Track Deform | CompositorNodePlaneTrackDeform | Deforms image to match a tracked plane                     |
| Corner Pin         | CompositorNodeCornerPin        | Four-corner deformation                                    |
| Displace           | CompositorNodeDisplace         | Displaces pixels based on a vector input                   |

---

## Render Pass Outputs (from Render Layers Node)

When enabled in View Layer Properties, the Render Layers node exposes these outputs:

### Combined & Data

| Pass             | Output Name      | Description                    |
| ---------------- | ---------------- | ------------------------------ |
| Combined         | Image            | Final combined render          |
| Alpha            | Alpha            | Alpha channel                  |
| Z                | Depth            | Distance from camera           |
| Normal           | Normal           | Surface normals                |
| Mist             | Mist             | Distance-based fog factor      |
| Position         | Position         | World-space position           |
| Vector           | Speed            | Motion vectors for vector blur |
| UV               | UV               | UV coordinates                 |
| Object Index     | IndexOB          | Per-object index for ID Mask   |
| Material Index   | IndexMA          | Per-material index for ID Mask |
| Denoising Normal | Denoising Normal | Normal data for denoiser       |
| Denoising Albedo | Denoising Albedo | Albedo data for denoiser       |

### Light Passes

| Pass                  | Output Name | Description                     |
| --------------------- | ----------- | ------------------------------- |
| Diffuse Direct        | DiffDir     | Direct diffuse lighting         |
| Diffuse Indirect      | DiffInd     | Indirect diffuse lighting (GI)  |
| Diffuse Color         | DiffCol     | Diffuse surface color (albedo)  |
| Glossy Direct         | GlossDir    | Direct glossy/specular lighting |
| Glossy Indirect       | GlossInd    | Indirect glossy lighting        |
| Glossy Color          | GlossCol    | Glossy surface color            |
| Transmission Direct   | TransDir    | Direct transmission lighting    |
| Transmission Indirect | TransInd    | Indirect transmission lighting  |
| Transmission Color    | TransCol    | Transmission color              |
| Volume Direct         | VolumeDir   | Direct volume lighting          |
| Volume Indirect       | VolumeInd   | Indirect volume lighting        |
| Emission              | Emit        | Surface emission                |
| Environment           | Env         | Environment lighting            |
| Shadow                | Shadow      | Shadow pass                     |
| AO                    | AO          | Ambient occlusion               |

### Cryptomatte

| Pass     | Output Name    | Description             |
| -------- | -------------- | ----------------------- |
| Object   | CryptoObject   | Cryptomatte by object   |
| Material | CryptoMaterial | Cryptomatte by material |
| Asset    | CryptoAsset    | Cryptomatte by asset    |
