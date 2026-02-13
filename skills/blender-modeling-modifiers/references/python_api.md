# Blender Modeling & Modifiers - Python API Reference

## Modifier Management

### Adding Modifiers

```python
import bpy

obj = bpy.context.active_object

# Add modifier by type
mod = obj.modifiers.new(name="Subdivision", type='SUBSURF')
mod.levels = 2
mod.render_levels = 3
mod.subdivision_type = 'CATMULL_CLARK'

# Add multiple modifiers
mirror = obj.modifiers.new("Mirror", 'MIRROR')
mirror.use_axis = (True, False, False)
mirror.use_clip = True

bevel = obj.modifiers.new("Bevel", 'BEVEL')
bevel.width = 0.02
bevel.segments = 3
bevel.limit_method = 'WEIGHT'
```

### Applying Modifiers

```python
# Apply single modifier (object mode required)
bpy.ops.object.modifier_apply(modifier="Subdivision")

# Apply all modifiers
for mod in obj.modifiers[:]:  # Copy list since we're modifying it
    bpy.ops.object.modifier_apply(modifier=mod.name)

# Apply as shape key (for deform modifiers)
bpy.ops.object.modifier_apply_as_shapekey(modifier="Lattice")

# Apply modifier without bpy.ops (lower-level)
depsgraph = bpy.context.evaluated_depsgraph_get()
eval_obj = obj.evaluated_get(depsgraph)
mesh = bpy.data.meshes.new_from_object(eval_obj)
obj.data = mesh
obj.modifiers.clear()
```

### Removing and Reordering

```python
# Remove modifier
obj.modifiers.remove(obj.modifiers["Boolean"])

# Or by operator
bpy.ops.object.modifier_remove(modifier="Boolean")

# Move modifier
bpy.ops.object.modifier_move_up(modifier="Bevel")
bpy.ops.object.modifier_move_down(modifier="Mirror")

# Move to specific index (Blender 4.x+)
bpy.ops.object.modifier_move_to_index(modifier="Bevel", index=0)
```

### Accessing Modifier Properties

```python
# By name
mod = obj.modifiers["Subdivision"]
mod.levels = 3

# Iterate all modifiers
for mod in obj.modifiers:
    print(f"{mod.name}: {mod.type}")
    mod.show_viewport = True
    mod.show_render = True

# Check if modifier exists
if "Mirror" in obj.modifiers:
    print("Has mirror modifier")
```

## Common Modifier Configurations

### Subdivision Surface

```python
mod = obj.modifiers.new("Subdiv", 'SUBSURF')
mod.levels = 2                    # Viewport subdivisions
mod.render_levels = 3             # Render subdivisions
mod.subdivision_type = 'CATMULL_CLARK'  # or 'SIMPLE'
mod.uv_smooth = 'PRESERVE_CORNERS'     # NONE, PRESERVE_CORNERS, PRESERVE_CORNERS_AND_JUNCTIONS, PRESERVE_CORNERS_JUNCTIONS_AND_CONCAVE, PRESERVE_BOUNDARIES, SMOOTH_ALL
mod.quality = 3                   # Quality level (1-6)
mod.show_only_control_edges = False
mod.use_creases = True            # Support edge creases
mod.use_limit_surface = False     # Limit surface (exact subdivision)
```

### Array

```python
mod = obj.modifiers.new("Array", 'ARRAY')
mod.fit_type = 'FIXED_COUNT'      # FIXED_COUNT, FIT_LENGTH, FIT_CURVE
mod.count = 5

# Relative offset (units of object dimensions)
mod.use_relative_offset = True
mod.relative_offset_displace = (1.0, 0.0, 0.0)  # Side by side on X

# Constant offset (world units)
mod.use_constant_offset = True
mod.constant_offset_displace = (2.0, 0.0, 0.0)

# Object offset (transform from another object)
mod.use_object_offset = True
mod.offset_object = bpy.data.objects["Empty"]

# Merge vertices
mod.use_merge_vertices = True
mod.use_merge_vertices_cap = True
mod.merge_threshold = 0.001

# Fit to curve
mod.fit_type = 'FIT_CURVE'
mod.curve = bpy.data.objects["BezierCurve"]
```

### Mirror

```python
mod = obj.modifiers.new("Mirror", 'MIRROR')
mod.use_axis = (True, False, False)           # Mirror on X
mod.use_bisect_axis = (False, False, False)   # Bisect on axis
mod.use_bisect_flip_axis = (False, False, False)
mod.use_clip = True                           # Prevent vertices crossing
mod.use_mirror_merge = True
mod.merge_threshold = 0.001
mod.mirror_object = None                      # Optional mirror center object
mod.use_mirror_vertex_groups = True           # Mirror vertex groups (.L <-> .R)
mod.use_mirror_u = False                      # Mirror UV U axis
mod.use_mirror_v = False                      # Mirror UV V axis
```

