# Blender Scene & Rendering Python API Reference

## Render Settings

### Full Render Configuration

```python
import bpy

scene = bpy.context.scene
render = scene.render

# Engine
render.engine = 'CYCLES'  # CYCLES, BLENDER_EEVEE_NEXT, BLENDER_WORKBENCH

# Resolution
render.resolution_x = 1920
render.resolution_y = 1080
render.resolution_percentage = 100

# Frame range
scene.frame_start = 1
scene.frame_end = 250
scene.frame_step = 1
render.fps = 24
render.fps_base = 1.0

# Output
render.filepath = "//output/render_"
render.image_settings.file_format = 'PNG'
render.image_settings.color_mode = 'RGBA'
render.image_settings.color_depth = '16'

# Film
render.film_transparent = True  # Transparent background

# Performance
render.threads_mode = 'AUTO'  # AUTO, FIXED
render.use_persistent_data = True  # Keep data between frames (Cycles)

# Metadata
render.use_stamp = True
render.stamp_font_size = 12
render.use_stamp_date = True
render.use_stamp_time = True
render.use_stamp_render_time = True
render.use_stamp_frame = True
render.use_stamp_camera = True
render.use_stamp_scene = True
```

### Cycles Configuration

```python
import bpy

cycles = bpy.context.scene.cycles

# Sampling
cycles.samples = 256
cycles.preview_samples = 32
cycles.use_adaptive_sampling = True
cycles.adaptive_threshold = 0.01
cycles.adaptive_min_samples = 64
cycles.use_preview_adaptive_sampling = True

# Time limit
cycles.time_limit = 0.0  # Seconds, 0 = disabled

# Seed
cycles.seed = 0
cycles.use_animated_seed = True

# Light paths
cycles.max_bounces = 12
cycles.diffuse_bounces = 4
cycles.glossy_bounces = 4
cycles.transmission_bounces = 12
cycles.volume_bounces = 0
cycles.transparent_max_bounces = 8
cycles.caustics_reflective = False
cycles.caustics_refractive = False

# Clamping
cycles.sample_clamp_direct = 0.0
cycles.sample_clamp_indirect = 10.0

# Denoising
cycles.use_denoising = True
cycles.denoiser = 'OPENIMAGEDENOISE'  # OPENIMAGEDENOISE, OPTIX
cycles.denoising_input_passes = 'RGB_ALBEDO_NORMAL'
cycles.denoising_prefilter = 'ACCURATE'  # NONE, FAST, ACCURATE
cycles.use_preview_denoising = True
cycles.preview_denoiser = 'OPENIMAGEDENOISE'
cycles.preview_denoising_start_sample = 1

# Device
cycles.device = 'GPU'  # CPU, GPU
prefs = bpy.context.preferences.addons['cycles'].preferences
prefs.compute_device_type = 'METAL'  # CUDA, OPTIX, HIP, METAL, ONEAPI
prefs.get_devices()
for device in prefs.devices:
    device.use = True

# Film
cycles.film_exposure = 1.0
cycles.pixel_filter_type = 'BLACKMAN_HARRIS'  # BOX, GAUSSIAN, BLACKMAN_HARRIS
cycles.filter_width = 1.5
```

### EEVEE Next Configuration

```python
import bpy

eevee = bpy.context.scene.eevee

# Sampling
eevee.taa_render_samples = 64
eevee.taa_samples = 16  # Viewport

# Shadows
eevee.shadow_ray_count = 1
eevee.shadow_step_count = 6

# Ray tracing
eevee.use_raytracing = True
eevee.ray_tracing_method = 'SCREEN'  # SCREEN, FULL

# Volumetrics
eevee.volumetric_start = 0.1
eevee.volumetric_end = 100.0
eevee.volumetric_tile_size = '8'
eevee.volumetric_samples = 64
eevee.use_volumetric_shadows = True
```

## Color Management

```python
import bpy

# View settings
view = bpy.context.scene.view_settings
view.view_transform = 'AgX'  # AgX, Filmic, Standard, Raw, False Color
view.look = 'None'           # None, AgX - Punchy, AgX - Golden, etc.
view.exposure = 0.0
view.gamma = 1.0

# Display settings
display = bpy.context.scene.display_settings
display.display_device = 'sRGB'  # sRGB, None

# Sequencer
bpy.context.scene.sequencer_colorspace_settings.name = 'sRGB'
```

