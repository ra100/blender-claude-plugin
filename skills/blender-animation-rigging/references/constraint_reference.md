# Blender 5.0 Constraints - Complete Reference

Approximately 45 constraints across 4 categories: Motion Tracking, Transform, Tracking, and Relationship.

## Adding Constraints via Python

```python
# Object constraints
constraint = obj.constraints.new(type='COPY_LOCATION')

# Bone constraints (in pose mode)
pose_bone = arm_obj.pose.bones["BoneName"]
constraint = pose_bone.constraints.new(type='COPY_LOCATION')

# Common properties
constraint.name = "My Constraint"
constraint.influence = 1.0        # 0.0 to 1.0
constraint.mute = False           # Disable without removing
constraint.target = target_obj    # Target object
constraint.subtarget = "BoneName" # Target bone (if target is armature)
```

---

## 1. MOTION TRACKING CONSTRAINTS

| Constraint    | Type String       | Description                                         | Key Properties                                               |
| ------------- | ----------------- | --------------------------------------------------- | ------------------------------------------------------------ |
| Camera Solver | `'CAMERA_SOLVER'` | Moves object to match tracked camera motion         | `clip`, `use_active_clip`                                    |
| Follow Track  | `'FOLLOW_TRACK'`  | Positions object at a 2D tracking point in 3D space | `clip`, `track`, `camera`, `depth_object`, `use_3d_position` |
| Object Solver | `'OBJECT_SOLVER'` | Moves object to match an object-tracked motion      | `clip`, `camera`, `object`                                   |

---

## 2. TRANSFORM CONSTRAINTS

### Copy Constraints

| Constraint      | Type String         | Description                    | Key Properties                                                                                                                                               |
| --------------- | ------------------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Copy Location   | `'COPY_LOCATION'`   | Copies target's location       | `target`, `subtarget`, `use_x/y/z`, `invert_x/y/z`, `use_offset`, `target_space`, `owner_space`                                                              |
| Copy Rotation   | `'COPY_ROTATION'`   | Copies target's rotation       | `target`, `subtarget`, `use_x/y/z`, `invert_x/y/z`, `mix_mode` ('REPLACE', 'ADD', 'BEFORE', 'AFTER', 'OFFSET'), `target_space`, `owner_space`, `euler_order` |
| Copy Scale      | `'COPY_SCALE'`      | Copies target's scale          | `target`, `subtarget`, `use_x/y/z`, `power`, `use_make_uniform`, `use_offset`, `use_add`, `target_space`, `owner_space`                                      |
| Copy Transforms | `'COPY_TRANSFORMS'` | Copies entire transform matrix | `target`, `subtarget`, `mix_mode` ('REPLACE', 'BEFORE_FULL', 'BEFORE', 'BEFORE_SPLIT', 'AFTER_FULL', 'AFTER', 'AFTER_SPLIT'), `target_space`, `owner_space`  |

### Limit Constraints

| Constraint     | Type String        | Description                 | Key Properties                                                                                                                                         |
| -------------- | ------------------ | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Limit Distance | `'LIMIT_DISTANCE'` | Clamps distance from target | `target`, `subtarget`, `distance`, `limit_mode` ('LIMITDIST_INSIDE', 'LIMITDIST_OUTSIDE', 'LIMITDIST_ONSURFACE'), `use_transform_limit`, `owner_space` |
| Limit Location | `'LIMIT_LOCATION'` | Clamps location to range    | `use_min_x/y/z`, `use_max_x/y/z`, `min_x/y/z`, `max_x/y/z`, `owner_space`                                                                              |
| Limit Rotation | `'LIMIT_ROTATION'` | Clamps rotation to range    | `use_limit_x/y/z`, `min_x/y/z`, `max_x/y/z`, `owner_space`, `euler_order`                                                                              |
| Limit Scale    | `'LIMIT_SCALE'`    | Clamps scale to range       | `use_min_x/y/z`, `use_max_x/y/z`, `min_x/y/z`, `max_x/y/z`, `owner_space`                                                                              |

