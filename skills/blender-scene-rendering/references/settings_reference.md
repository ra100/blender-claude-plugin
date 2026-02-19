# Blender Scene & Rendering Settings Reference

## Render Engines

| Engine               | Type String              | Description                         |
| -------------------- | ------------------------ | ----------------------------------- |
| Cycles               | `'CYCLES'`               | Path tracer, production quality     |
| EEVEE                | `'BLENDER_EEVEE'`        | Real-time, ray tracing support      |
| Workbench            | `'BLENDER_WORKBENCH'`    | Solid/preview, technical drawing    |

## Cycles Device Types

| Device   | Type String   | Description                       |
| -------- | ------------- | --------------------------------- |
| CUDA     | `'CUDA'`      | NVIDIA (older)                    |
| OptiX    | `'OPTIX'`     | NVIDIA (RTX acceleration)         |
| HIP      | `'HIP'`       | AMD                               |
| Metal    | `'METAL'`     | Apple Silicon / macOS             |
| oneAPI   | `'ONEAPI'`    | Intel Arc                         |

## Image Output Formats

| Format              | Type String                | Channels   | Depth          | Notes                        |
| ------------------- | -------------------------- | ---------- | -------------- | ---------------------------- |
| PNG                 | `'PNG'`                    | RGB/RGBA   | 8, 16          | Lossless, supports alpha     |
| JPEG                | `'JPEG'`                   | RGB        | 8              | Lossy, no alpha              |
| OpenEXR             | `'OPEN_EXR'`               | RGB/RGBA   | 16, 32 float   | HDR, VFX standard            |
| OpenEXR Multilayer  | `'OPEN_EXR_MULTILAYER'`    | RGB/RGBA   | 16, 32 float   | Multiple render passes       |
| TIFF                | `'TIFF'`                   | RGB/RGBA   | 8, 16          | Print, no compression loss   |
| BMP                 | `'BMP'`                    | RGB        | 8              | Uncompressed                 |
| HDR (Radiance)      | `'HDR'`                    | RGB        | 32 float       | Environment maps             |
| WebP                | `'WEBP'`                   | RGB/RGBA   | 8              | Web, lossy/lossless          |

## EXR Codecs

| Codec   | Type String   | Description                            |
| ------- | ------------- | -------------------------------------- |
| None    | `'NONE'`      | Uncompressed                           |
| Pxr24   | `'PXR24'`     | Lossy for float, lossless for half     |
| ZIP     | `'ZIP'`       | Lossless, scanline blocks              |
| PIZ     | `'PIZ'`       | Lossless, best for grainy images       |
| RLE     | `'RLE'`       | Lossless, run-length encoding          |
| ZIPS    | `'ZIPS'`      | Lossless, single scanline              |
| DWAA    | `'DWAA'`      | Lossy, 32-scanline blocks, small files |
| DWAB    | `'DWAB'`      | Lossy, 256-scanline blocks             |

## Video Formats (FFmpeg)

| Container  | Type String    | Common Codecs                |
| ---------- | -------------- | ---------------------------- |
| MPEG-4     | `'MPEG4'`      | H.264, MPEG4                 |
| MKV        | `'MKV'`        | H.264, FFV1, VP9             |
| WebM       | `'WEBM'`       | VP9, AV1                     |
| AVI        | `'AVI'`        | HuffYUV, FFV1, raw           |
| QuickTime  | `'QUICKTIME'`  | H.264, ProRes                |

## Video Codecs

| Codec    | Type String   | Quality        | Notes                        |
| -------- | ------------- | -------------- | ---------------------------- |
| H.264    | `'H264'`      | Lossy          | Universal, good compression  |
| MPEG-4   | `'MPEG4'`     | Lossy          | Older, wider compatibility   |
| WebM/VP9 | `'WEBM'`      | Lossy          | Web standard                 |
| HuffYUV  | `'HUFFYUV'`   | Lossless       | Fast, large files            |
| DNxHD    | `'DNXHD'`     | Lossy/Visually | Broadcast/editing            |
| FFV1     | `'FFV1'`      | Lossless       | Archival                     |
| PNG      | `'PNG'`       | Lossless       | Huge files, frame sequences  |

## Audio Codecs

| Codec   | Type String   | Notes                   |
| ------- | ------------- | ----------------------- |
| None    | `'NONE'`      | No audio                |
| AAC     | `'AAC'`       | Standard, good quality  |
| AC3     | `'AC3'`       | Surround sound          |
| FLAC    | `'FLAC'`      | Lossless                |
| MP2     | `'MP2'`       | Legacy                  |
| MP3     | `'MP3'`       | Universal, lossy        |
| Opus    | `'OPUS'`      | Modern, efficient       |
| PCM     | `'PCM'`       | Uncompressed            |
| Vorbis  | `'VORBIS'`    | Open source, WebM       |

## Constant Rate Factor (H.264/H.265)