## Scene Management

### Scene Properties

```python
import bpy

scene = bpy.context.scene

# Units
unit = scene.unit_settings
unit.system = 'METRIC'
unit.scale_length = 1.0
unit.length_unit = 'METERS'

# Gravity
scene.gravity = (0, 0, -9.81)
scene.use_gravity = True

# Audio
scene.audio_doppler_speed = 343.3
scene.audio_doppler_factor = 1.0
```

### Create / Switch / Delete Scenes

```python
import bpy

# Create new empty scene
new_scene = bpy.data.scenes.new("NewScene")

# Duplicate current scene (operator)
bpy.ops.scene.new(type='FULL_COPY')  # NEW, EMPTY, LINK_COPY, FULL_COPY

# Switch scene
bpy.context.window.scene = bpy.data.scenes["SceneName"]

# Delete scene
bpy.data.scenes.remove(bpy.data.scenes["SceneName"])

# List all scenes
for scene in bpy.data.scenes:
    print(scene.name)
```

## View Layers

```python
import bpy

scene = bpy.context.scene

# Create view layer
vl = scene.view_layers.new("MyLayer")

# Access active view layer
active_vl = bpy.context.view_layer

# Enable/disable collections
layer_coll = vl.layer_collection
for child in layer_coll.children:
    child.exclude = False  # Include in render
    child.hide_viewport = False
    child.holdout = False
    child.indirect_only = False

# Render passes
vl.use_pass_combined = True
vl.use_pass_z = True
vl.use_pass_normal = True
vl.use_pass_mist = True
vl.use_pass_diffuse_color = True
vl.use_pass_diffuse_direct = True
vl.use_pass_diffuse_indirect = True
vl.use_pass_glossy_color = True
vl.use_pass_glossy_direct = True
vl.use_pass_glossy_indirect = True
vl.use_pass_emit = True
vl.use_pass_shadow = True
vl.use_pass_ambient_occlusion = True
vl.use_pass_position = True
vl.use_pass_vector = True

# Cryptomatte
vl.use_pass_cryptomatte_object = True
vl.use_pass_cryptomatte_material = True
vl.use_pass_cryptomatte_asset = True

# Delete view layer
scene.view_layers.remove(vl)
```

## World / Environment

### HDRI Setup

```python
import bpy

world = bpy.context.scene.world
if world is None:
    world = bpy.data.worlds.new("World")
    bpy.context.scene.world = world

world.use_nodes = True
nodes = world.node_tree.nodes
links = world.node_tree.links
nodes.clear()

bg = nodes.new('ShaderNodeBackground')
env_tex = nodes.new('ShaderNodeTexEnvironment')
env_tex.image = bpy.data.images.load("//hdri/environment.exr")
mapping = nodes.new('ShaderNodeMapping')
tex_coord = nodes.new('ShaderNodeTexCoord')
output = nodes.new('ShaderNodeOutputWorld')

links.new(tex_coord.outputs['Generated'], mapping.inputs['Vector'])
links.new(mapping.outputs['Vector'], env_tex.inputs['Vector'])
links.new(env_tex.outputs['Color'], bg.inputs['Color'])
links.new(bg.outputs['Background'], output.inputs['Surface'])

bg.inputs['Strength'].default_value = 1.0
mapping.inputs['Rotation'].default_value = (0, 0, 0)
```

### Gradient Sky

```python
import bpy

world = bpy.context.scene.world
world.use_nodes = True
nodes = world.node_tree.nodes
links = world.node_tree.links
nodes.clear()

bg = nodes.new('ShaderNodeBackground')
color_ramp = nodes.new('ShaderNodeValToRGB')
separate = nodes.new('ShaderNodeSeparateXYZ')
tex_coord = nodes.new('ShaderNodeTexCoord')
output = nodes.new('ShaderNodeOutputWorld')

# Set gradient colors
color_ramp.color_ramp.elements[0].color = (0.1, 0.2, 0.5, 1.0)  # Horizon
color_ramp.color_ramp.elements[1].color = (0.5, 0.7, 1.0, 1.0)  # Zenith

links.new(tex_coord.outputs['Generated'], separate.inputs['Vector'])
links.new(separate.outputs['Z'], color_ramp.inputs['Fac'])
links.new(color_ramp.outputs['Color'], bg.inputs['Color'])
links.new(bg.outputs['Background'], output.inputs['Surface'])
```