### Solidify

```python
mod = obj.modifiers.new("Solidify", 'SOLIDIFY')
mod.thickness = 0.05
mod.offset = -1.0                 # -1 = outward, 0 = centered, 1 = inward
mod.use_rim = True                # Fill rim edges
mod.use_rim_only = False          # Only rim (hollow)
mod.use_even_offset = True        # Even thickness
mod.use_quality_normals = True    # Better normal calculation
mod.solidify_mode = 'EXTRUDE'    # EXTRUDE or NON_MANIFOLD
mod.material_offset = 1           # Material index for rim/inner faces
mod.material_offset_rim = 1
```

### Bevel

```python
mod = obj.modifiers.new("Bevel", 'BEVEL')
mod.width = 0.05
mod.segments = 3
mod.profile = 0.5                 # 0-1, 0.5 = round
mod.affect = 'EDGES'             # EDGES or VERTICES
mod.limit_method = 'ANGLE'       # NONE, ANGLE, WEIGHT, VGROUP
mod.angle_limit = 0.523599        # ~30 degrees (radians)
mod.offset_type = 'OFFSET'       # OFFSET, WIDTH, DEPTH, PERCENT, ABSOLUTE
mod.miter_outer = 'SHARP'        # SHARP, PATCH, ARC
mod.miter_inner = 'SHARP'
mod.harden_normals = True         # Harden normals at bevel
mod.use_clamp_overlap = True
mod.loop_slide = True
```

### Shrinkwrap

```python
mod = obj.modifiers.new("Shrinkwrap", 'SHRINKWRAP')
mod.target = bpy.data.objects["TargetMesh"]
mod.wrap_method = 'NEAREST_SURFACEPOINT'  # NEAREST_SURFACEPOINT, PROJECT, NEAREST_VERTEX, TARGET_PROJECT
mod.wrap_mode = 'ON_SURFACE'     # ON_SURFACE, INSIDE, OUTSIDE, OUTSIDE_SURFACE, ABOVE_SURFACE
mod.offset = 0.0                 # Distance from target surface
mod.vertex_group = ""            # Limit to vertex group

# Project-specific
mod.use_project_x = False
mod.use_project_y = False
mod.use_project_z = True
mod.use_negative_direction = True
mod.use_positive_direction = True
mod.project_limit = 0.0          # Max projection distance (0 = unlimited)
mod.cull_face = 'OFF'            # OFF, FRONT, BACK
```

### Simple Deform

```python
mod = obj.modifiers.new("Twist", 'SIMPLE_DEFORM')
mod.deform_method = 'TWIST'     # TWIST, BEND, TAPER, STRETCH
mod.deform_axis = 'Z'           # X, Y, Z
mod.angle = 1.5708              # Radians (90 degrees)
mod.factor = 1.0                # For TAPER/STRETCH
mod.limits = (0.0, 1.0)         # Limit deformation range (0-1)
mod.origin = bpy.data.objects.get("Empty")  # Optional origin object
mod.lock_x = False
mod.lock_y = False
```

### Curve Deform

```python
mod = obj.modifiers.new("Curve", 'CURVE')
mod.object = bpy.data.objects["BezierCurve"]
mod.deform_axis = 'POS_X'       # POS_X, POS_Y, POS_Z, NEG_X, NEG_Y, NEG_Z
mod.vertex_group = ""
```

### Lattice

```python
# Create lattice
lattice_data = bpy.data.lattices.new("MyLattice")
lattice_data.points_u = 3
lattice_data.points_v = 3
lattice_data.points_w = 3

lattice_obj = bpy.data.objects.new("MyLattice", lattice_data)
bpy.context.collection.objects.link(lattice_obj)
lattice_obj.scale = obj.dimensions * 1.1  # Slightly larger than target

# Add modifier
mod = obj.modifiers.new("Lattice", 'LATTICE')
mod.object = lattice_obj
mod.strength = 1.0
mod.vertex_group = ""

# Deform lattice points
for pt in lattice_data.points:
    pt.co_deform  # Deformed position (modify this)
```

### Weighted Normal

```python
mod = obj.modifiers.new("WNormal", 'WEIGHTED_NORMAL')
mod.mode = 'FACE_AREA'           # FACE_AREA, CORNER_ANGLE, FACE_AREA_AND_ANGLE
mod.weight = 50                   # Weight factor
mod.thresh = 0.01
mod.keep_sharp = True             # Preserve sharp edges
mod.face_influence = False        # Use face influence from Face Strength
```

## bmesh API

### Creating Geometry

