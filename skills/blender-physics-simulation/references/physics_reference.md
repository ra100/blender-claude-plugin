# Blender 5.0 Physics Types - Complete Reference

## Physics Types Overview

| System                | Access                                     | Description                                           |
| --------------------- | ------------------------------------------ | ----------------------------------------------------- |
| Rigid Body            | `obj.rigid_body`                           | Solid object dynamics (falling, colliding, breaking)  |
| Rigid Body Constraint | `obj.rigid_body_constraint`                | Joints between rigid bodies (hinges, springs, motors) |
| Cloth                 | `obj.modifiers["Cloth"]`                   | Fabric simulation (draping, tearing)                  |
| Soft Body             | `obj.modifiers["Softbody"]`                | Deformable solid objects (jelly, rubber)              |
| Fluid (Domain)        | `obj.modifiers["Fluid"].domain_settings`   | Mantaflow gas/liquid simulation container             |
| Fluid (Flow)          | `obj.modifiers["Fluid"].flow_settings`     | Fluid emitter/source                                  |
| Fluid (Effector)      | `obj.modifiers["Fluid"].effector_settings` | Fluid obstacle/force                                  |
| Collision             | `obj.modifiers["Collision"]`               | Passive collision surface for cloth/particles         |
| Particle System       | `obj.particle_systems[]`                   | Particle emission (effects, hair, instances)          |
| Force Field           | `obj.field`                                | External forces (wind, turbulence, gravity)           |
| Dynamic Paint         | `obj.modifiers["Dynamic Paint"]`           | Surface interaction painting                          |
| Ocean                 | `obj.modifiers["Ocean"]`                   | Procedural ocean surface                              |

---

## Rigid Body Properties

### Active (Dynamic) Object

| Property           | Type  | Default         | Description                                                       |
| ------------------ | ----- | --------------- | ----------------------------------------------------------------- |
| `type`             | enum  | `'ACTIVE'`      | ACTIVE or PASSIVE                                                 |
| `enabled`          | bool  | True            | Enable simulation                                                 |
| `mass`             | float | 1.0             | Object mass (kg)                                                  |
| `collision_shape`  | enum  | `'CONVEX_HULL'` | BOX, SPHERE, CAPSULE, CYLINDER, CONE, CONVEX_HULL, MESH, COMPOUND |
| `friction`         | float | 0.5             | Surface friction (0-1)                                            |
| `restitution`      | float | 0.0             | Bounciness (0-1)                                                  |
| `linear_damping`   | float | 0.04            | Linear velocity damping                                           |
| `angular_damping`  | float | 0.1             | Angular velocity damping                                          |
| `use_margin`       | bool  | True            | Use collision margin                                              |
| `collision_margin` | float | 0.04            | Collision detection margin                                        |
| `kinematic`        | bool  | False           | Keyframe-driven (passive animated)                                |
| `use_deactivation` | bool  | False           | Sleep when still                                                  |

### Passive (Static) Object

Same properties as Active but `type='PASSIVE'`. Typically uses `collision_shape='MESH'` for terrain/walls.

### Rigid Body Constraint Types

| Type           | Type String        | Description                           |
| -------------- | ------------------ | ------------------------------------- |
| Fixed          | `'FIXED'`          | Rigid connection (no movement)        |
| Point          | `'POINT'`          | Ball-and-socket joint (rotation only) |
| Hinge          | `'HINGE'`          | Single-axis rotation (door)           |
| Slider         | `'SLIDER'`         | Single-axis translation (drawer)      |
| Piston         | `'PISTON'`         | Translation + rotation on one axis    |
| Generic        | `'GENERIC'`        | Configurable limits on all 6 DOF      |
| Generic Spring | `'GENERIC_SPRING'` | Generic with spring forces            |
| Motor          | `'MOTOR'`          | Powered rotation/translation          |

---

## Cloth Properties

### Simulation Settings (`modifier.settings`)

| Property                 | Type   | Default | Description                   |
| ------------------------ | ------ | ------- | ----------------------------- |
| `quality`                | int    | 5       | Steps per frame               |
| `time_scale`             | float  | 1.0     | Simulation speed              |
| `mass`                   | float  | 0.3     | Fabric mass per vertex        |
| `tension_stiffness`      | float  | 15.0    | Stretch resistance            |
| `compression_stiffness`  | float  | 15.0    | Compression resistance        |
| `shear_stiffness`        | float  | 5.0     | Shear resistance              |
| `bending_stiffness`      | float  | 0.5     | Bend resistance               |
| `tension_damping`        | float  | 5.0     | Tension damping               |
| `compression_damping`    | float  | 5.0     | Compression damping           |
| `shear_damping`          | float  | 5.0     | Shear damping                 |
| `bending_damping`        | float  | 0.5     | Bending damping               |
| `air_damping`            | float  | 1.0     | Air drag                      |
| `vertex_group_mass`      | string | ""      | Pin group (0 weight = pinned) |
| `use_pressure`           | bool   | False   | Enable pressure simulation    |
| `uniform_pressure_force` | float  | 0.0     | Inflation pressure            |