### World Mist Settings

```python
import bpy

world = bpy.context.scene.world
mist = world.mist_settings
mist.use_mist = True
mist.start = 5.0
mist.depth = 25.0
mist.falloff = 'QUADRATIC'  # QUADRATIC, LINEAR, INVERSE_QUADRATIC
```

## Import Operations

### FBX Import

```python
import bpy

bpy.ops.import_scene.fbx(
    filepath="/path/to/model.fbx",
    global_scale=1.0,
    use_custom_normals=True,
    use_image_search=True,
    use_alpha_decals=False,
    decal_offset=0.0,
    use_anim=True,
    anim_offset=1.0,
    use_custom_props=True,
    use_custom_props_enum_as_string=True,
    ignore_leaf_bones=True,
    force_connect_children=False,
    automatic_bone_orientation=False,
    primary_bone_axis='Y',
    secondary_bone_axis='X',
    use_prepost_rot=True,
)
```

### glTF Import

```python
import bpy

bpy.ops.import_scene.gltf(
    filepath="/path/to/model.glb",
    import_pack_images=True,
    merge_vertices=False,
    import_shading='NORMALS',  # NORMALS, FLAT
    bone_heuristic='TEMPERANCE',  # BLENDER, TEMPERANCE, FORTUNE
    guess_original_bind_pose=True,
)
```

### OBJ Import

```python
import bpy

bpy.ops.wm.obj_import(
    filepath="/path/to/model.obj",
    global_scale=1.0,
    clamp_size=0,
    forward_axis='NEGATIVE_Z',
    up_axis='Y',
    use_split_objects=True,
    use_split_groups=False,
    import_vertex_groups=False,
)
```

### USD Import

```python
import bpy

bpy.ops.wm.usd_import(
    filepath="/path/to/scene.usdc",
    scale=1.0,
    import_cameras=True,
    import_curves=True,
    import_lights=True,
    import_materials=True,
    import_meshes=True,
    import_volumes=True,
    import_guide=False,
    import_proxy=True,
    import_render=True,
)
```

### Alembic Import

```python
import bpy

bpy.ops.wm.alembic_import(
    filepath="/path/to/cache.abc",
    scale=1.0,
    set_frame_range=True,
    is_sequence=False,
    as_background_job=False,
)
```

## Export Operations

### FBX Export

```python
import bpy

bpy.ops.export_scene.fbx(
    filepath="/path/to/export.fbx",
    use_selection=True,
    use_visible=False,
    use_active_collection=False,
    apply_unit_scale=True,
    apply_scale_options='FBX_SCALE_ALL',
    axis_forward='-Z',
    axis_up='Y',
    object_types={'MESH', 'ARMATURE', 'EMPTY', 'CAMERA', 'LIGHT'},
    use_mesh_modifiers=True,
    mesh_smooth_type='FACE',       # OFF, FACE, EDGE
    use_mesh_edges=False,
    use_triangles=False,
    use_custom_props=False,
    add_leaf_bones=False,
    primary_bone_axis='Y',
    secondary_bone_axis='X',
    bake_anim=True,
    bake_anim_use_all_bones=True,
    bake_anim_use_nla_strips=True,
    bake_anim_use_all_actions=True,
    bake_anim_step=1.0,
    bake_anim_simplify_factor=1.0,
    path_mode='AUTO',
    embed_textures=False,
    batch_mode='OFF',
)
```

### glTF Export

```python
import bpy

bpy.ops.export_scene.gltf(
    filepath="/path/to/export.glb",
    export_format='GLB',              # GLB, GLTF_SEPARATE, GLTF_EMBEDDED
    use_selection=True,
    use_visible=False,
    use_active_collection=False,
    use_active_scene=True,
    export_apply=True,                # Apply modifiers
    export_texcoords=True,
    export_normals=True,
    export_tangents=False,
    export_colors=True,
    export_attributes=False,
    export_cameras=False,
    export_lights=False,
    export_yup=True,                  # +Y up (glTF convention)
    export_animations=True,
    export_frame_range=True,
    export_nla_strips=True,
    export_draco_mesh_compression_enable=False,
    export_draco_mesh_compression_level=6,
    export_image_format='AUTO',       # AUTO, JPEG, WEBP, NONE
)
```