| Quality          | Type String          | File Size | Use Case              |
| ---------------- | -------------------- | --------- | --------------------- |
| Lossless         | `'LOSSLESS'`         | Huge      | Master archive        |
| Perc. Lossless   | `'PERC_LOSSLESS'`    | Very large| Near-lossless         |
| High             | `'HIGH'`             | Large     | High quality delivery |
| Medium           | `'MEDIUM'`           | Moderate  | Default, balanced     |
| Low              | `'LOW'`              | Small     | Preview, web          |
| Lowest           | `'LOWEST'`           | Tiny      | Quick preview         |

## Color Management

### Display Devices

| Device   | Description               |
| -------- | ------------------------- |
| `sRGB`   | Standard monitors         |
| `None`   | No display transform      |

### View Transforms

| Transform       | Type String        | Use Case                                    |
| --------------- | ------------------ | ------------------------------------------- |
| AgX             | `'AgX'`            | Default 4.0+/5.x, wide dynamic range       |
| Filmic          | `'Filmic'`         | Legacy, photorealistic scenes               |
| Standard        | `'Standard'`       | Direct sRGB, UI, non-photorealistic         |
| Raw             | `'Raw'`            | No transform, data visualization            |
| False Color     | `'False Color'`    | Exposure/lighting analysis                  |

### AgX Looks

| Look              | Type String          | Effect                    |
| ----------------- | -------------------- | ------------------------- |
| None              | `'None'`             | Neutral                   |
| Punchy            | `'AgX - Punchy'`     | Higher contrast           |
| Golden            | `'AgX - Golden'`     | Warm tone                 |
| Base Contrast     | `'AgX - Base Contrast'` | Subtle contrast boost  |

### Filmic Looks

| Look              | Type String                     | Effect                  |
| ----------------- | ------------------------------- | ----------------------- |
| None              | `'None'`                        | Neutral Filmic          |
| Very Low Contrast | `'Filmic - Very Low Contrast'`  | Flat, maximum latitude  |
| Low Contrast      | `'Filmic - Low Contrast'`       | Gentle rolloff          |
| Medium Contrast   | `'Filmic - Medium Contrast'`    | Balanced                |
| High Contrast     | `'Filmic - High Contrast'`      | Punchy                  |
| Very High Contrast| `'Filmic - Very High Contrast'` | Maximum contrast        |

## Unit Systems

| System    | Type String    | Base Unit       |
| --------- | -------------- | --------------- |
| None      | `'NONE'`       | Blender units   |
| Metric    | `'METRIC'`     | Meters          |
| Imperial  | `'IMPERIAL'`   | Feet            |

### Length Units (Metric)

| Unit         | Type String        |
| ------------ | ------------------ |
| Adaptive     | `'ADAPTIVE'`       |
| Kilometers   | `'KILOMETERS'`     |
| Meters       | `'METERS'`         |
| Centimeters  | `'CENTIMETERS'`    |
| Millimeters  | `'MILLIMETERS'`    |
| Micrometers  | `'MICROMETERS'`    |

## Import/Export Formats

### Import Operators

| Format    | Operator                         | Key Options                                         |
| --------- | -------------------------------- | --------------------------------------------------- |
| FBX       | `bpy.ops.import_scene.fbx()`    | `filepath`, `use_custom_normals`, `global_scale`    |
| glTF/GLB  | `bpy.ops.import_scene.gltf()`   | `filepath`, `merge_vertices`, `import_shading`      |
| OBJ       | `bpy.ops.wm.obj_import()`       | `filepath`, `global_scale`, `forward_axis`          |
| USD       | `bpy.ops.wm.usd_import()`       | `filepath`, `import_guide`, `import_cameras`        |
| Alembic   | `bpy.ops.wm.alembic_import()`   | `filepath`, `scale`, `as_background_job`            |
| STL       | `bpy.ops.wm.stl_import()`       | `filepath`, `global_scale`, `forward_axis`          |
| SVG       | `bpy.ops.import_curve.svg()`    | `filepath`                                          |

### Export Operators

| Format    | Operator                         | Key Options                                                     |
| --------- | -------------------------------- | --------------------------------------------------------------- |
| FBX       | `bpy.ops.export_scene.fbx()`    | `filepath`, `use_selection`, `apply_scale_options`, `bake_anim` |
| glTF/GLB  | `bpy.ops.export_scene.gltf()`   | `filepath`, `export_format`, `use_selection`, `export_apply`    |
| OBJ       | `bpy.ops.wm.obj_export()`       | `filepath`, `export_selected_objects`, `apply_modifiers`        |
| USD       | `bpy.ops.wm.usd_export()`       | `filepath`, `selected_objects_only`, `export_animation`         |
| Alembic   | `bpy.ops.wm.alembic_export()`   | `filepath`, `selected`, `start`, `end`                          |
| STL       | `bpy.ops.wm.stl_export()`       | `filepath`, `export_selected_objects`, `apply_modifiers`        |

