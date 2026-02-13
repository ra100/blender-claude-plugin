---
name: blender-modeling-modifiers
description: This skill should be used when working with Blender modifiers, bmesh API, mesh editing operators, sculpting setup, and modeling workflows. It applies when adding/configuring modifiers (Subdivision Surface, Boolean, Array, Mirror, Bevel, etc.), using bmesh for procedural mesh creation/editing, running mesh operators, setting up sculpt mode, or scripting modeling pipelines via Python (bpy) in Blender 5.x. Triggers on "modifier", "subdivision", "boolean", "array", "mirror", "bevel", "bmesh", "mesh editing", "sculpt", "retopology", "hard surface", "decimate", "remesh", or any Blender modeling workflow. If a Blender MCP server is available, prefer using that for direct Blender interaction.
---

# Blender Modeling & Modifiers Expert

## Overview

This skill provides expert guidance for Blender 5.x modeling and modifiers: configuring the modifier stack, using bmesh for procedural mesh creation/editing, running mesh operators, setting up sculpting workflows, and common modeling patterns (hard surface, retopology, booleans). The reference files contain the full modifier catalog and Python API patterns.

## MCP-First Approach

When a Blender MCP server is available in the environment, prefer using it for direct interaction with Blender (adding modifiers, editing meshes, applying operations). Fall back to generating Python scripts when MCP is unavailable.

To check for Blender MCP availability, search available tools for "blender" at the start of a session.

## Task Decision Tree

- **"Add a modifier for X"** -> Consult `references/modifier_reference.md` for the correct modifier type and properties
- **"Create mesh geometry procedurally"** -> See bmesh Workflow below and `references/python_api.md`
- **"Boolean operation between objects"** -> See Boolean Workflow
- **"Set up hard surface modeling"** -> See Hard Surface Pattern
- **"Retopologize a mesh"** -> See Retopology Workflow
- **"Clean up mesh"** -> See Mesh Cleanup
- **"Set up sculpting"** -> See Sculpt Setup
- **"Apply all modifiers"** -> See Modifier API in `references/python_api.md`

## Modifier Stack Principles

1. **Order matters**: Modifiers are applied top-to-bottom. Mirror before Subdivision is different from Subdivision before Mirror.
2. **Viewport vs Render**: Many modifiers have separate viewport and render settings (e.g., `levels_viewport` vs `render_levels` on Subdivision Surface).
3. **Apply with caution**: Applying modifiers is destructive. Keep them live when possible.
4. **Non-destructive workflow**: Use modifier stack for iterative modeling. Apply only when needed for export or further editing.

### Common Modifier Stacking Order

1. **Mirror** (if symmetric)
2. **Array** (if repeating)
3. **Boolean** (cut/join operations)
4. **Solidify** (add thickness)
5. **Bevel** (edge bevels)
6. **Weighted Normal** (fix shading)
7. **Subdivision Surface** (smooth final result)

## Boolean Workflow

### Non-Destructive Booleans (Modifier)

```python
import bpy

obj = bpy.context.active_object
cutter = bpy.data.objects["Cutter"]

mod = obj.modifiers.new(name="Boolean", type='BOOLEAN')
mod.operation = 'DIFFERENCE'  # DIFFERENCE, UNION, INTERSECT
mod.object = cutter
mod.solver = 'FAST'  # FAST or EXACT
# EXACT is slower but handles more edge cases

# Hide cutter in viewport
cutter.hide_viewport = True
cutter.display_type = 'WIRE'
```

### Exact Solver Options

```python
mod.solver = 'EXACT'
mod.use_self = False          # Self-intersection handling
mod.use_hole_tolerant = True  # Better handling of non-manifold meshes
```

## Hard Surface Pattern

1. Start with base shape (Cube, Cylinder, etc.)
2. Add **Mirror** modifier for symmetry
3. Model with limited geometry — define major forms
4. Add supporting edge loops near edges to control subdivision
5. Add **Bevel** modifier (Limit Method: Weight, use Ctrl+Shift+E to set edge weights)
6. Add **Weighted Normal** modifier to fix shading artifacts
7. Add **Subdivision Surface** modifier (Viewport: 2, Render: 3)

```python
# Hard surface modifier stack
obj = bpy.context.active_object

# Mirror
mirror = obj.modifiers.new("Mirror", 'MIRROR')
mirror.use_axis = (True, False, False)
mirror.use_clip = True

# Bevel
bevel = obj.modifiers.new("Bevel", 'BEVEL')
bevel.limit_method = 'WEIGHT'
bevel.width = 0.02
bevel.segments = 3
bevel.profile = 0.5

# Weighted Normal
wnormal = obj.modifiers.new("WeightedNormal", 'WEIGHTED_NORMAL')
wnormal.mode = 'FACE_AREA'
wnormal.keep_sharp = True

# Subdivision Surface
subsurf = obj.modifiers.new("Subdivision", 'SUBSURF')
subsurf.levels = 2
subsurf.render_levels = 3
```

## Retopology Workflow

1. Import high-poly scan/sculpt
2. Add **Shrinkwrap** modifier to a new low-poly mesh
3. Set Shrinkwrap to `NEAREST_SURFACE` or `PROJECT`
4. Model low-poly mesh in edit mode — it snaps to high-poly surface
5. Alternatively: Use **Remesh** modifier for automated retopology

```python
# Remesh (automated)
mod = obj.modifiers.new("Remesh", 'REMESH')
mod.mode = 'VOXEL'        # VOXEL, BLOCKS, SMOOTH, SHARP
mod.voxel_size = 0.05     # Smaller = more detail
mod.use_smooth_shade = True

# Shrinkwrap (manual retopology helper)
mod = lowpoly.modifiers.new("Shrinkwrap", 'SHRINKWRAP')
mod.target = highpoly
mod.wrap_method = 'NEAREST_SURFACEPOINT'
mod.wrap_mode = 'ON_SURFACE'
```