```python
import bpy
import bmesh
from mathutils import Vector, Matrix
import math

# Create bmesh
bm = bmesh.new()

# Create vertices
v1 = bm.verts.new((0, 0, 0))
v2 = bm.verts.new((1, 0, 0))
v3 = bm.verts.new((1, 1, 0))
v4 = bm.verts.new((0, 1, 0))
v5 = bm.verts.new((0.5, 0.5, 1))

# Create edges
bm.edges.new((v1, v2))

# Create faces
face = bm.faces.new((v1, v2, v3, v4))  # Quad
tri1 = bm.faces.new((v1, v2, v5))       # Triangle
tri2 = bm.faces.new((v2, v3, v5))

# Ensure lookup tables are valid
bm.verts.ensure_lookup_table()
bm.edges.ensure_lookup_table()
bm.faces.ensure_lookup_table()

# Write to mesh
mesh = bpy.data.meshes.new("MyMesh")
bm.to_mesh(mesh)
bm.free()

obj = bpy.data.objects.new("MyObject", mesh)
bpy.context.collection.objects.link(obj)
```

### Primitive Generation

```python
import bmesh

bm = bmesh.new()

# Create primitives
bmesh.ops.create_cube(bm, size=2.0)
# bmesh.ops.create_uvsphere(bm, u_segments=32, v_segments=16, radius=1.0)
# bmesh.ops.create_icosphere(bm, subdivisions=2, radius=1.0)
# bmesh.ops.create_cone(bm, segments=32, radius1=1.0, radius2=0.0, depth=2.0)
# bmesh.ops.create_grid(bm, x_segments=10, y_segments=10, size=2.0)
# bmesh.ops.create_circle(bm, segments=32, radius=1.0)
```

### Mesh Operations

```python
bm = bmesh.new()
bm.from_mesh(obj.data)

# Subdivide
bmesh.ops.subdivide_edges(bm, edges=bm.edges[:], cuts=1, use_grid_fill=True)

# Extrude faces
result = bmesh.ops.extrude_face_region(bm, geom=bm.faces[:])
extruded_verts = [v for v in result['geom'] if isinstance(v, bmesh.types.BMVert)]
bmesh.ops.translate(bm, vec=(0, 0, 1), verts=extruded_verts)

# Inset faces
bmesh.ops.inset_region(bm, faces=bm.faces[:], thickness=0.1, depth=0.0)

# Scale
bmesh.ops.scale(bm, vec=(2, 2, 2), verts=bm.verts[:])

# Rotate
bmesh.ops.rotate(bm,
    cent=(0, 0, 0),
    matrix=Matrix.Rotation(math.radians(45), 3, 'Z'),
    verts=bm.verts[:])

# Translate
bmesh.ops.translate(bm, vec=(0, 0, 1), verts=bm.verts[:])

# Mirror
bmesh.ops.mirror(bm, geom=bm.verts[:] + bm.edges[:] + bm.faces[:],
    axis='X', merge_dist=0.001)

# Remove doubles
bmesh.ops.remove_doubles(bm, verts=bm.verts[:], dist=0.0001)

# Recalculate normals
bmesh.ops.recalc_face_normals(bm, faces=bm.faces[:])

# Dissolve edges/faces
bmesh.ops.dissolve_edges(bm, edges=selected_edges)
bmesh.ops.dissolve_faces(bm, faces=selected_faces)

# Delete geometry
bmesh.ops.delete(bm, geom=bm.faces[:], context='FACES')
# context: 'VERTS', 'EDGES', 'FACES_ONLY', 'EDGES_FACES', 'FACES', 'FACES_KEEP_BOUNDARY', 'TAGGED_ONLY'

# Boolean
bmesh.ops.boolean(bm, geom=bm.faces[:], object=other_bm, operation='DIFFERENCE')

# Write back
bm.to_mesh(obj.data)
bm.free()
obj.data.update()
```

### Selection and Filtering

```python
bm = bmesh.new()
bm.from_mesh(obj.data)

# Select by criteria
for v in bm.verts:
    if v.co.z > 0.5:
        v.select = True

# Get selected elements
selected_verts = [v for v in bm.verts if v.select]
selected_edges = [e for e in bm.edges if e.select]
selected_faces = [f for f in bm.faces if f.select]

# Select by face normal
import math
for f in bm.faces:
    angle = f.normal.angle(Vector((0, 0, 1)))
    if angle < math.radians(30):  # Faces pointing up
        f.select = True

# Select linked (flood fill from vertex)
# Use BMesh's built-in linked selection
```

### Vertex Groups in bmesh

```python
bm = bmesh.new()
bm.from_mesh(obj.data)

# Get deform layer
deform_layer = bm.verts.layers.deform.active
if deform_layer is None:
    deform_layer = bm.verts.layers.deform.new()

# Get vertex group index
vg_index = obj.vertex_groups["MyGroup"].index

# Assign weights
for v in bm.verts:
    v[deform_layer][vg_index] = 1.0  # Full weight

bm.to_mesh(obj.data)
bm.free()
```

