# Blender Animation & Rigging - Python API Reference

## Keyframe Insertion

### Object Keyframes

```python
import bpy

obj = bpy.context.active_object

# Insert keyframes on transform properties
obj.location = (0, 0, 0)
obj.keyframe_insert(data_path="location", frame=1)

obj.rotation_euler = (0, 0, 1.5708)  # 90 degrees
obj.keyframe_insert(data_path="rotation_euler", frame=30)

obj.scale = (2, 2, 2)
obj.keyframe_insert(data_path="scale", frame=30)

# Single channel
obj.keyframe_insert(data_path="location", index=0, frame=1)  # X only
obj.keyframe_insert(data_path="location", index=2, frame=1)  # Z only

# Remove keyframe
obj.keyframe_delete(data_path="location", frame=1)

# Custom property keyframe
obj["my_prop"] = 0.0
obj.keyframe_insert(data_path='["my_prop"]', frame=1)
obj["my_prop"] = 1.0
obj.keyframe_insert(data_path='["my_prop"]', frame=60)
```

### Bone Keyframes (Pose Mode)

```python
arm_obj = bpy.data.objects["Armature"]
pose_bone = arm_obj.pose.bones["Bone"]

pose_bone.location = (0, 0, 0)
pose_bone.keyframe_insert(data_path="location", frame=1)

pose_bone.rotation_euler = (0, 0, 0.5)
pose_bone.keyframe_insert(data_path="rotation_euler", frame=1)

# Quaternion rotation (default for bones)
pose_bone.rotation_quaternion = (1, 0, 0, 0)
pose_bone.keyframe_insert(data_path="rotation_quaternion", frame=1)
```

### Material/Node Keyframes

```python
mat = bpy.data.materials["Material"]
node = mat.node_tree.nodes["Principled BSDF"]

# Keyframe node input
node.inputs['Roughness'].default_value = 0.1
node.inputs['Roughness'].keyframe_insert(data_path="default_value", frame=1)

node.inputs['Roughness'].default_value = 0.9
node.inputs['Roughness'].keyframe_insert(data_path="default_value", frame=60)
```

### Constraint Keyframes

```python
con = obj.constraints["Copy Location"]
con.influence = 1.0
con.keyframe_insert(data_path="influence", frame=1)
con.influence = 0.0
con.keyframe_insert(data_path="influence", frame=30)
```

## FCurve Manipulation

### Accessing FCurves

```python
# Get animation data
anim_data = obj.animation_data
action = anim_data.action

# Iterate all FCurves
for fcurve in action.fcurves:
    print(f"Path: {fcurve.data_path}, Index: {fcurve.array_index}")
    print(f"  Points: {len(fcurve.keyframe_points)}")

# Find specific FCurve
fc = action.fcurves.find("location", index=0)  # X location
if fc:
    print(f"X Location has {len(fc.keyframe_points)} keyframes")
```

### Modifying Keyframe Points

```python
fc = action.fcurves.find("location", index=0)

# Add keyframe point
kp = fc.keyframe_points.insert(frame=15, value=3.0)
kp.interpolation = 'BEZIER'  # CONSTANT, LINEAR, BEZIER, SINE, QUAD, CUBIC, etc.
kp.easing = 'AUTO'           # AUTO, EASE_IN, EASE_OUT, EASE_IN_OUT
kp.handle_left_type = 'AUTO_CLAMPED'   # FREE, ALIGNED, VECTOR, AUTO, AUTO_CLAMPED
kp.handle_right_type = 'AUTO_CLAMPED'

# Modify existing keyframe
for kp in fc.keyframe_points:
    kp.co.x  # Frame number
    kp.co.y  # Value
    kp.interpolation = 'LINEAR'

# Remove keyframe at specific frame
# (find index first, then remove)
for i, kp in enumerate(fc.keyframe_points):
    if kp.co.x == 30:
        fc.keyframe_points.remove(fc.keyframe_points[i])
        break

# Update FCurve after modifications
fc.update()
```

### FCurve Modifiers