### FBX Axis Options

| Option          | Values                                    | Default  |
| --------------- | ----------------------------------------- | -------- |
| `axis_forward`  | `X`, `Y`, `Z`, `-X`, `-Y`, `-Z`          | `-Z`     |
| `axis_up`       | `X`, `Y`, `Z`, `-X`, `-Y`, `-Z`          | `Y`      |

### FBX Scale Options

| Option                  | Type String           | Description                          |
| ----------------------- | --------------------- | ------------------------------------ |
| All Local               | `'FBX_SCALE_ALL'`     | Apply custom scale to all            |
| FBX Units Scale         | `'FBX_SCALE_UNITS'`   | Use FBX unit scaling                 |
| FBX Custom Scale        | `'FBX_SCALE_CUSTOM'`  | Use custom scaling factor            |
| FBX Scale None          | `'FBX_SCALE_NONE'`    | No scaling                           |

### glTF Export Formats

| Format          | Type String          | Description                       |
| --------------- | -------------------- | --------------------------------- |
| GLB (binary)    | `'GLB'`              | Single binary file                |
| glTF Separate   | `'GLTF_SEPARATE'`    | .gltf + .bin + textures           |
| glTF Embedded   | `'GLTF_EMBEDDED'`    | Single JSON with embedded data    |

## Viewport Shading Types

| Type      | Type String    | Description                          |
| --------- | -------------- | ------------------------------------ |
| Wireframe | `'WIREFRAME'`  | Edges only                           |
| Solid     | `'SOLID'`      | Flat/studio lighting, fast           |
| Material  | `'MATERIAL'`   | EEVEE preview of materials           |
| Rendered  | `'RENDERED'`   | Full render engine preview           |

### Solid Mode Lighting

| Light    | Type String   | Description            |
| -------- | ------------- | ---------------------- |
| Studio   | `'STUDIO'`    | Studio light presets   |
| MatCap   | `'MATCAP'`    | Material capture       |
| Flat     | `'FLAT'`      | No lighting            |

### Solid Mode Color Types

| Color    | Type String    | Description              |
| -------- | -------------- | ------------------------ |
| Material | `'MATERIAL'`   | Material diffuse color   |
| Single   | `'SINGLE'`     | Single color for all     |
| Object   | `'OBJECT'`     | Per-object color         |
| Random   | `'RANDOM'`     | Random per object        |
| Vertex   | `'VERTEX'`     | Vertex colors            |
| Texture  | `'TEXTURE'`    | Active texture           |

## Scene Copy Types

| Type       | Type String     | Description                                  |
| ---------- | --------------- | -------------------------------------------- |
| New        | `'NEW'`         | Empty scene                                  |
| Empty      | `'EMPTY'`       | Settings only, no objects                    |
| Link Copy  | `'LINK_COPY'`   | Objects linked to original                   |
| Full Copy  | `'FULL_COPY'`   | Independent copy of everything               |

## Render Passes (View Layer)

| Pass                  | Property                            | Engine        |
| --------------------- | ----------------------------------- | ------------- |
| Combined              | `use_pass_combined`                 | All           |
| Z Depth               | `use_pass_z`                        | All           |
| Mist                  | `use_pass_mist`                     | All           |
| Normal                | `use_pass_normal`                   | All           |
| Diffuse Color         | `use_pass_diffuse_color`            | Cycles        |
| Diffuse Direct        | `use_pass_diffuse_direct`           | Cycles        |
| Diffuse Indirect      | `use_pass_diffuse_indirect`         | Cycles        |
| Glossy Color          | `use_pass_glossy_color`             | Cycles        |
| Glossy Direct         | `use_pass_glossy_direct`            | Cycles        |
| Glossy Indirect       | `use_pass_glossy_indirect`          | Cycles        |
| Transmission Color    | `use_pass_transmission_color`       | Cycles        |
| Transmission Direct   | `use_pass_transmission_direct`      | Cycles        |
| Transmission Indirect | `use_pass_transmission_indirect`    | Cycles        |
| Emit                  | `use_pass_emit`                     | Cycles        |
| Environment           | `use_pass_environment`              | Cycles        |
| Shadow                | `use_pass_shadow`                   | Cycles        |
| Ambient Occlusion     | `use_pass_ambient_occlusion`        | Cycles        |
| Position              | `use_pass_position`                 | Cycles        |
| Vector (Motion)       | `use_pass_vector`                   | Cycles        |
| Denoising Data        | `use_pass_denoising_data`           | Cycles        |
| Object Index          | `use_pass_object_index`             | Cycles        |
| Material Index        | `use_pass_material_index`           | Cycles        |
| Cryptomatte Object    | `use_pass_cryptomatte_object`       | Cycles, EEVEE |
| Cryptomatte Material  | `use_pass_cryptomatte_material`     | Cycles, EEVEE |
| Cryptomatte Asset     | `use_pass_cryptomatte_asset`        | Cycles, EEVEE |