### Collision Settings (`modifier.collision_settings`)

| Property             | Type  | Default | Description                 |
| -------------------- | ----- | ------- | --------------------------- |
| `use_collision`      | bool  | True    | Enable external collisions  |
| `distance_min`       | float | 0.015   | Min distance from colliders |
| `use_self_collision` | bool  | False   | Enable self-collision       |
| `self_distance_min`  | float | 0.015   | Min self-collision distance |
| `self_friction`      | float | 5.0     | Self-collision friction     |
| `collision_quality`  | int   | 2       | Collision steps             |

---

## Soft Body Properties (`modifier.settings`)

| Property             | Type   | Default | Description                |
| -------------------- | ------ | ------- | -------------------------- |
| `mass`               | float  | 1.0     | Point mass                 |
| `friction`           | float  | 0.5     | Surface friction           |
| `speed`              | float  | 1.0     | Simulation speed           |
| `use_goal`           | bool   | True    | Use goal (spring to rest)  |
| `goal_default`       | float  | 0.7     | Default goal strength      |
| `goal_spring`        | float  | 0.5     | Goal spring stiffness      |
| `goal_friction`      | float  | 0.5     | Goal spring damping        |
| `vertex_group_goal`  | string | ""      | Goal vertex group          |
| `use_edges`          | bool   | True    | Use edge springs           |
| `pull`               | float  | 0.5     | Pull spring stiffness      |
| `push`               | float  | 0.5     | Push spring stiffness      |
| `damping`            | float  | 5.0     | Edge spring damping        |
| `bending`            | float  | 0.1     | Bending stiffness          |
| `plastic`            | float  | 0       | Permanent deformation      |
| `use_self_collision` | bool   | False   | Self-collision             |
| `ball_size`          | float  | 0.49    | Self-collision ball radius |

---

## Fluid Properties (Mantaflow)

### Domain Settings (`modifier.domain_settings`)

| Property                                                | Type  | Default | Description            |
| ------------------------------------------------------- | ----- | ------- | ---------------------- |
| `domain_type`                                           | enum  | `'GAS'` | GAS or LIQUID          |
| `resolution_max`                                        | int   | 32      | Voxel resolution       |
| `use_adaptive_domain`                                   | bool  | False   | Adaptive domain bounds |
| `time_scale`                                            | float | 1.0     | Simulation speed       |
| `cfl_condition`                                         | float | 4.0     | CFL safety number      |
| `use_collision_border_front/back/left/right/top/bottom` | bool  | True    | Border collision       |

#### Gas-Specific

| Property             | Type  | Default | Description                 |
| -------------------- | ----- | ------- | --------------------------- |
| `alpha`              | float | 1.0     | Buoyancy density            |
| `beta`               | float | 1.0     | Buoyancy heat               |
| `vorticity`          | float | 0.0     | Vorticity enhancement       |
| `use_dissolve_smoke` | bool  | False   | Dissolve smoke over time    |
| `dissolve_speed`     | int   | 5       | Dissolve rate (frames)      |
| `use_noise`          | bool  | False   | Enable noise detail         |
| `noise_scale`        | int   | 2       | Noise resolution multiplier |
| `noise_strength`     | float | 1.0     | Noise intensity             |
| `burning_rate`       | float | 0.75    | Fire burning speed          |
| `flame_smoke`        | float | 1.0     | Smoke from fire             |
| `flame_vorticity`    | float | 0.5     | Fire vorticity              |

#### Liquid-Specific

| Property               | Type  | Default | Description                 |
| ---------------------- | ----- | ------- | --------------------------- |
| `use_mesh`             | bool  | False   | Generate mesh surface       |
| `mesh_concave_upper`   | float | 3.5     | Mesh upper concavity        |
| `mesh_concave_lower`   | float | 0.5     | Mesh lower concavity        |
| `mesh_particle_radius` | float | 1.0     | Particle radius for meshing |
| `use_spray_particles`  | bool  | False   | Generate spray              |
| `use_foam_particles`   | bool  | False   | Generate foam               |
| `use_bubble_particles` | bool  | False   | Generate bubbles            |
| `viscosity_base`       | float | 1.0     | Viscosity base              |
| `viscosity_exponent`   | int   | 6       | Viscosity exponent          |