## Mesh Cleanup

### Common Cleanup Operations

```python
import bpy

obj = bpy.context.active_object
bpy.ops.object.mode_set(mode='EDIT')
bpy.ops.mesh.select_all(action='SELECT')

# Remove doubles (merge by distance)
bpy.ops.mesh.remove_doubles(threshold=0.0001)

# Recalculate normals
bpy.ops.mesh.normals_make_consistent(inside=False)

# Delete loose vertices/edges
bpy.ops.mesh.select_all(action='DESELECT')
bpy.ops.mesh.select_loose()
bpy.ops.mesh.delete(type='VERT')

# Fill holes
bpy.ops.mesh.select_non_manifold()
bpy.ops.mesh.fill()

# Triangulate (for export)
bpy.ops.mesh.select_all(action='SELECT')
bpy.ops.mesh.quads_convert_to_tris()

bpy.ops.object.mode_set(mode='OBJECT')
```

### Decimate for Optimization

```python
mod = obj.modifiers.new("Decimate", 'DECIMATE')
mod.decimate_type = 'COLLAPSE'  # COLLAPSE, UNSUBDIV, DISSOLVE
mod.ratio = 0.5                 # 50% of original faces
# mod.use_symmetry = True
# mod.symmetry_axis = 'X'
```

## Sculpt Setup

```python
import bpy

obj = bpy.context.active_object

# Add multires for sculpting (preserves base mesh)
multires = obj.modifiers.new("Multires", 'MULTIRES')
for _ in range(4):
    bpy.ops.object.multires_subdivide(modifier="Multires", mode='CATMULL_CLARK')

# Or use Dyntopo (dynamic topology) — requires sculpt mode
bpy.ops.object.mode_set(mode='SCULPT')
bpy.context.sculpt_object.use_dynamic_topology_sculpting = True

# Sculpt settings
sculpt = bpy.context.tool_settings.sculpt
sculpt.detail_size = 12         # Dyntopo detail (lower = more detail)
sculpt.detail_refine_method = 'RELATIVE'  # RELATIVE, CONSTANT, BRUSH
sculpt.detail_type_method = 'RELATIVE'    # RELATIVE, CONSTANT, MANUAL
sculpt.use_smooth_shading = True
sculpt.symmetrize_direction = 'NEGATIVE_X'

# Brush settings
brush = sculpt.brush
brush.strength = 0.5
brush.size = 50
brush.auto_smooth_factor = 0.0
```

## bmesh Workflow

bmesh provides direct, efficient mesh creation and manipulation without `bpy.ops`:

### Create Mesh from Scratch

```python
import bpy
import bmesh

# Create empty mesh
mesh = bpy.data.meshes.new("ProcMesh")
bm = bmesh.new()

# Create geometry
v1 = bm.verts.new((0, 0, 0))
v2 = bm.verts.new((1, 0, 0))
v3 = bm.verts.new((1, 1, 0))
v4 = bm.verts.new((0, 1, 0))
bm.faces.new((v1, v2, v3, v4))

# Write to mesh and create object
bm.to_mesh(mesh)
bm.free()

obj = bpy.data.objects.new("ProcObj", mesh)
bpy.context.collection.objects.link(obj)
```

### Edit Existing Mesh

```python
import bmesh

obj = bpy.context.active_object
bm = bmesh.new()
bm.from_mesh(obj.data)

# Operations
bmesh.ops.subdivide_edges(bm, edges=bm.edges[:], cuts=1)
bmesh.ops.translate(bm, vec=(0, 0, 1), verts=bm.verts[:])

# Write back
bm.to_mesh(obj.data)
bm.free()
obj.data.update()
```

For complete bmesh patterns and all modifier API details, see `references/python_api.md`.

## Debugging & Optimization

### Common Issues

- **Normals appear inverted**: Recalculate normals (`Mesh > Normals > Recalculate Outside`)
- **Shading artifacts on curved surfaces**: Add Weighted Normal modifier or use Auto Smooth
- **Boolean fails**: Ensure meshes are manifold (watertight). Try the Exact solver. Check for coplanar faces.
- **Modifier not applying**: Some modifiers require the object to be in Object mode. Check for dependencies.
- **Subdivision too dense**: Reduce viewport levels. Use adaptive subdivision for Cycles render.
- **Sculpt performance**: Lower multires preview level. Disable symmetry if not needed. Use Dyntopo constant detail.

### Performance Tips

1. Keep modifier stack short — each modifier is computed per depsgraph evaluation
2. Use Exact boolean solver only when needed — FAST is significantly quicker
3. For animation, apply static modifiers before the animated ones
4. Use instancing (Collection Instance) instead of duplicating high-poly objects
5. Decimate before export if triangle count is excessive

## Modifier Reference

For the complete catalog of all ~50 modifiers with type strings, properties, and use cases, consult `references/modifier_reference.md`.

Key categories:
- Generate: grep for "Array" or "Boolean" or "Bevel" or "Mirror" or "Solidify" or "Subdivision"
- Deform: grep for "Lattice" or "Shrinkwrap" or "Simple Deform" or "Curve"
- Physics: grep for "Cloth" or "Fluid" or "Particle" (see also blender-physics-simulation skill)

## Python API Reference

For complete Python API patterns including modifier management, bmesh operations, mesh operators, and sculpt configuration, consult `references/python_api.md`.