### Transform Manipulation

| Constraint      | Type String         | Description                           | Key Properties                                                                                                                            |
| --------------- | ------------------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Maintain Volume | `'MAINTAIN_VOLUME'` | Preserves volume during scaling       | `mode` ('STRICT', 'UNIFORM', 'SINGLE_AXIS'), `free_axis` ('X', 'Y', 'Z'), `volume`                                                        |
| Transformation  | `'TRANSFORM'`       | Maps one transform channel to another | `target`, `map_from` ('LOCATION', 'ROTATION', 'SCALE'), `map_to`, `from_min/max_x/y/z`, `to_min/max_x/y/z`, `target_space`, `owner_space` |
| Transform Cache | `'TRANSFORM_CACHE'` | Reads transform from Alembic cache    | `cache_file`, `object_path`                                                                                                               |

---

## 3. TRACKING CONSTRAINTS

| Constraint   | Type String      | Description                                             | Key Properties                                                                                                                    |
| ------------ | ---------------- | ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Clamp To     | `'CLAMP_TO'`     | Constrains object to a curve path                       | `target` (curve object), `main_axis` ('CLAMPTO_X', 'CLAMPTO_Y', 'CLAMPTO_Z'), `use_cyclic`                                        |
| Damped Track | `'DAMPED_TRACK'` | Points an axis at target with minimal rotation          | `target`, `subtarget`, `track_axis` ('TRACK_X', 'TRACK_NEGATIVE_X', 'TRACK_Y', 'TRACK_NEGATIVE_Y', 'TRACK_Z', 'TRACK_NEGATIVE_Z') |
| Locked Track | `'LOCKED_TRACK'` | Points axis at target while keeping another axis locked | `target`, `subtarget`, `track_axis`, `lock_axis` ('LOCK_X', 'LOCK_Y', 'LOCK_Z')                                                   |
| Stretch To   | `'STRETCH_TO'`   | Points at and stretches toward target                   | `target`, `subtarget`, `rest_length`, `bulge`, `volume` ('VOLUME_XZX', 'VOLUME_X', 'VOLUME_Z', 'VOLUME_NONE'), `keep_axis`        |
| Track To     | `'TRACK_TO'`     | Points axis at target with up axis constraint           | `target`, `subtarget`, `track_axis`, `up_axis` ('UP_X', 'UP_Y', 'UP_Z'), `use_target_z`                                           |

---

## 4. RELATIONSHIP CONSTRAINTS

| Constraint  | Type String     | Description                                               | Key Properties                                                                                                                                 |
| ----------- | --------------- | --------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Action      | `'ACTION'`      | Drives an action based on a transform channel             | `target`, `action`, `transform_channel`, `min/max`, `frame_start/end`, `mix_mode`                                                              |
| Armature    | `'ARMATURE'`    | Multi-bone parent (like multiple Copy Transforms blended) | `targets` (collection of targets with weight)                                                                                                  |
| Child Of    | `'CHILD_OF'`    | Dynamic parent-child relationship                         | `target`, `subtarget`, `use_location_x/y/z`, `use_rotation_x/y/z`, `use_scale_x/y/z`, `set_inverse()`                                          |
| Floor       | `'FLOOR'`       | Prevents object from going below target                   | `target`, `subtarget`, `use_rotation`, `offset`, `floor_location` ('FLOOR_X', 'FLOOR_Y', 'FLOOR_Z', 'FLOOR_NEGATIVE_X', etc.)                  |
| Follow Path | `'FOLLOW_PATH'` | Moves object along a curve                                | `target` (curve), `use_fixed_location`, `offset_factor`, `forward_axis`, `up_axis`, `use_curve_follow`, `use_curve_radius`                     |
| Pivot       | `'PIVOT'`       | Changes pivot point for rotations                         | `target`, `subtarget`, `rotation_range` ('ALWAYS_ACTIVE', 'NX', 'NY', 'NZ', 'X', 'Y', 'Z'), `offset`                                           |
| Shrinkwrap  | `'SHRINKWRAP'`  | Projects onto target surface                              | `target`, `shrinkwrap_type` ('NEAREST_SURFACE', 'PROJECT', 'NEAREST_VERTEX', 'TARGET_PROJECT'), `distance`, `project_axis`, `use_track_normal` |