### Flow Settings (`modifier.flow_settings`)

| Property               | Type     | Default         | Description                        |
| ---------------------- | -------- | --------------- | ---------------------------------- |
| `flow_type`            | enum     | `'SMOKE'`       | SMOKE, FIRE, BOTH, LIQUID          |
| `flow_behavior`        | enum     | `'INFLOW'`      | INFLOW, OUTFLOW, GEOMETRY          |
| `density`              | float    | 1.0             | Smoke density                      |
| `temperature`          | float    | 1.0             | Smoke temperature                  |
| `smoke_color`          | float[3] | (0.7, 0.7, 0.7) | Smoke color                        |
| `fuel_amount`          | float    | 0.5             | Fire fuel                          |
| `surface_distance`     | float    | 1.5             | Emission surface distance          |
| `volume_density`       | float    | 0.0             | Volume emission (0 = surface only) |
| `use_initial_velocity` | bool     | False           | Use source velocity                |
| `velocity_factor`      | float    | 1.0             | Velocity multiplier                |

### Effector Settings (`modifier.effector_settings`)

| Property           | Type  | Default       | Description                          |
| ------------------ | ----- | ------------- | ------------------------------------ |
| `effector_type`    | enum  | `'COLLISION'` | COLLISION or GUIDE                   |
| `surface_distance` | float | 0.0           | Surface distance                     |
| `use_effector`     | bool  | True          | Enable effector                      |
| `use_plane_init`   | bool  | False         | Use as initialization plane          |
| `guide_mode`       | enum  | `'MAXIMUM'`   | MAXIMUM, MINIMUM, OVERRIDE, AVERAGED |

---

## Collision Properties (`modifier.settings`)

| Property            | Type  | Default | Description                 |
| ------------------- | ----- | ------- | --------------------------- |
| `thickness_outer`   | float | 0.02    | Outer collision thickness   |
| `thickness_inner`   | float | 0.2     | Inner collision thickness   |
| `cloth_friction`    | float | 5.0     | Friction for cloth          |
| `damping`           | float | 0.0     | Collision damping           |
| `use_particle_kill` | bool  | False   | Kill particles on collision |
| `stickiness`        | float | 0.0     | Particle stickiness         |

---

## Particle System Properties

### Emitter Settings (`ps.settings`)

| Property              | Type       | Default     | Description                                |
| --------------------- | ---------- | ----------- | ------------------------------------------ |
| `type`                | enum       | `'EMITTER'` | EMITTER or HAIR                            |
| `count`               | int        | 1000        | Total particles                            |
| `frame_start`         | float      | 1           | Start emission frame                       |
| `frame_end`           | float      | 200         | End emission frame                         |
| `lifetime`            | float      | 50          | Particle lifetime (frames)                 |
| `lifetime_random`     | float      | 0.0         | Lifetime randomness                        |
| `emit_from`           | enum       | `'FACE'`    | VERT, FACE, VOLUME                         |
| `normal_factor`       | float      | 1.0         | Normal velocity                            |
| `tangent_factor`      | float      | 0.0         | Tangent velocity                           |
| `factor_random`       | float      | 0.0         | Random velocity                            |
| `physics_type`        | enum       | `'NEWTON'`  | NO, NEWTON, KEYED, BOIDS, FLUID            |
| `mass`                | float      | 1.0         | Particle mass                              |
| `particle_size`       | float      | 0.05        | Particle size                              |
| `size_random`         | float      | 0.0         | Size randomness                            |
| `render_type`         | enum       | `'HALO'`    | NONE, HALO, LINE, PATH, OBJECT, COLLECTION |
| `instance_object`     | Object     | None        | Object to instance (OBJECT render)         |
| `instance_collection` | Collection | None        | Collection to instance                     |

### Hair Settings (additional)

| Property               | Type  | Default  | Description                  |
| ---------------------- | ----- | -------- | ---------------------------- |
| `hair_length`          | float | 4.0      | Hair strand length           |
| `hair_step`            | int   | 5        | Hair segments                |
| `child_type`           | enum  | `'NONE'` | NONE, SIMPLE, INTERPOLATED   |
| `child_nbr`            | int   | 0        | Viewport children per parent |
| `rendered_child_count` | int   | 0        | Render children per parent   |
| `clump_factor`         | float | 0.0      | Clumping strength            |
| `roughness_1`          | float | 0.0      | Uniform roughness            |
| `roughness_2`          | float | 0.0      | Size roughness               |

