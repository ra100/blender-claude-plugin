# Blender Shader Nodes - Python API Reference

## Creating a Material with Nodes

```python
import bpy

# Create new material
mat = bpy.data.materials.new(name="MyMaterial")
mat.use_nodes = True
nodes = mat.node_tree.nodes
links = mat.node_tree.links

# Clear default nodes
nodes.clear()
```

## Adding Nodes

All shader nodes use `nodes.new(type=<type_string>)`.

### Shader/BSDF Nodes

```python
# Core shaders
principled = nodes.new(type='ShaderNodeBsdfPrincipled')
diffuse = nodes.new(type='ShaderNodeBsdfDiffuse')
glossy = nodes.new(type='ShaderNodeBsdfGlossy')
glass = nodes.new(type='ShaderNodeBsdfGlass')
metallic = nodes.new(type='ShaderNodeBsdfMetallic')  # New in 5.0
refraction = nodes.new(type='ShaderNodeBsdfRefraction')
translucent = nodes.new(type='ShaderNodeBsdfTranslucent')
transparent = nodes.new(type='ShaderNodeBsdfTransparent')
sheen = nodes.new(type='ShaderNodeBsdfSheen')
sss = nodes.new(type='ShaderNodeSubsurfaceScattering')
emission = nodes.new(type='ShaderNodeEmission')
toon = nodes.new(type='ShaderNodeBsdfToon')
hair = nodes.new(type='ShaderNodeBsdfHair')
hair_principled = nodes.new(type='ShaderNodeBsdfHairPrincipled')
ray_portal = nodes.new(type='ShaderNodeBsdfRayPortal')

# Shader combiners
mix_shader = nodes.new(type='ShaderNodeMixShader')
add_shader = nodes.new(type='ShaderNodeAddShader')

# Volume
vol_absorption = nodes.new(type='ShaderNodeVolumeAbsorption')
vol_scatter = nodes.new(type='ShaderNodeVolumeScatter')
vol_principled = nodes.new(type='ShaderNodeVolumePrincipled')
vol_coefficients = nodes.new(type='ShaderNodeVolumeCoefficients')  # New in 5.0

# Special
holdout = nodes.new(type='ShaderNodeHoldout')
background = nodes.new(type='ShaderNodeBackground')
eevee_specular = nodes.new(type='ShaderNodeEeveeSpecular')
```

### Output Nodes

```python
output = nodes.new(type='ShaderNodeOutputMaterial')
light_out = nodes.new(type='ShaderNodeOutputLight')
world_out = nodes.new(type='ShaderNodeOutputWorld')
aov_out = nodes.new(type='ShaderNodeOutputAOV')
```

### Texture Nodes

```python
image_tex = nodes.new(type='ShaderNodeTexImage')
env_tex = nodes.new(type='ShaderNodeTexEnvironment')
sky = nodes.new(type='ShaderNodeTexSky')
noise = nodes.new(type='ShaderNodeTexNoise')
voronoi = nodes.new(type='ShaderNodeTexVoronoi')
wave = nodes.new(type='ShaderNodeTexWave')
magic = nodes.new(type='ShaderNodeTexMagic')
checker = nodes.new(type='ShaderNodeTexChecker')
brick = nodes.new(type='ShaderNodeTexBrick')
gradient = nodes.new(type='ShaderNodeTexGradient')
white_noise = nodes.new(type='ShaderNodeTexWhiteNoise')
gabor = nodes.new(type='ShaderNodeTexGabor')
ies = nodes.new(type='ShaderNodeTexIES')
point_density = nodes.new(type='ShaderNodeTexPointDensity')
```

### Input Nodes