### OBJ Export

```python
import bpy

bpy.ops.wm.obj_export(
    filepath="/path/to/export.obj",
    export_selected_objects=True,
    apply_modifiers=True,
    export_uv=True,
    export_normals=True,
    export_colors=False,
    export_materials=True,
    export_triangulated_mesh=False,
    export_curves_as_nurbs=False,
    forward_axis='NEGATIVE_Z',
    up_axis='Y',
    global_scale=1.0,
)
```

### USD Export

```python
import bpy

bpy.ops.wm.usd_export(
    filepath="/path/to/export.usdc",
    selected_objects_only=True,
    visible_objects_only=True,
    export_animation=True,
    export_hair=True,
    export_uvmaps=True,
    export_normals=True,
    export_materials=True,
    use_instancing=True,
    evaluation_mode='RENDER',  # RENDER, VIEWPORT
)
```

## Linking & Appending

### Append Data Blocks

```python
import bpy

blend_path = "/path/to/library.blend"

# Append specific data blocks
with bpy.data.libraries.load(blend_path, link=False) as (data_from, data_to):
    # List available data
    print("Objects:", data_from.objects)
    print("Materials:", data_from.materials)
    print("Node Groups:", data_from.node_groups)
    print("Collections:", data_from.collections)

    # Select what to append
    data_to.objects = ["ObjectName"]
    data_to.materials = ["MaterialName"]
    data_to.node_groups = ["NodeGroupName"]
    data_to.collections = ["CollectionName"]

# Link appended objects to scene
for obj in data_to.objects:
    if obj is not None:
        bpy.context.collection.objects.link(obj)

# Link appended collection
for coll in data_to.collections:
    if coll is not None:
        bpy.context.scene.collection.children.link(coll)
```

### Link Data Blocks (Reference)

```python
import bpy

blend_path = "/path/to/library.blend"

with bpy.data.libraries.load(blend_path, link=True) as (data_from, data_to):
    data_to.collections = ["CollectionName"]

for coll in data_to.collections:
    if coll is not None:
        bpy.context.scene.collection.children.link(coll)
```

### Library Overrides

```python
import bpy

# Select a linked object, then:
bpy.ops.object.make_override_library()

# Or programmatically on a linked collection
linked_coll = bpy.data.collections["LinkedCollection"]
linked_coll.override_hierarchy_create(
    bpy.context.scene,
    bpy.context.view_layer,
)
```

### Manage Libraries

```python
import bpy

# List all libraries
for lib in bpy.data.libraries:
    print(f"{lib.name}: {lib.filepath}")

# Reload all libraries
for lib in bpy.data.libraries:
    lib.reload()

# Relocate library
lib = bpy.data.libraries["library.blend"]
lib.filepath = "/new/path/to/library.blend"
lib.reload()
```

## Viewport Configuration

### Shading

```python
import bpy

for area in bpy.context.screen.areas:
    if area.type == 'VIEW_3D':
        for space in area.spaces:
            if space.type == 'VIEW_3D':
                shading = space.shading

                # Type: WIREFRAME, SOLID, MATERIAL, RENDERED
                shading.type = 'MATERIAL'

                # Solid mode
                shading.light = 'STUDIO'
                shading.color_type = 'MATERIAL'
                shading.show_backface_culling = False
                shading.show_cavity = True
                shading.cavity_type = 'BOTH'

                # Rendered mode
                shading.use_scene_lights_render = True
                shading.use_scene_world_render = True

                break
```

### Overlays

```python
import bpy

for area in bpy.context.screen.areas:
    if area.type == 'VIEW_3D':
        for space in area.spaces:
            if space.type == 'VIEW_3D':
                overlay = space.overlay

                overlay.show_overlays = True
                overlay.show_floor = True
                overlay.show_axis_x = True
                overlay.show_axis_y = True
                overlay.show_axis_z = False
                overlay.show_wireframes = False
                overlay.wireframe_threshold = 1.0
                overlay.show_face_orientation = False
                overlay.show_relationship_lines = True
                overlay.show_stats = True
                overlay.show_text = True
                overlay.show_extras = True

                break
```

