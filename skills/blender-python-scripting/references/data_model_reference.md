# Blender Data Model Reference

## Top-Level Data Access

All Blender data is accessed through `bpy.data`:

```python
import bpy

bpy.data.objects          # All objects in the file
bpy.data.meshes           # All mesh data blocks
bpy.data.materials        # All materials
bpy.data.textures         # All textures
bpy.data.images           # All images
bpy.data.cameras          # All camera data
bpy.data.lights           # All light data
bpy.data.curves           # All curve data
bpy.data.armatures        # All armature data
bpy.data.collections      # All collections
bpy.data.scenes           # All scenes
bpy.data.worlds           # All world environments
bpy.data.node_groups      # All node groups (geometry, shader, compositor)
bpy.data.particles        # All particle settings
bpy.data.actions          # All animation actions
bpy.data.fonts            # All font data
bpy.data.grease_pencils   # All grease pencil data (legacy)
bpy.data.speakers         # All speaker data
bpy.data.lattices         # All lattice data
bpy.data.metaballs        # All metaball data
bpy.data.volumes          # All volume data
bpy.data.libraries        # Linked libraries
bpy.data.texts            # Internal text blocks
bpy.data.screens          # All screen layouts
bpy.data.workspaces       # All workspaces
bpy.data.palettes         # All color palettes
bpy.data.brushes          # All brushes
bpy.data.masks            # All masks
bpy.data.movieclips       # All movie clips
bpy.data.linestyles       # All freestyle linestyles
bpy.data.shape_keys       # (accessed via mesh.shape_keys)
```

## Data Hierarchy

```
Scene
├── ViewLayer
│   └── LayerCollection (mirrors collection hierarchy)
├── Collection (Scene Collection - root)
│   ├── Collection (child)
│   │   ├── Object
│   │   │   ├── data → Mesh / Curve / Armature / Camera / Light / ...
│   │   │   ├── modifiers[]
│   │   │   ├── constraints[]
│   │   │   ├── particle_systems[]
│   │   │   ├── materials[] (material slots)
│   │   │   └── animation_data → Action → FCurves
│   │   └── Object ...
│   └── Collection (child) ...
├── World (environment)
│   └── node_tree (shader nodes)
├── RenderSettings
└── frame_start, frame_end, frame_current
```

## Object Types

| `object.type`    | Data Attribute                   | Description                    |
| ---------------- | -------------------------------- | ------------------------------ |
| `'MESH'`         | `object.data` → `Mesh`           | Polygon mesh                   |
| `'CURVE'`        | `object.data` → `Curve`          | Bézier/NURBS curve             |
| `'SURFACE'`      | `object.data` → `SurfaceCurve`   | NURBS surface                  |
| `'META'`         | `object.data` → `MetaBall`       | Metaball                       |
| `'FONT'`         | `object.data` → `TextCurve`      | Text object                    |
| `'ARMATURE'`     | `object.data` → `Armature`       | Armature/skeleton              |
| `'LATTICE'`      | `object.data` → `Lattice`        | Lattice deformer               |
| `'EMPTY'`        | None                             | Empty/null object              |
| `'CAMERA'`       | `object.data` → `Camera`         | Camera                         |
| `'LIGHT'`        | `object.data` → `Light`          | Light (point, sun, spot, area) |
| `'SPEAKER'`      | `object.data` → `Speaker`        | Audio speaker                  |
| `'GPENCIL'`      | `object.data` → `GreasePencil`   | Grease Pencil (legacy)         |
| `'GREASEPENCIL'` | `object.data` → `GreasePencilv3` | Grease Pencil v3 (5.0)         |
| `'VOLUME'`       | `object.data` → `Volume`         | OpenVDB volume                 |
| `'LIGHT_PROBE'`  | `object.data` → `LightProbe`     | Light probe (EEVEE)            |
| `'POINTCLOUD'`   | `object.data` → `PointCloud`     | Point cloud                    |
| `'CURVES'`       | `object.data` → `Curves`         | Hair curves (new system)       |

## ID Data Blocks

All top-level data blocks inherit from `ID`:

```python
# Common ID properties
id_block.name             # Name (string)
id_block.name_full        # Full name including library
id_block.users            # Number of users
id_block.use_fake_user    # Fake user (prevents deletion)
id_block.is_library_indirect  # Indirectly linked
id_block.library          # Source library (if linked)
id_block.tag              # Temporary tag (for scripts)

# Copy
new_block = id_block.copy()

# Remove
bpy.data.meshes.remove(mesh_data)
bpy.data.objects.remove(obj)
```

## Mesh Data

```python
mesh = obj.data  # type: bpy.types.Mesh

# Read geometry
mesh.vertices       # MeshVertices collection
mesh.edges          # MeshEdges collection
mesh.polygons       # MeshPolygons collection (faces)
mesh.loops          # MeshLoops collection (face corners)

# Access per-vertex
for v in mesh.vertices:
    v.co              # Vector (x, y, z) position
    v.normal          # Vector normal
    v.index           # Vertex index
    v.select          # Selected state
    v.hide            # Hidden state

# Access per-face
for poly in mesh.polygons:
    poly.vertices     # Tuple of vertex indices
    poly.loop_start   # Index into mesh.loops
    poly.loop_total   # Number of loops (vertices in face)
    poly.normal       # Face normal
    poly.center       # Face center
    poly.area         # Face area
    poly.material_index  # Material slot index

# Access per-edge
for edge in mesh.edges:
    edge.vertices     # Tuple of 2 vertex indices
    edge.use_seam     # UV seam
    edge.use_sharp    # Sharp edge
    edge.crease       # Edge crease value (0-1)

# UV Maps
uv_layer = mesh.uv_layers.active  # or mesh.uv_layers["UVMap"]
for loop_idx, loop in enumerate(mesh.loops):
    uv = uv_layer.data[loop_idx].uv  # Vector2 (u, v)

# Vertex Colors / Color Attributes
color_attr = mesh.color_attributes.active
# or mesh.color_attributes["Col"]

# Custom Attributes (Blender 4.x+)
mesh.attributes["my_attr"]  # Access custom attribute
mesh.attributes.new(name="my_float", type='FLOAT', domain='POINT')

# Create mesh from data
mesh = bpy.data.meshes.new("MyMesh")
verts = [(0, 0, 0), (1, 0, 0), (1, 1, 0), (0, 1, 0)]
faces = [(0, 1, 2, 3)]
mesh.from_pydata(verts, [], faces)
mesh.update()
```

## Curve Data

```python
curve = obj.data  # type: bpy.types.Curve

curve.dimensions = '3D'  # '2D' or '3D'
curve.resolution_u = 12  # Curve smoothness
curve.fill_mode = 'FULL'  # 'NONE', 'BACK', 'FRONT', 'FULL'
curve.bevel_depth = 0.1   # Bevel radius
curve.bevel_resolution = 4
curve.extrude = 0.0

# Splines
for spline in curve.splines:
    spline.type         # 'BEZIER', 'NURBS', 'POLY'
    spline.use_cyclic_u # Closed spline
    spline.order_u      # NURBS order

    # Bézier points
    for bp in spline.bezier_points:
        bp.co                  # Control point position
        bp.handle_left         # Left handle position
        bp.handle_right        # Right handle position
        bp.handle_left_type    # 'FREE', 'VECTOR', 'ALIGNED', 'AUTO'
        bp.handle_right_type

    # NURBS/Poly points
    for pt in spline.points:
        pt.co                  # (x, y, z, w) homogeneous coordinates

# Create curve from data
curve_data = bpy.data.curves.new("MyCurve", type='CURVE')
curve_data.dimensions = '3D'
spline = curve_data.splines.new('BEZIER')
spline.bezier_points.add(2)  # 3 points total (1 default + 2)
spline.bezier_points[0].co = (0, 0, 0)
spline.bezier_points[1].co = (1, 1, 0)
spline.bezier_points[2].co = (2, 0, 0)
```

## Camera Data