```python
fc = action.fcurves.find("location", index=2)  # Z location

# Add noise modifier
mod = fc.modifiers.new('NOISE')
mod.strength = 0.5
mod.scale = 2.0
mod.phase = 0.0
mod.depth = 0

# Add cycles modifier (loop)
mod = fc.modifiers.new('CYCLES')
mod.mode_before = 'REPEAT'      # NONE, REPEAT, REPEAT_OFFSET, MIRROR
mod.mode_after = 'REPEAT'
mod.cycles_before = 0           # 0 = infinite
mod.cycles_after = 0

# Add envelope modifier
mod = fc.modifiers.new('ENVELOPE')

# Add generator (function)
mod = fc.modifiers.new('GENERATOR')
mod.mode = 'POLYNOMIAL'         # POLYNOMIAL, POLYNOMIAL_FACTORED
mod.poly_order = 1              # Linear
mod.coefficients = (0.0, 1.0)   # y = 0 + 1*x

# Add stepped modifier
mod = fc.modifiers.new('STEPPED')
mod.frame_step = 2              # Hold for 2 frames
mod.frame_offset = 0

# Add limits modifier
mod = fc.modifiers.new('LIMITS')
mod.use_min_y = True
mod.min_y = 0.0
mod.use_max_y = True
mod.max_y = 10.0
```

### FCurve Sampling and Evaluation

```python
fc = action.fcurves.find("location", index=0)

# Evaluate at specific frame
value = fc.evaluate(frame=15.5)

# Sample to keyframes (bake)
fc.convert_to_samples(start=1, end=100)

# Convert samples back to keyframes
fc.convert_to_keyframes(start=1, end=100)

# Get range
frame_range = fc.range()  # (min_frame, max_frame)
```

## Actions

### Creating and Managing Actions

```python
# Create new action
action = bpy.data.actions.new(name="WalkCycle")

# Assign to object
if obj.animation_data is None:
    obj.animation_data_create()
obj.animation_data.action = action

# Duplicate action
new_action = action.copy()
new_action.name = "RunCycle"

# Remove action
bpy.data.actions.remove(action)

# Fake user (prevent deletion)
action.use_fake_user = True

# Action properties
action.frame_range        # (start, end) computed from keyframes
action.frame_start        # Manual override start
action.frame_end          # Manual override end
action.use_frame_range    # Use manual range
```

## Drivers

### Adding Drivers

```python
# Add driver (returns FCurve)
fcurve = obj.driver_add("location", 0)  # X location

# Access driver
driver = fcurve.driver

# Driver types
driver.type = 'SCRIPTED'       # Python expression
# driver.type = 'AVERAGE'      # Average of variables
# driver.type = 'SUM'          # Sum of variables
# driver.type = 'MIN'          # Minimum
# driver.type = 'MAX'          # Maximum

# Set expression
driver.expression = "var1 + var2 * 2"
```

### Driver Variables

```python
# Single Property
var = driver.variables.new()
var.name = "my_var"
var.type = 'SINGLE_PROP'
var.targets[0].id_type = 'OBJECT'
var.targets[0].id = bpy.data.objects["Target"]
var.targets[0].data_path = "location.x"

# Transform channel
var = driver.variables.new()
var.name = "tx"
var.type = 'TRANSFORMS'
var.targets[0].id = bpy.data.objects["Target"]
var.targets[0].bone_target = "BoneName"  # Optional, for armatures
var.targets[0].transform_type = 'LOC_X'
var.targets[0].transform_space = 'WORLD_SPACE'  # WORLD_SPACE, TRANSFORM_SPACE, LOCAL_SPACE

# Rotation Difference (angle between two bones)
var = driver.variables.new()
var.name = "angle"
var.type = 'ROTATION_DIFF'
var.targets[0].id = arm_obj
var.targets[0].bone_target = "Bone1"
var.targets[1].id = arm_obj
var.targets[1].bone_target = "Bone2"

# Distance
var = driver.variables.new()
var.name = "dist"
var.type = 'LOC_DIFF'
var.targets[0].id = bpy.data.objects["Obj1"]
var.targets[1].id = bpy.data.objects["Obj2"]
```

### Removing Drivers

```python
obj.driver_remove("location", 0)  # Remove driver from X location
obj.driver_remove("location")     # Remove drivers from all location channels
```

## Armature Setup

### Create Armature with Python

```python
import bpy
from mathutils import Vector, Matrix

# Create armature data
armature_data = bpy.data.armatures.new("Skeleton")
armature_data.display_type = 'OCTAHEDRAL'  # OCTAHEDRAL, STICK, BBONE, ENVELOPE, WIRE

# Create armature object
arm_obj = bpy.data.objects.new("Skeleton", armature_data)
bpy.context.collection.objects.link(arm_obj)
bpy.context.view_layer.objects.active = arm_obj
arm_obj.select_set(True)

# Enter edit mode
bpy.ops.object.mode_set(mode='EDIT')
edit_bones = armature_data.edit_bones

# Create bones
bone = edit_bones.new("Hip")
bone.head = (0, 0, 1.0)
bone.tail = (0, 0, 1.3)

spine = edit_bones.new("Spine")
spine.head = bone.tail
spine.tail = (0, 0, 1.6)
spine.parent = bone
spine.use_connect = True

# Exit edit mode
bpy.ops.object.mode_set(mode='OBJECT')
```

