# Blender 5.0 Modifiers - Complete Reference

Approximately 50 modifiers across 4 categories: Generate, Deform, Physics, and Grease Pencil.

## Adding Modifiers via Python

```python
import bpy

obj = bpy.context.active_object
mod = obj.modifiers.new(name="MyModifier", type='SUBSURF')

# Common operations
bpy.ops.object.modifier_apply(modifier=mod.name)     # Apply modifier
bpy.ops.object.modifier_remove(modifier=mod.name)    # Remove modifier
obj.modifiers.remove(mod)                             # Remove (no ops)
bpy.ops.object.modifier_move_up(modifier=mod.name)   # Move up in stack
bpy.ops.object.modifier_move_down(modifier=mod.name) # Move down in stack

# Visibility
mod.show_viewport = True       # Show in viewport
mod.show_render = True         # Show in render
mod.show_in_editmode = False   # Show in edit mode
mod.show_on_cage = False       # Show on cage (edit mode deform)
```

---

## 1. GENERATE MODIFIERS

| Modifier               | Type String        | Description                                   | Key Properties                                                                                                                                                                                                                                                             |
| ---------------------- | ------------------ | --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Array                  | `'ARRAY'`          | Duplicates object in a pattern                | `count`, `relative_offset_displace`, `constant_offset_displace`, `use_relative_offset`, `use_constant_offset`, `use_object_offset`, `offset_object`, `use_merge_vertices`, `merge_threshold`, `fit_type` ('FIXED_COUNT', 'FIT_LENGTH', 'FIT_CURVE'), `fit_length`, `curve` |
| Bevel                  | `'BEVEL'`          | Bevels edges or vertices                      | `width`, `segments`, `profile`, `limit_method` ('NONE', 'ANGLE', 'WEIGHT', 'VGROUP'), `angle_limit`, `offset_type` ('OFFSET', 'WIDTH', 'DEPTH', 'PERCENT', 'ABSOLUTE'), `miter_outer`, `miter_inner`, `affect` ('VERTICES', 'EDGES'), `harden_normals`                     |
| Boolean                | `'BOOLEAN'`        | Boolean operations between meshes             | `operation` ('INTERSECT', 'UNION', 'DIFFERENCE'), `object`, `solver` ('FAST', 'EXACT'), `use_self`, `use_hole_tolerant`, `operand_type` ('OBJECT', 'COLLECTION'), `collection`                                                                                             |
| Build                  | `'BUILD'`          | Builds mesh over time                         | `frame_start`, `frame_duration`, `use_reverse`, `use_random_order`, `seed`                                                                                                                                                                                                 |
| Decimate               | `'DECIMATE'`       | Reduces polygon count                         | `decimate_type` ('COLLAPSE', 'UNSUBDIV', 'DISSOLVE'), `ratio`, `iterations` (UNSUBDIV), `angle_limit` (DISSOLVE), `use_symmetry`, `symmetry_axis`                                                                                                                          |
| Edge Split             | `'EDGE_SPLIT'`     | Splits edges for sharp shading                | `split_angle`, `use_edge_angle`, `use_edge_sharp`                                                                                                                                                                                                                          |
| Mask                   | `'MASK'`           | Hides geometry based on vertex group/armature | `mode` ('VERTEX_GROUP', 'ARMATURE'), `vertex_group`, `armature`, `invert_vertex_group`, `threshold`                                                                                                                                                                        |
| Mesh to Volume         | `'MESH_TO_VOLUME'` | Converts mesh to volume                       | `resolution_mode`, `voxel_size`, `voxel_amount`, `interior_band_width`, `density`                                                                                                                                                                                          |
| Mirror                 | `'MIRROR'`         | Mirrors mesh across axes                      | `use_axis` (tuple of 3 bools), `use_bisect_axis`, `use_bisect_flip_axis`, `use_clip`, `merge_threshold`, `mirror_object`, `use_mirror_merge`, `use_mirror_vertex_groups`, `use_mirror_u`, `use_mirror_v`                                                                   |
| Multiresolution        | `'MULTIRES'`       | Subdivides for sculpting detail               | `levels`, `sculpt_levels`, `render_levels`, `quality`, `uv_smooth`, `show_only_control_edges`                                                                                                                                                                              |
| Nodes (Geometry Nodes) | `'NODES'`          | Applies geometry node group                   | `node_group` (reference to NodeTree)                                                                                                                                                                                                                                       |
| Remesh                 | `'REMESH'`         | Regenerates mesh topology                     | `mode` ('VOXEL', 'BLOCKS', 'SMOOTH', 'SHARP'), `voxel_size`, `octree_depth`, `scale`, `use_smooth_shade`, `use_remove_disconnected`, `threshold`                                                                                                                           |
| Screw                  | `'SCREW'`          | Revolves profile around axis                  | `axis` ('X', 'Y', 'Z'), `object`, `angle`, `steps`, `render_steps`, `screw_offset`, `iterations`, `use_merge_vertices`, `use_smooth_shade`, `use_normal_calculate`                                                                                                         |
| Skin                   | `'SKIN'`           | Generates skin mesh around vertices           | (controlled via vertex radii in edit mode) `branch_smoothing`, `use_smooth_shade`                                                                                                                                                                                          |
| Solidify               | `'SOLIDIFY'`       | Adds thickness to surface                     | `thickness`, `offset`, `use_rim`, `use_rim_only`, `use_even_offset`, `use_quality_normals`, `solidify_mode` ('EXTRUDE', 'NON_MANIFOLD'), `nonmanifold_thickness_mode`, `nonmanifold_boundary_mode`                                                                         |
| Subdivision Surface    | `'SUBSURF'`        | Catmull-Clark or Simple subdivision           | `levels` (viewport), `render_levels`, `subdivision_type` ('CATMULL_CLARK', 'SIMPLE'), `uv_smooth`, `quality`, `use_limit_surface`, `show_only_control_edges`, `use_creases`                                                                                                |
| Triangulate            | `'TRIANGULATE'`    | Converts faces to triangles                   | `quad_method` ('BEAUTY', 'FIXED', 'FIXED_ALTERNATE', 'SHORTEST_DIAGONAL', 'LONGEST_DIAGONAL'), `ngon_method` ('BEAUTY', 'CLIP'), `min_vertices`, `keep_custom_normals`                                                                                                     |
| Volume to Mesh         | `'VOLUME_TO_MESH'` | Converts volume to mesh                       | `resolution_mode`, `voxel_size`, `voxel_amount`, `threshold`, `adaptivity`, `grid_name`, `object`                                                                                                                                                                          |
| Weld                   | `'WELD'`           | Merges vertices by distance                   | `merge_threshold`, `mode` ('ALL', 'CONNECTED'), `vertex_group`, `invert_vertex_group`                                                                                                                                                                                      |
| Wireframe              | `'WIREFRAME'`      | Creates wireframe from mesh edges             | `thickness`, `offset`, `use_boundary`, `use_replace`, `use_even_offset`, `use_relative_offset`, `use_crease`, `crease_weight`, `material_offset`, `vertex_group`                                                                                                           |