### Camera View

```python
import bpy

# Set active camera
bpy.context.scene.camera = bpy.data.objects["Camera"]

# Switch to camera view
for area in bpy.context.screen.areas:
    if area.type == 'VIEW_3D':
        area.spaces[0].region_3d.view_perspective = 'CAMERA'
        break

# Camera properties
cam = bpy.data.cameras["Camera"]
cam.lens = 50            # Focal length (mm)
cam.sensor_width = 36    # Sensor width (mm)
cam.clip_start = 0.1
cam.clip_end = 1000
cam.dof.use_dof = True
cam.dof.focus_distance = 5.0
cam.dof.aperture_fstop = 2.8
```

## Rendering Commands

```python
import bpy

# Render still image
bpy.ops.render.render(write_still=True)

# Render animation
bpy.ops.render.render(animation=True)

# Render specific scene
bpy.ops.render.render(write_still=True, scene="SceneName")

# Render to viewer (not saved to disk)
bpy.ops.render.render()
result = bpy.data.images['Render Result']
```

## Batch Rendering

### Multiple Cameras

```python
import bpy

scene = bpy.context.scene
original_camera = scene.camera

for obj in bpy.data.objects:
    if obj.type == 'CAMERA':
        scene.camera = obj
        scene.render.filepath = f"//output/{obj.name}_"
        bpy.ops.render.render(write_still=True)

scene.camera = original_camera
```

### Multiple Scenes

```python
import bpy

for scene in bpy.data.scenes:
    scene.render.filepath = f"//output/{scene.name}_"
    bpy.ops.render.render(write_still=True, scene=scene.name)
```

### Multiple View Layers

```python
import bpy

scene = bpy.context.scene

for vl in scene.view_layers:
    # Disable all layers, enable just one
    for other_vl in scene.view_layers:
        other_vl.use = (other_vl == vl)
    scene.render.filepath = f"//output/{vl.name}_"
    bpy.ops.render.render(write_still=True)

# Re-enable all
for vl in scene.view_layers:
    vl.use = True
```

### Render with Custom Settings per Shot

```python
import bpy

shots = [
    {"name": "wide", "camera": "Camera.Wide", "samples": 128, "res": (1920, 1080)},
    {"name": "close", "camera": "Camera.Close", "samples": 256, "res": (2560, 1440)},
    {"name": "top", "camera": "Camera.Top", "samples": 64, "res": (1080, 1080)},
]

scene = bpy.context.scene

for shot in shots:
    scene.camera = bpy.data.objects[shot["camera"]]
    scene.cycles.samples = shot["samples"]
    scene.render.resolution_x = shot["res"][0]
    scene.render.resolution_y = shot["res"][1]
    scene.render.filepath = f"//output/{shot['name']}_"
    bpy.ops.render.render(write_still=True)
```

## Preferences & Add-ons

### Enable/Disable Add-ons

```python
import bpy

# Enable an add-on
bpy.ops.preferences.addon_enable(module="io_scene_gltf2")

# Disable an add-on
bpy.ops.preferences.addon_disable(module="io_scene_gltf2")

# Check if add-on is enabled
is_enabled = "io_scene_gltf2" in bpy.context.preferences.addons

# List all enabled add-ons
for addon in bpy.context.preferences.addons:
    print(addon.module)
```

### Pack / Unpack Resources

```python
import bpy

# Pack all external files into .blend
bpy.ops.file.pack_all()

# Unpack all files
bpy.ops.file.unpack_all(method='USE_LOCAL')  # USE_LOCAL, WRITE_LOCAL, USE_ORIGINAL, WRITE_ORIGINAL

# Pack specific image
img = bpy.data.images["texture.png"]
img.pack()

# Unpack specific image
img.unpack(method='USE_LOCAL')
```

### File Operations

```python
import bpy

# Save current file
bpy.ops.wm.save_mainfile()

# Save as
bpy.ops.wm.save_as_mainfile(filepath="/path/to/file.blend")

# Open file
bpy.ops.wm.open_mainfile(filepath="/path/to/file.blend")

# Revert to saved
bpy.ops.wm.revert_mainfile()
```