### Edit Bone Properties

```python
# In EDIT mode
bone = armature_data.edit_bones["Bone"]

bone.head = (0, 0, 0)          # Head position
bone.tail = (0, 0, 1)          # Tail position
bone.roll = 0.0                 # Bone roll (twist along length)
bone.parent = other_bone        # Parent bone
bone.use_connect = True         # Connected to parent's tail
bone.use_deform = True          # Used for mesh deformation
bone.use_inherit_rotation = True
bone.use_local_location = True
bone.use_inherit_scale = 'FULL'  # FULL, FIX_SHEAR, ALIGNED, NONE, AVERAGE, NONE_LEGACY
bone.layers = [True] + [False]*31  # Bone layers (32 layers)
bone.envelope_distance = 0.25   # Envelope radius (head)
bone.envelope_weight = 1.0      # Envelope weight

# B-Bone properties
bone.bbone_segments = 1         # Number of segments (1 = straight)
bone.bbone_curveinx = 0.0       # In handle X curve
bone.bbone_curveiny = 0.0       # In handle Y curve
bone.bbone_curveoutx = 0.0      # Out handle X curve
bone.bbone_curveouty = 0.0      # Out handle Y curve
bone.bbone_scalein = (1, 1, 1)  # In scale
bone.bbone_scaleout = (1, 1, 1) # Out scale
```

### Pose Bone Properties

```python
# In OBJECT or POSE mode
pose_bone = arm_obj.pose.bones["Bone"]

pose_bone.location             # Local offset from rest position
pose_bone.rotation_mode        # 'QUATERNION', 'XYZ', 'XZY', etc.
pose_bone.rotation_quaternion  # Quaternion rotation
pose_bone.rotation_euler       # Euler rotation
pose_bone.scale                # Scale

# Read-only computed matrices
pose_bone.matrix               # Pose-space matrix
pose_bone.matrix_basis         # Basis matrix (local transform only)
pose_bone.head                 # Posed head position
pose_bone.tail                 # Posed tail position

# Bone groups / colors
pose_bone.color.palette = 'THEME01'  # THEME01-THEME20, DEFAULT, CUSTOM

# Custom shape
pose_bone.custom_shape = bpy.data.objects["WidgetMesh"]
pose_bone.custom_shape_scale_xyz = (1, 1, 1)
pose_bone.use_custom_shape_bone_size = True
```

### Bone Constraints

```python
bpy.ops.object.mode_set(mode='POSE')
pose_bone = arm_obj.pose.bones["Bone"]

# Add constraint
con = pose_bone.constraints.new('COPY_ROTATION')
con.target = arm_obj
con.subtarget = "TargetBone"
con.influence = 1.0

# Remove constraint
pose_bone.constraints.remove(con)

# Iterate constraints
for con in pose_bone.constraints:
    print(f"{con.name}: {con.type}, influence={con.influence}")

# Reorder constraints
pose_bone.constraints.move(from_index=0, to_index=1)
```

## NLA Editor

### NLA Tracks and Strips

```python
# Ensure animation data
if obj.animation_data is None:
    obj.animation_data_create()

anim_data = obj.animation_data

# Create NLA track
track = anim_data.nla_tracks.new()
track.name = "Base Animation"
track.mute = False
track.is_solo = False
track.lock = False

# Add strip from action
action = bpy.data.actions["WalkCycle"]
strip = track.strips.new(name="Walk", start=1, action=action)

# Strip properties
strip.frame_start = 1
strip.frame_end = action.frame_range[1]
strip.action_frame_start = action.frame_range[0]
strip.action_frame_end = action.frame_range[1]
strip.repeat = 1.0
strip.scale = 1.0
strip.blend_type = 'REPLACE'   # REPLACE, COMBINE, ADD, SUBTRACT, MULTIPLY
strip.influence = 1.0
strip.blend_in = 0.0           # Fade-in frames
strip.blend_out = 0.0          # Fade-out frames
strip.mute = False
strip.use_auto_blend = True
strip.use_reverse = False
strip.extrapolation = 'HOLD'   # NOTHING, HOLD, HOLD_FORWARD

# Remove strip
track.strips.remove(strip)

# Remove track
anim_data.nla_tracks.remove(track)
```