```python
cam = obj.data  # type: bpy.types.Camera

cam.type = 'PERSP'        # 'PERSP', 'ORTHO', 'PANO'
cam.lens = 50             # Focal length (mm)
cam.sensor_width = 36     # Sensor width (mm)
cam.clip_start = 0.1
cam.clip_end = 1000
cam.dof.use_dof = True
cam.dof.focus_distance = 5.0
cam.dof.aperture_fstop = 2.8

# Orthographic
cam.ortho_scale = 6.0
```

## Light Data

```python
light = obj.data  # type: bpy.types.Light

light.type = 'POINT'      # 'POINT', 'SUN', 'SPOT', 'AREA'
light.color = (1.0, 1.0, 1.0)
light.energy = 1000        # Watts (Cycles)
light.use_shadow = True

# Spot-specific
light.spot_size = 0.785     # Cone angle (radians)
light.spot_blend = 0.15     # Soft edge

# Area-specific
light.shape = 'RECTANGLE'   # 'SQUARE', 'RECTANGLE', 'DISK', 'ELLIPSE'
light.size = 2.0
light.size_y = 1.0           # Rectangle/Ellipse only
```

## Armature Data

```python
armature = obj.data  # type: bpy.types.Armature

# Edit mode bones (only in EDIT mode)
for bone in armature.edit_bones:
    bone.name
    bone.head          # Head position (Vector)
    bone.tail          # Tail position (Vector)
    bone.roll          # Roll angle
    bone.parent        # Parent EditBone or None
    bone.use_connect   # Connected to parent

# Object mode bones
for bone in armature.bones:
    bone.name
    bone.head_local    # Head in armature space
    bone.tail_local    # Tail in armature space
    bone.parent

# Pose bones (for constraints, transforms)
for pbone in obj.pose.bones:
    pbone.name
    pbone.location       # Pose-space location
    pbone.rotation_euler # Euler rotation
    pbone.rotation_quaternion
    pbone.scale
    pbone.constraints    # Bone constraints
    pbone.bone           # Reference to armature.bones entry
```

## Accessing Object Properties

```python
obj = bpy.data.objects["Cube"]

# Transform
obj.location             # (x, y, z)
obj.rotation_euler       # (x, y, z) Euler
obj.rotation_quaternion  # (w, x, y, z) Quaternion
obj.rotation_mode        # 'XYZ', 'QUATERNION', etc.
obj.scale                # (x, y, z)
obj.dimensions           # Bounding box dimensions
obj.matrix_world         # 4x4 world matrix
obj.matrix_local         # 4x4 local matrix
obj.matrix_basis         # Basis matrix (before constraints/parenting)

# Hierarchy
obj.parent               # Parent object
obj.parent_type          # 'OBJECT', 'BONE', 'VERTEX', etc.
obj.children             # Child objects

# Visibility
obj.hide_viewport        # Hidden in viewport
obj.hide_render          # Hidden in render
obj.hide_set(True)       # Set viewport visibility

# Custom properties
obj["my_prop"] = 42
obj["my_string"] = "hello"
val = obj.get("my_prop", 0)  # With default
```

## Dependency Graph (Depsgraph)

```python
depsgraph = bpy.context.evaluated_depsgraph_get()

# Get evaluated (final) object (with modifiers applied)
eval_obj = obj.evaluated_get(depsgraph)
eval_mesh = eval_obj.to_mesh()  # Evaluated mesh

# Clean up
eval_obj.to_mesh_clear()

# Iterate updates
for update in depsgraph.updates:
    print(f"Updated: {update.id.name} (geometry={update.is_updated_geometry})")

# Get evaluated objects
for obj_inst in depsgraph.object_instances:
    obj_inst.object         # The evaluated object
    obj_inst.matrix_world   # World matrix (including instances)
    obj_inst.is_instance    # True if this is an instance
```

## Collections