```python
tex_coord = nodes.new(type='ShaderNodeTexCoord')
geometry = nodes.new(type='ShaderNodeNewGeometry')
object_info = nodes.new(type='ShaderNodeObjectInfo')
camera_data = nodes.new(type='ShaderNodeCameraData')
light_path = nodes.new(type='ShaderNodeLightPath')
fresnel = nodes.new(type='ShaderNodeFresnel')
layer_weight = nodes.new(type='ShaderNodeLayerWeight')
attribute = nodes.new(type='ShaderNodeAttribute')
uv_map = nodes.new(type='ShaderNodeUVMap')
tangent = nodes.new(type='ShaderNodeTangent')
vertex_color = nodes.new(type='ShaderNodeVertexColor')
ao = nodes.new(type='ShaderNodeAmbientOcclusion')
bevel = nodes.new(type='ShaderNodeBevel')
particle_info = nodes.new(type='ShaderNodeParticleInfo')
hair_info = nodes.new(type='ShaderNodeHairInfo')
point_info = nodes.new(type='ShaderNodePointInfo')
volume_info = nodes.new(type='ShaderNodeVolumeInfo')
wireframe = nodes.new(type='ShaderNodeWireframe')
rgb = nodes.new(type='ShaderNodeRGB')
value = nodes.new(type='ShaderNodeValue')
```

### Color Nodes

```python
mix_color = nodes.new(type='ShaderNodeMix')
mix_color.data_type = 'RGBA'

color_ramp = nodes.new(type='ShaderNodeValToRGB')
rgb_curves = nodes.new(type='ShaderNodeRGBCurve')
invert = nodes.new(type='ShaderNodeInvert')
hue_sat = nodes.new(type='ShaderNodeHueSaturation')
bright_contrast = nodes.new(type='ShaderNodeBrightContrast')
gamma = nodes.new(type='ShaderNodeGamma')
light_falloff = nodes.new(type='ShaderNodeLightFalloff')
shader_to_rgb = nodes.new(type='ShaderNodeShaderToRGB')
combine_color = nodes.new(type='ShaderNodeCombineColor')
separate_color = nodes.new(type='ShaderNodeSeparateColor')
```

### Vector Nodes

```python
bump = nodes.new(type='ShaderNodeBump')
displacement = nodes.new(type='ShaderNodeDisplacement')
vector_displacement = nodes.new(type='ShaderNodeVectorDisplacement')
normal = nodes.new(type='ShaderNodeNormal')
normal_map = nodes.new(type='ShaderNodeNormalMap')
vector_transform = nodes.new(type='ShaderNodeVectorTransform')
vector_curves = nodes.new(type='ShaderNodeVectorCurve')
vector_math = nodes.new(type='ShaderNodeVectorMath')
vector_rotate = nodes.new(type='ShaderNodeVectorRotate')
mapping = nodes.new(type='ShaderNodeMapping')
radial_tiling = nodes.new(type='ShaderNodeRadialTiling')
```

### Converter Nodes

```python
math = nodes.new(type='ShaderNodeMath')
combine_xyz = nodes.new(type='ShaderNodeCombineXYZ')
separate_xyz = nodes.new(type='ShaderNodeSeparateXYZ')
map_range = nodes.new(type='ShaderNodeMapRange')
float_curve = nodes.new(type='ShaderNodeFloatCurve')
clamp = nodes.new(type='ShaderNodeClamp')
rgb_to_bw = nodes.new(type='ShaderNodeRGBToBW')
blackbody = nodes.new(type='ShaderNodeBlackbody')
wavelength = nodes.new(type='ShaderNodeWavelength')
mix = nodes.new(type='ShaderNodeMix')
```

## Linking Nodes

```python
# By socket name
links.new(principled.outputs['BSDF'], output.inputs['Surface'])
links.new(noise.outputs['Fac'], color_ramp.inputs['Fac'])
links.new(color_ramp.outputs['Color'], principled.inputs['Base Color'])

# By index
links.new(principled.outputs[0], output.inputs[0])
```

## Setting Input Values