---

## 5. INVERSE KINEMATICS CONSTRAINTS

| Constraint         | Type String            | Description                | Key Properties                                                                                                                                                                                                   |
| ------------------ | ---------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Inverse Kinematics | `'INVERSE_KINEMATICS'` | IK solver for bone chains  | `target`, `subtarget`, `pole_target`, `pole_subtarget`, `pole_angle`, `chain_count`, `use_rotation`, `ik_type` ('COPY_POSE', 'DISTANCE'), `iterations`, `use_location`, `use_stretch`, `weight`, `orient_weight` |
| Spline IK          | `'SPLINE_IK'`          | Fits bone chain to a curve | `target` (curve), `chain_count`, `use_chain_offset`, `use_curve_radius`, `use_even_divisions`, `y_scale_mode`, `xz_scale_mode`                                                                                   |

---

## 6. SPACES

Both `target_space` and `owner_space` accept:

| Value                  | Description                                    |
| ---------------------- | ---------------------------------------------- |
| `'WORLD'`              | World coordinate space                         |
| `'CUSTOM'`             | Custom space (relative to another object/bone) |
| `'POSE'`               | Pose space (relative to armature object)       |
| `'LOCAL_WITH_PARENT'`  | Local space including parent transform         |
| `'LOCAL'`              | Local space (relative to parent bone/object)   |
| `'LOCAL_OWNER_ORIENT'` | Local space with owner orientation             |

---

## Common Constraint Patterns

### FK/IK Switch

```python
# Add both FK and IK constraints, drive influence with a custom property
arm_obj["ik_fk_switch"] = 0.0  # 0 = FK, 1 = IK

# On IK constraint
ik_con = pose_bone.constraints["IK"]
fcurve = ik_con.driver_add("influence")
driver = fcurve.driver
var = driver.variables.new()
var.name = "switch"
var.type = 'SINGLE_PROP'
var.targets[0].id = arm_obj
var.targets[0].data_path = '["ik_fk_switch"]'
driver.expression = "switch"

# On FK Copy Rotation constraints (inverse)
fk_con = pose_bone.constraints["FK"]
fcurve = fk_con.driver_add("influence")
driver = fcurve.driver
var = driver.variables.new()
var.name = "switch"
var.type = 'SINGLE_PROP'
var.targets[0].id = arm_obj
var.targets[0].data_path = '["ik_fk_switch"]'
driver.expression = "1 - switch"
```

### Follow Path Animation

```python
# Create curve path
bpy.ops.curve.primitive_nurbs_path_add()
path = bpy.context.active_object

# Add Follow Path constraint
obj = bpy.data.objects["MyObject"]
con = obj.constraints.new('FOLLOW_PATH')
con.target = path
con.use_fixed_location = True
con.use_curve_follow = True
con.forward_axis = 'FORWARD_Y'
con.up_axis = 'UP_Z'

# Animate offset
con.offset_factor = 0.0
con.keyframe_insert(data_path="offset_factor", frame=1)
con.offset_factor = 1.0
con.keyframe_insert(data_path="offset_factor", frame=120)
```

### Bone Chain with Stretch To

```python
# Make each bone stretch toward the next bone's target
for i, bone_name in enumerate(chain_bones[:-1]):
    pb = arm_obj.pose.bones[bone_name]
    con = pb.constraints.new('STRETCH_TO')
    con.target = arm_obj
    con.subtarget = chain_bones[i + 1]
    con.rest_length = 0.0
    con.volume = 'VOLUME_XZX'
    con.keep_axis = 'SWING_Y'
```