---

## 2. DEFORM MODIFIERS

| Modifier          | Type String           | Description                                       | Key Properties                                                                                                                                                                                                                                                                                                        |
| ----------------- | --------------------- | ------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Armature          | `'ARMATURE'`          | Deforms mesh using armature bones                 | `object` (armature), `use_bone_envelopes`, `use_vertex_groups`, `use_deform_preserve_volume`, `vertex_group`                                                                                                                                                                                                          |
| Cast              | `'CAST'`              | Casts mesh toward a geometric shape               | `cast_type` ('SPHERE', 'CYLINDER', 'CUBOID'), `factor`, `radius`, `size`, `use_radius_as_size`, `use_x/y/z`, `object`, `vertex_group`                                                                                                                                                                                 |
| Corrective Smooth | `'CORRECTIVE_SMOOTH'` | Smooths deformation artifacts                     | `factor`, `iterations`, `smooth_type` ('ORCO', 'BIND'), `scale`, `use_only_smooth`, `use_pin_boundary`, `vertex_group`                                                                                                                                                                                                |
| Curve             | `'CURVE'`             | Deforms mesh along a curve                        | `object` (curve), `deform_axis` ('POS_X', 'POS_Y', 'POS_Z', 'NEG_X', 'NEG_Y', 'NEG_Z'), `vertex_group`                                                                                                                                                                                                                |
| Displace          | `'DISPLACE'`          | Displaces vertices using texture                  | `texture`, `texture_coords` ('LOCAL', 'GLOBAL', 'OBJECT', 'UV'), `texture_coords_object`, `uv_layer`, `direction` ('X', 'Y', 'Z', 'NORMAL', 'CUSTOM_NORMAL', 'RGB_TO_XYZ'), `space`, `strength`, `mid_level`, `vertex_group`                                                                                          |
| Hook              | `'HOOK'`              | Deforms vertices toward an object                 | `object`, `subtarget`, `vertex_group`, `strength`, `falloff_type`, `falloff_radius`, `center`, `use_falloff_uniform`                                                                                                                                                                                                  |
| Laplacian Deform  | `'LAPLACIANDEFORM'`   | Preserves geometric details during deformation    | `iterations`, `vertex_group` (anchor group)                                                                                                                                                                                                                                                                           |
| Lattice           | `'LATTICE'`           | Deforms mesh using lattice object                 | `object` (lattice), `vertex_group`, `strength`                                                                                                                                                                                                                                                                        |
| Mesh Deform       | `'MESH_DEFORM'`       | Deforms mesh using another mesh as cage           | `object` (cage mesh), `vertex_group`, `precision`, `use_dynamic_bind`                                                                                                                                                                                                                                                 |
| Shrinkwrap        | `'SHRINKWRAP'`        | Projects mesh onto target surface                 | `target`, `wrap_method` ('NEAREST_SURFACEPOINT', 'PROJECT', 'NEAREST_VERTEX', 'TARGET_PROJECT'), `wrap_mode` ('ON_SURFACE', 'INSIDE', 'OUTSIDE', 'OUTSIDE_SURFACE', 'ABOVE_SURFACE'), `offset`, `vertex_group`, `project_limit`, `use_project_x/y/z`, `use_negative_direction`, `use_positive_direction`, `cull_face` |
| Simple Deform     | `'SIMPLE_DEFORM'`     | Twist, Bend, Taper, or Stretch                    | `deform_method` ('TWIST', 'BEND', 'TAPER', 'STRETCH'), `deform_axis` ('X', 'Y', 'Z'), `factor`, `angle`, `limits` (tuple: min, max), `lock_x/y/z`, `origin` (object)                                                                                                                                                  |
| Smooth            | `'SMOOTH'`            | Smooths mesh by averaging vertex positions        | `factor`, `iterations`, `use_x/y/z`, `vertex_group`                                                                                                                                                                                                                                                                   |
| Corrective Smooth | `'CORRECTIVE_SMOOTH'` | Smooths deformation while preserving volume       | `factor`, `iterations`, `smooth_type`, `use_only_smooth`, `use_pin_boundary`                                                                                                                                                                                                                                          |
| Laplacian Smooth  | `'LAPLACIANSMOOTH'`   | Laplacian smoothing (preserves volume better)     | `lambda_factor`, `lambda_border`, `iterations`, `use_x/y/z`, `use_volume_preserve`, `use_normalized`, `vertex_group`                                                                                                                                                                                                  |
| Surface Deform    | `'SURFACE_DEFORM'`    | Deforms mesh to follow target surface deformation | `target`, `vertex_group`, `falloff`, `strength`                                                                                                                                                                                                                                                                       |
| Volume Displace   | `'VOLUME_DISPLACE'`   | Displaces volume data                             | `texture`, `strength`, `texture_mid_level`, `texture_sample_radius`                                                                                                                                                                                                                                                   |
| Warp              | `'WARP'`              | Warps mesh between two objects                    | `object_from`, `object_to`, `strength`, `falloff_type`, `falloff_radius`, `falloff_curve`, `vertex_group`, `texture`                                                                                                                                                                                                  |
| Wave              | `'WAVE'`              | Animated wave deformation                         | `use_x`, `use_y`, `use_cyclic`, `use_normal`, `use_normal_x/y/z`, `time_offset`, `lifetime`, `damping_time`, `falloff_radius`, `start_position_x/y`, `start_position_object`, `speed`, `height`, `width`, `narrowness`, `vertex_group`, `texture`                                                                     |