---

## Force Field Types

| Type          | Type String    | Description                               | Key Properties                        |
| ------------- | -------------- | ----------------------------------------- | ------------------------------------- |
| Force         | `'FORCE'`      | Attract/repel from center                 | `strength` (negative = attract)       |
| Wind          | `'WIND'`       | Constant directional force                | `strength`, `noise`, `flow`           |
| Vortex        | `'VORTEX'`     | Spinning force                            | `strength`, `inflow`                  |
| Magnetic      | `'MAGNETIC'`   | Magnetic field                            | `strength`                            |
| Harmonic      | `'HARMONIC'`   | Spring force toward object                | `strength`, `rest_length`, `damping`  |
| Charge        | `'CHARGE'`     | Electrostatic (particle charge dependent) | `strength`                            |
| Lennard-Jones | `'LENNARDJ'`   | Molecular attraction/repulsion            | `strength`                            |
| Texture       | `'TEXTURE'`    | Force from texture                        | `texture`, `strength`, `texture_mode` |
| Guide         | `'GUIDE'`      | Guides particles along curve              | `strength`                            |
| Boid          | `'BOID'`       | Boid AI forces                            | (complex boid settings)               |
| Turbulence    | `'TURBULENCE'` | Random turbulent force                    | `strength`, `size`, `flow`            |
| Drag          | `'DRAG'`       | Velocity-dependent drag                   | `linear_drag`, `quadratic_drag`       |
| Smoke Flow    | `'SMOKE'`      | Force from smoke simulation               | `strength`, `flow`                    |

### Common Force Field Properties

| Property           | Type  | Description                              |
| ------------------ | ----- | ---------------------------------------- |
| `strength`         | float | Force strength                           |
| `flow`             | float | Outward flow                             |
| `noise`            | float | Random noise                             |
| `seed`             | int   | Random seed                              |
| `use_max_distance` | bool  | Limit maximum distance                   |
| `distance_max`     | float | Maximum effect distance                  |
| `use_min_distance` | bool  | Limit minimum distance                   |
| `distance_min`     | float | Minimum effect distance                  |
| `falloff_type`     | enum  | SPHERE, TUBE, CONE                       |
| `falloff_power`    | float | Distance falloff exponent                |
| `shape`            | enum  | POINT, LINE, PLANE, SURFACE, EVERY_POINT |
| `use_absorption`   | bool  | Absorb force behind collision            |

---

## Dynamic Paint

### Canvas (Receiver)

```python
bpy.ops.object.modifier_add(type='DYNAMIC_PAINT')
dp = obj.modifiers["Dynamic Paint"]
bpy.ops.dpaint.type_toggle(type='CANVAS')

canvas = dp.canvas_settings
surface = canvas.canvas_surfaces.active

surface.surface_type = 'PAINT'  # PAINT, DISPLACE, WAVE, WEIGHT
surface.frame_start = 1
surface.frame_end = 250
surface.use_dissolve = True
surface.dissolve_speed = 30
```

### Brush (Painter)

```python
bpy.ops.object.modifier_add(type='DYNAMIC_PAINT')
dp = obj.modifiers["Dynamic Paint"]
bpy.ops.dpaint.type_toggle(type='BRUSH')

brush = dp.brush_settings
brush.paint_source = 'VOLUME_DISTANCE'  # PARTICLE_SYSTEM, POINT, DISTANCE, VOLUME_DISTANCE, VOLUME
brush.paint_color = (1.0, 0.0, 0.0)
brush.paint_alpha = 1.0
brush.paint_wetness = 1.0
```

---

## Ocean Modifier

```python
mod = obj.modifiers.new("Ocean", 'OCEAN')
mod.geometry_mode = 'GENERATE'   # GENERATE or DISPLACE
mod.resolution = 7               # 2^resolution grid
mod.size = 1.0                   # Spatial size
mod.spatial_size = 50            # Real-world size (meters)
mod.depth = 200                  # Water depth
mod.wave_scale = 1.0
mod.wave_scale_min = 0.0
mod.choppiness = 1.0
mod.wind_velocity = 12.0
mod.wave_alignment = 0.0
mod.wave_direction = 0.0
mod.damping = 0.5
mod.foam_layer_name = "foam"
mod.time = 1.0                   # Animate this for wave motion
mod.use_foam = True
mod.foam_coverage = 0.0
```