### Stashing Actions

```python
# Stash current action to NLA (non-destructive storage)
track = anim_data.nla_tracks.new()
track.name = "[Action Stash]"
strip = track.strips.new(name=action.name, start=0, action=action)
strip.mute = True
track.mute = True
anim_data.action = None
```

## Shape Keys

### Creating Shape Keys

```python
obj = bpy.context.active_object
mesh = obj.data

# Create basis shape (required first)
basis = obj.shape_key_add(name="Basis", from_mix=False)

# Add shape keys
shape1 = obj.shape_key_add(name="Wide", from_mix=False)
for v in shape1.data:
    v.co.x *= 1.5  # Scale X

shape2 = obj.shape_key_add(name="Tall", from_mix=False)
for v in shape2.data:
    v.co.z *= 1.5  # Scale Z

# From current mesh state (mix of existing keys)
mixed = obj.shape_key_add(name="Mixed", from_mix=True)
```

### Shape Key Access

```python
shape_keys = mesh.shape_keys        # ShapeKey data
key_blocks = shape_keys.key_blocks  # All shape key blocks

# By name
key = key_blocks["Wide"]

# Properties
key.value = 0.5                     # Blend weight
key.slider_min = 0.0
key.slider_max = 1.0
key.mute = False
key.relative_key = key_blocks["Basis"]
key.vertex_group = ""               # Limit to vertex group
key.interpolation = 'KEY_LINEAR'    # KEY_LINEAR, KEY_CARDINAL, KEY_CATMULL_ROM, KEY_BSPLINE

# Per-vertex data
for i, sv in enumerate(key.data):
    sv.co                           # Shape key vertex position (Vector)
```

### Animating Shape Keys

```python
key_blocks = mesh.shape_keys.key_blocks

# Keyframe shape key value
key_blocks["Wide"].value = 0.0
key_blocks["Wide"].keyframe_insert("value", frame=1)

key_blocks["Wide"].value = 1.0
key_blocks["Wide"].keyframe_insert("value", frame=30)
```

### Shape Key Drivers

```python
# Drive shape key from bone rotation
shape_key = mesh.shape_keys.key_blocks["Smile"]
fcurve = shape_key.driver_add("value")
driver = fcurve.driver
driver.type = 'SCRIPTED'

var = driver.variables.new()
var.name = "jaw_rot"
var.type = 'TRANSFORMS'
var.targets[0].id = bpy.data.objects["Armature"]
var.targets[0].bone_target = "Jaw"
var.targets[0].transform_type = 'ROT_X'
var.targets[0].transform_space = 'LOCAL_SPACE'

driver.expression = "jaw_rot * -5.0"  # Scale rotation to 0-1 range
```

## Bone Collections (Blender 4.0+ / 5.x)

```python
armature = arm_obj.data

# Bone collections replace bone layers in Blender 4.0+
coll = armature.collections.new("IK Controls")
coll_deform = armature.collections.new("Deform")
coll_mech = armature.collections.new("Mechanism")

# Assign bones to collections (in edit mode)
bpy.ops.object.mode_set(mode='EDIT')
bone = armature.edit_bones["IK_Hand.L"]
coll.assign(bone)

# Toggle visibility
coll.is_visible = True
coll_mech.is_visible = False

bpy.ops.object.mode_set(mode='OBJECT')
```

## Useful Animation Utilities

### Set Interpolation for All Keyframes

```python
for fcurve in obj.animation_data.action.fcurves:
    for kp in fcurve.keyframe_points:
        kp.interpolation = 'LINEAR'
    fcurve.update()
```

### Bake Animation

```python
# Select object, then bake
bpy.context.view_layer.objects.active = obj
obj.select_set(True)
bpy.ops.nla.bake(
    frame_start=1,
    frame_end=100,
    only_selected=True,
    visual_keying=True,
    clear_constraints=False,
    clear_parents=False,
    use_current_action=True,
    bake_types={'OBJECT'}  # or {'POSE'} for armatures
)
```

### Clear Animation

```python
# Clear all animation from object
if obj.animation_data:
    obj.animation_data.action = None
    # Or remove animation data entirely
    obj.animation_data_clear()
```