### UV Maps in bmesh

```python
bm = bmesh.new()
bm.from_mesh(obj.data)

# Get or create UV layer
uv_layer = bm.loops.layers.uv.active
if uv_layer is None:
    uv_layer = bm.loops.layers.uv.new("UVMap")

# Set UVs
for face in bm.faces:
    for loop in face.loops:
        uv = loop[uv_layer]
        uv.uv = (loop.vert.co.x, loop.vert.co.y)  # Simple planar projection

bm.to_mesh(obj.data)
bm.free()
```

### Color Attributes in bmesh

```python
bm = bmesh.new()
bm.from_mesh(obj.data)

# Get or create color layer
color_layer = bm.loops.layers.color.active
if color_layer is None:
    color_layer = bm.loops.layers.color.new("Color")

# Set colors
for face in bm.faces:
    for loop in face.loops:
        loop[color_layer] = (1.0, 0.0, 0.0, 1.0)  # Red

bm.to_mesh(obj.data)
bm.free()
```

## Mesh Operators (bpy.ops.mesh)

These require EDIT mode:

```python
bpy.ops.object.mode_set(mode='EDIT')

# Selection
bpy.ops.mesh.select_all(action='SELECT')     # SELECT, DESELECT, INVERT, TOGGLE
bpy.ops.mesh.select_linked()                   # Select connected
bpy.ops.mesh.select_loose()                    # Select loose vertices/edges
bpy.ops.mesh.select_non_manifold()             # Select non-manifold
bpy.ops.mesh.select_face_by_sides(number=4, type='EQUAL')  # Select by polygon sides
bpy.ops.mesh.select_similar(type='NORMAL', threshold=0.1)

# Editing
bpy.ops.mesh.extrude_region_move(TRANSFORM_OT_translate={"value": (0, 0, 1)})
bpy.ops.mesh.inset(thickness=0.1, depth=0.0)
bpy.ops.mesh.bevel(offset=0.1, segments=3)
bpy.ops.mesh.subdivide(number_cuts=1)
bpy.ops.mesh.loopcut_slide(MESH_OT_loopcut={"number_cuts": 1})
bpy.ops.mesh.knife_tool()  # Interactive

# Merge/Split
bpy.ops.mesh.remove_doubles(threshold=0.0001)  # Merge by distance
bpy.ops.mesh.merge(type='CENTER')               # CENTER, CURSOR, COLLAPSE
bpy.ops.mesh.split()                             # Split selected
bpy.ops.mesh.separate(type='SELECTED')           # SELECTED, MATERIAL, LOOSE

# Normals
bpy.ops.mesh.normals_make_consistent(inside=False)
bpy.ops.mesh.flip_normals()
bpy.ops.mesh.set_normals_from_faces()

# Delete
bpy.ops.mesh.delete(type='VERT')    # VERT, EDGE, FACE, EDGE_FACE, ONLY_FACE
bpy.ops.mesh.dissolve_verts()
bpy.ops.mesh.dissolve_edges()
bpy.ops.mesh.dissolve_faces()

# Fill
bpy.ops.mesh.fill()                  # Fill selected edge loop
bpy.ops.mesh.fill_grid()             # Grid fill (two edge loops)
bpy.ops.mesh.bridge_edge_loops()     # Bridge two edge loops

# Transform
bpy.ops.mesh.vertices_smooth(factor=0.5, repeat=1)
bpy.ops.mesh.smooth_normals()

# UVs
bpy.ops.mesh.uv_texture_add()
bpy.ops.uv.unwrap(method='ANGLE_BASED')
bpy.ops.uv.smart_project()
bpy.ops.uv.cube_project()
bpy.ops.uv.cylinder_project()
bpy.ops.uv.sphere_project()

bpy.ops.object.mode_set(mode='OBJECT')
```

## Object-Level Operations

```python
# Join objects
bpy.ops.object.select_all(action='DESELECT')
for o in objects_to_join:
    o.select_set(True)
bpy.context.view_layer.objects.active = objects_to_join[0]
bpy.ops.object.join()

# Separate by loose parts
bpy.ops.mesh.separate(type='LOOSE')

# Convert types
bpy.ops.object.convert(target='MESH')      # Curve/Text/etc to Mesh
bpy.ops.object.convert(target='CURVE')      # Mesh to Curve

# Apply transforms
bpy.ops.object.transform_apply(location=True, rotation=True, scale=True)

# Set origin
bpy.ops.object.origin_set(type='ORIGIN_CENTER_OF_MASS')
# ORIGIN_GEOMETRY, ORIGIN_CENTER_OF_MASS, ORIGIN_CENTER_OF_VOLUME, ORIGIN_CURSOR, GEOMETRY_ORIGIN

# Shade smooth/flat
bpy.ops.object.shade_smooth()
bpy.ops.object.shade_flat()
```