```python
# Scene collection (root)
scene_coll = bpy.context.scene.collection

# Create and link
coll = bpy.data.collections.new("My Collection")
scene_coll.children.link(coll)

# Nest collections
child_coll = bpy.data.collections.new("Child")
coll.children.link(child_coll)

# Add object to collection
coll.objects.link(obj)

# Remove object from collection
coll.objects.unlink(obj)

# Collection properties
coll.hide_viewport = False
coll.hide_render = False
coll.color_tag = 'COLOR_01'  # 'NONE', 'COLOR_01' through 'COLOR_08'

# Iterate hierarchy
def walk_collections(coll, level=0):
    print("  " * level + coll.name)
    for child in coll.children:
        walk_collections(child, level + 1)

walk_collections(scene_coll)
```

## View Layers

```python
view_layer = bpy.context.view_layer

# Active object
view_layer.objects.active = obj

# Layer collection visibility
def find_layer_collection(layer_coll, name):
    if layer_coll.name == name:
        return layer_coll
    for child in layer_coll.children:
        result = find_layer_collection(child, name)
        if result:
            return result
    return None

lc = find_layer_collection(view_layer.layer_collection, "My Collection")
lc.exclude = True    # Exclude from view layer
lc.hide_viewport = True  # Hide in viewport

# Render passes
view_layer.use_pass_z = True
view_layer.use_pass_normal = True
view_layer.use_pass_mist = True
```

## Animation Data

```python
# Check/create animation data
if obj.animation_data is None:
    obj.animation_data_create()

# Access action
action = obj.animation_data.action

# FCurves
for fcurve in action.fcurves:
    fcurve.data_path    # e.g., "location", "rotation_euler"
    fcurve.array_index  # Component index (0=X, 1=Y, 2=Z)
    for kp in fcurve.keyframe_points:
        kp.co           # (frame, value)
        kp.interpolation  # 'CONSTANT', 'LINEAR', 'BEZIER'

# NLA
for track in obj.animation_data.nla_tracks:
    for strip in track.strips:
        strip.action
        strip.frame_start
        strip.frame_end

# Drivers
for driver in obj.animation_data.drivers:
    driver.data_path
    driver.driver.expression
    for var in driver.driver.variables:
        var.name
        var.type  # 'SINGLE_PROP', 'TRANSFORMS', etc.
```

## Scenes

```python
scene = bpy.context.scene

scene.name
scene.frame_start
scene.frame_end
scene.frame_current
scene.frame_step
scene.render.fps
scene.render.fps_base

# Render settings
scene.render.engine           # 'CYCLES', 'BLENDER_EEVEE', 'BLENDER_WORKBENCH'
scene.render.resolution_x
scene.render.resolution_y
scene.render.resolution_percentage
scene.render.filepath
scene.render.image_settings.file_format  # 'PNG', 'JPEG', 'OPEN_EXR', 'FFMPEG', etc.

# Multiple scenes
for scene in bpy.data.scenes:
    print(scene.name)
```

## Linked/Appended Data

```python
# Append from another .blend file
filepath = "/path/to/file.blend"
with bpy.data.libraries.load(filepath) as (data_from, data_to):
    # List available data
    print(data_from.objects)
    print(data_from.materials)

    # Append specific items
    data_to.objects = ["Cube", "Sphere"]
    data_to.materials = ["Material.001"]

# Link (reference, not copy)
with bpy.data.libraries.load(filepath, link=True) as (data_from, data_to):
    data_to.collections = ["MyCollection"]

# After loading, link to scene
for obj in data_to.objects:
    if obj is not None:
        bpy.context.collection.objects.link(obj)
```

## Custom Properties

```python
# Simple custom properties
obj["my_int"] = 42
obj["my_float"] = 3.14
obj["my_string"] = "hello"
obj["my_list"] = [1, 2, 3]
obj["my_dict"] = {"key": "value"}

# Access with default
val = obj.get("nonexistent", "default")

# ID properties with metadata (for UI display)
import rna_prop_ui
rna_prop_ui.rna_idprop_ui_create(obj, "my_prop", default=1.0, min=0.0, max=10.0, description="My property")

# Remove
del obj["my_prop"]

# Iterate custom properties
for key in obj.keys():
    if key not in '_RNA_UI':  # Skip internal
        print(f"{key} = {obj[key]}")
```