---

## 3. PHYSICS MODIFIERS

These are added automatically by physics systems. See the blender-physics-simulation skill for detailed physics setup.

| Modifier          | Type String           | Description                               |
| ----------------- | --------------------- | ----------------------------------------- |
| Cloth             | `'CLOTH'`             | Cloth simulation (auto-added by physics)  |
| Collision         | `'COLLISION'`         | Collision detection for other simulations |
| Dynamic Paint     | `'DYNAMIC_PAINT'`     | Paint effects from physics interactions   |
| Explode           | `'EXPLODE'`           | Breaks mesh along particle paths          |
| Fluid             | `'FLUID'`             | Fluid simulation (Mantaflow)              |
| Ocean             | `'OCEAN'`             | Procedural ocean surface                  |
| Particle Instance | `'PARTICLE_INSTANCE'` | Instances mesh on particles               |
| Particle System   | `'PARTICLE_SYSTEM'`   | Particle system                           |
| Soft Body         | `'SOFT_BODY'`         | Soft body simulation                      |

---

## 4. NORMALS / SHADING MODIFIERS

| Modifier        | Type String         | Description                                 | Key Properties                                                                                                     |
| --------------- | ------------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Normal Edit     | `'NORMAL_EDIT'`     | Overrides custom normals                    | `mode` ('RADIAL', 'DIRECTIONAL'), `target`, `offset`, `mix_mode`, `mix_factor`, `mix_limit`                        |
| Weighted Normal | `'WEIGHTED_NORMAL'` | Calculates normals based on face area/angle | `mode` ('FACE_AREA', 'CORNER_ANGLE', 'FACE_AREA_AND_ANGLE'), `weight`, `keep_sharp`, `thresh`, `face_influence`    |
| Data Transfer   | `'DATA_TRANSFER'`   | Transfers data from another mesh            | `object`, `use_vert_data`, `use_edge_data`, `use_loop_data`, `use_poly_data`, `data_types_verts/edges/loops/polys` |
| UV Project      | `'UV_PROJECT'`      | Projects UVs from an object                 | `projectors` (collection), `projector_count`, `uv_layer`                                                           |
| UV Warp         | `'UV_WARP'`         | Warps UVs using two objects                 | `object_from`, `object_to`, `uv_layer`, `center`, `vertex_group`                                                   |

---

## Auto Smooth (Blender 4.1+ / 5.x)

Auto Smooth is no longer a mesh property — it's handled via the Smooth by Angle node in geometry nodes, or via a modifier:

```python
# In Blender 5.x, smooth shading is per-face
# Set all faces to smooth
bpy.ops.object.shade_smooth()

# Sharp edges are controlled by edge marks or angle-based auto smooth
# The "Smooth by Angle" geometry node modifier is auto-added
# Or set via mesh custom normals
```