```python
# Principled BSDF inputs
principled.inputs['Base Color'].default_value = (0.8, 0.1, 0.1, 1.0)
principled.inputs['Metallic'].default_value = 0.0
principled.inputs['Roughness'].default_value = 0.4
principled.inputs['IOR'].default_value = 1.45
principled.inputs['Alpha'].default_value = 1.0
principled.inputs['Emission Color'].default_value = (0, 0, 0, 1)
principled.inputs['Emission Strength'].default_value = 0.0

# Texture inputs
noise.inputs['Scale'].default_value = 5.0
noise.inputs['Detail'].default_value = 2.0
noise.inputs['Roughness'].default_value = 0.5

# Math operations
math.operation = 'MULTIPLY'  # ADD, SUBTRACT, MULTIPLY, DIVIDE, POWER, etc.
math.inputs[0].default_value = 1.0
math.inputs[1].default_value = 0.5

# Vector math
vector_math.operation = 'NORMALIZE'

# Image texture
image_tex.image = bpy.data.images.load("/path/to/texture.png")
image_tex.projection = 'FLAT'  # FLAT, BOX, SPHERE, TUBE
```

## Color Ramp Configuration

```python
color_ramp = nodes.new(type='ShaderNodeValToRGB')

# Access color ramp elements
ramp = color_ramp.color_ramp

# Modify existing stops
ramp.elements[0].position = 0.3
ramp.elements[0].color = (0.0, 0.0, 0.0, 1.0)  # Black
ramp.elements[1].position = 0.7
ramp.elements[1].color = (1.0, 1.0, 1.0, 1.0)  # White

# Add new stop
new_stop = ramp.elements.new(0.5)
new_stop.color = (0.5, 0.0, 0.0, 1.0)  # Red at midpoint

# Set interpolation
ramp.color_mode = 'RGB'  # RGB, HSV, HSL
ramp.interpolation = 'LINEAR'  # LINEAR, EASE, CARDINAL, B_SPLINE, CONSTANT
```

## Node Positioning

```python
# Left-to-right layout, ~300 spacing
tex_coord.location = (-900, 0)
noise.location = (-600, 0)
color_ramp.location = (-300, 0)
principled.location = (0, 0)
output.location = (300, 0)
```

## Assigning Material to Object

```python
obj = bpy.context.active_object
if obj and obj.type == 'MESH':
    if len(obj.data.materials) == 0:
        obj.data.materials.append(mat)
    else:
        obj.data.materials[0] = mat
```

## World/Environment Setup

```python
world = bpy.data.worlds.new(name="MyWorld")
bpy.context.scene.world = world
world.use_nodes = True

nodes = world.node_tree.nodes
links = world.node_tree.links
nodes.clear()

# HDRI environment
env_tex = nodes.new(type='ShaderNodeTexEnvironment')
env_tex.image = bpy.data.images.load("/path/to/hdri.exr")
env_tex.location = (-300, 0)

bg = nodes.new(type='ShaderNodeBackground')
bg.inputs['Strength'].default_value = 1.0
bg.location = (0, 0)

output = nodes.new(type='ShaderNodeOutputWorld')
output.location = (300, 0)

links.new(env_tex.outputs['Color'], bg.inputs['Color'])
links.new(bg.outputs['Background'], output.inputs['Surface'])
```

## Common Material Settings

```python
# Blend mode (EEVEE)
mat.blend_method = 'OPAQUE'  # OPAQUE, CLIP, HASHED, BLEND

# Shadow mode (EEVEE)
mat.shadow_method = 'OPAQUE'  # NONE, OPAQUE, CLIP, HASHED

# Backface culling
mat.use_backface_culling = False

# Displacement method (Cycles)
mat.cycles.displacement_method = 'DISPLACEMENT'  # BUMP, DISPLACEMENT, BOTH

# Screen refraction (EEVEE)
mat.use_screen_refraction = True  # For glass-like materials
```

## Frame Nodes for Organization

```python
frame = nodes.new(type='NodeFrame')
frame.label = "Base Color Setup"
frame.use_custom_color = True
frame.color = (0.2, 0.3, 0.4)

# Parent nodes to frame
noise.parent = frame
color_ramp.parent = frame
```
