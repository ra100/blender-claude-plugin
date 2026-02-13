# Blender Physics & Simulation - Python API Reference

## Rigid Body Setup

### Active Rigid Body

```python
import bpy

obj = bpy.context.active_object

# Add rigid body via operator (requires object selection)
bpy.ops.object.select_all(action='DESELECT')
obj.select_set(True)
bpy.context.view_layer.objects.active = obj
bpy.ops.rigidbody.object_add(type='ACTIVE')

# Configure
rb = obj.rigid_body
rb.mass = 2.0
rb.friction = 0.5
rb.restitution = 0.3
rb.collision_shape = 'CONVEX_HULL'
rb.linear_damping = 0.04
rb.angular_damping = 0.1
rb.use_margin = True
rb.collision_margin = 0.04
```

### Passive Rigid Body (Floor/Wall)

```python
bpy.ops.rigidbody.object_add(type='PASSIVE')
rb = obj.rigid_body
rb.collision_shape = 'MESH'
rb.friction = 0.8
rb.restitution = 0.0
```

### Batch Rigid Body Setup

```python
import bpy

# Add rigid body to multiple objects
for obj in bpy.data.collections["FallingObjects"].objects:
    bpy.ops.object.select_all(action='DESELECT')
    obj.select_set(True)
    bpy.context.view_layer.objects.active = obj
    bpy.ops.rigidbody.object_add(type='ACTIVE')

    rb = obj.rigid_body
    rb.mass = 1.0
    rb.collision_shape = 'CONVEX_HULL'
    rb.friction = 0.5
    rb.restitution = 0.3
```

### Rigid Body World

```python
scene = bpy.context.scene

# Ensure world exists
if scene.rigidbody_world is None:
    bpy.ops.rigidbody.world_add()

rbw = scene.rigidbody_world
rbw.time_scale = 1.0
rbw.substeps_per_frame = 10
rbw.solver_iterations = 10
rbw.use_split_impulse = True

# Cache settings
cache = rbw.point_cache
cache.frame_start = 1
cache.frame_end = 250
```

### Rigid Body Constraints

```python
# Create constraint object (empty)
bpy.ops.object.empty_add(type='PLAIN_AXES', location=(0, 0, 2))
constraint_obj = bpy.context.active_object

bpy.ops.rigidbody.constraint_add(type='HINGE')
rbc = constraint_obj.rigid_body_constraint
rbc.object1 = bpy.data.objects["Arm"]
rbc.object2 = bpy.data.objects["Body"]

# Hinge limits
rbc.use_limit_ang_z = True
rbc.limit_ang_z_lower = -1.5708  # -90 degrees
rbc.limit_ang_z_upper = 0.0

# Spring constraint
bpy.ops.rigidbody.constraint_add(type='GENERIC_SPRING')
rbc = constraint_obj.rigid_body_constraint
rbc.use_spring_x = True
rbc.spring_stiffness_x = 10.0
rbc.spring_damping_x = 0.5

# Breakable constraint
rbc.use_breaking = True
rbc.breaking_threshold = 50.0
```

### Kinematic Animation (Passive to Active Switch)

```python
obj = bpy.data.objects["Ball"]
rb = obj.rigid_body

# Frame 1: kinematic (follows keyframes)
rb.kinematic = True
rb.keyframe_insert(data_path="kinematic", frame=1)

# Frame 50: switch to dynamic
rb.kinematic = False
rb.keyframe_insert(data_path="kinematic", frame=50)
```

## Cloth Simulation

### Full Cloth Setup

```python
import bpy

cloth_obj = bpy.context.active_object

# Add cloth modifier
bpy.ops.object.modifier_add(type='CLOTH')
cloth_mod = cloth_obj.modifiers["Cloth"]
settings = cloth_mod.settings
collision = cloth_mod.collision_settings

# Physical properties
settings.quality = 5
settings.mass = 0.3
settings.tension_stiffness = 15.0
settings.compression_stiffness = 15.0
settings.shear_stiffness = 5.0
settings.bending_stiffness = 0.5
settings.tension_damping = 5.0
settings.compression_damping = 5.0
settings.shear_damping = 5.0
settings.bending_damping = 0.5
settings.air_damping = 1.0

# Collision
collision.use_collision = True
collision.distance_min = 0.015
collision.use_self_collision = True
collision.self_distance_min = 0.015
collision.self_friction = 5.0

# Pinning vertex group
settings.vertex_group_mass = "Pin"

# Cache
cache = cloth_mod.point_cache
cache.frame_start = 1
cache.frame_end = 250
```

### Collision Object for Cloth

```python
collider = bpy.data.objects["Mannequin"]
bpy.ops.object.select_all(action='DESELECT')
collider.select_set(True)
bpy.context.view_layer.objects.active = collider

bpy.ops.object.modifier_add(type='COLLISION')
col = collider.modifiers["Collision"].settings
col.thickness_outer = 0.02
col.thickness_inner = 0.2
col.cloth_friction = 5.0
```

### Pressure (Inflatable)

```python
settings.use_pressure = True
settings.uniform_pressure_force = 5.0
settings.use_pressure_volume = True
settings.target_volume = 1.0
settings.pressure_factor = 1.0
```

## Fluid Simulation

### Smoke Setup (Complete)

```python
import bpy

# --- Domain ---
bpy.ops.mesh.primitive_cube_add(size=4, location=(0, 0, 2))
domain = bpy.context.active_object
domain.name = "SmokeDomain"

bpy.ops.object.modifier_add(type='FLUID')
domain.modifiers["Fluid"].fluid_type = 'DOMAIN'
ds = domain.modifiers["Fluid"].domain_settings

ds.domain_type = 'GAS'
ds.resolution_max = 64
ds.use_adaptive_domain = True
ds.time_scale = 1.0

# Gas properties
ds.alpha = 1.0          # Buoyancy density influence
ds.beta = 1.0           # Buoyancy temperature influence
ds.vorticity = 0.1

# Dissolve
ds.use_dissolve_smoke = True
ds.dissolve_speed = 30

# Noise
ds.use_noise = True
ds.noise_scale = 2
ds.noise_strength = 1.0

# Cache
ds.cache_frame_start = 1
ds.cache_frame_end = 150
ds.cache_type = 'MODULAR'

# --- Emitter ---
bpy.ops.mesh.primitive_uv_sphere_add(radius=0.3, location=(0, 0, 0.5))
emitter = bpy.context.active_object
emitter.name = "SmokeEmitter"

bpy.ops.object.modifier_add(type='FLUID')
emitter.modifiers["Fluid"].fluid_type = 'FLOW'
flow = emitter.modifiers["Fluid"].flow_settings

flow.flow_type = 'SMOKE'
flow.flow_behavior = 'INFLOW'
flow.density = 1.0
flow.temperature = 1.0
flow.smoke_color = (0.8, 0.8, 0.8)
flow.surface_distance = 1.5
flow.volume_density = 0.0
```

### Fire Setup

```python
flow.flow_type = 'BOTH'  # Smoke + Fire
flow.fuel_amount = 1.0
flow.temperature = 2.0

# Domain fire settings
ds.burning_rate = 0.75
ds.flame_smoke = 1.0
ds.flame_vorticity = 0.5
ds.flame_max_temp = 3.0
ds.flame_smoke_color = (0.7, 0.7, 0.7)
```

### Liquid Setup (Complete)

```python
import bpy

# --- Domain ---
bpy.ops.mesh.primitive_cube_add(size=4, location=(0, 0, 2))
domain = bpy.context.active_object
domain.name = "LiquidDomain"

bpy.ops.object.modifier_add(type='FLUID')
domain.modifiers["Fluid"].fluid_type = 'DOMAIN'
ds = domain.modifiers["Fluid"].domain_settings

ds.domain_type = 'LIQUID'
ds.resolution_max = 64
ds.use_adaptive_domain = False  # Less reliable for liquid

# Mesh generation
ds.use_mesh = True
ds.mesh_concave_upper = 3.5
ds.mesh_concave_lower = 0.5
ds.mesh_particle_radius = 1.0

# Secondary particles
ds.use_spray_particles = True
ds.use_foam_particles = True
ds.use_bubble_particles = True

# Viscosity (water: base=1, exp=6)
ds.viscosity_base = 1.0
ds.viscosity_exponent = 6

# Cache
ds.cache_frame_start = 1
ds.cache_frame_end = 150
ds.cache_type = 'MODULAR'

# --- Emitter ---
bpy.ops.mesh.primitive_uv_sphere_add(radius=0.5, location=(0, 0, 3))
emitter = bpy.context.active_object
emitter.name = "LiquidEmitter"

bpy.ops.object.modifier_add(type='FLUID')
emitter.modifiers["Fluid"].fluid_type = 'FLOW'
flow = emitter.modifiers["Fluid"].flow_settings

flow.flow_type = 'LIQUID'
flow.flow_behavior = 'GEOMETRY'  # Initial geometry as liquid volume
flow.use_initial_velocity = True
flow.velocity_factor = 1.0
```

### Fluid Effector (Obstacle)

```python
obstacle = bpy.data.objects["Wall"]
bpy.ops.object.select_all(action='DESELECT')
obstacle.select_set(True)
bpy.context.view_layer.objects.active = obstacle

bpy.ops.object.modifier_add(type='FLUID')
obstacle.modifiers["Fluid"].fluid_type = 'EFFECTOR'
eff = obstacle.modifiers["Fluid"].effector_settings
eff.effector_type = 'COLLISION'
eff.surface_distance = 0.0
eff.use_effector = True
```

## Soft Body

```python
import bpy

obj = bpy.context.active_object

bpy.ops.object.modifier_add(type='SOFT_BODY')
sb = obj.modifiers["Softbody"].settings

# Object
sb.mass = 1.0
sb.friction = 0.5
sb.speed = 1.0

# Goal (spring to original shape)
sb.use_goal = True
sb.goal_default = 0.7
sb.goal_spring = 0.5
sb.goal_friction = 0.5

# Edges (internal springs)
sb.use_edges = True
sb.pull = 0.5
sb.push = 0.5
sb.damping = 5.0
sb.bending = 0.1
sb.plastic = 0
sb.spring_length = 0

# Self-collision
sb.use_self_collision = True
sb.ball_size = 0.49
sb.ball_stiff = 1.0
sb.ball_damp = 0.5

# Solver
sb.step_min = 1
sb.step_max = 5
sb.use_auto_step = True
sb.error_threshold = 0.1

# Cache
cache = obj.modifiers["Softbody"].point_cache
cache.frame_start = 1
cache.frame_end = 250
```

## Particle Systems

### Emitter System

```python
import bpy

obj = bpy.context.active_object

# Add particle system
bpy.ops.object.particle_system_add()
ps = obj.particle_systems[-1]
ps.name = "Sparks"
settings = ps.settings

# Basic
settings.type = 'EMITTER'
settings.count = 5000
settings.frame_start = 1
settings.frame_end = 50
settings.lifetime = 30
settings.lifetime_random = 0.5

# Emission
settings.emit_from = 'FACE'
settings.use_modifier_stack = True
settings.use_even_distribution = True

# Velocity
settings.normal_factor = 2.0
settings.tangent_factor = 0.0
settings.factor_random = 1.0

# Physics
settings.physics_type = 'NEWTON'
settings.mass = 0.1
settings.particle_size = 0.01
settings.size_random = 0.5

# Gravity and damping
settings.effector_weights.gravity = 1.0
settings.drag_factor = 0.1
settings.damping = 0.0

# Render
settings.render_type = 'HALO'
settings.display_method = 'DOT'
settings.display_size = 0.02
settings.display_percentage = 100
```

### Object Instance Particles

```python
settings.render_type = 'OBJECT'
settings.instance_object = bpy.data.objects["Leaf"]
settings.use_rotation_instance = True
settings.use_scale_instance = True
settings.particle_size = 0.5

# Random rotation
settings.phase_factor = 1.0
settings.phase_factor_random = 1.0
settings.angular_velocity_mode = 'RAND'
settings.angular_velocity_factor = 1.0
```

### Collection Instance Particles

```python
settings.render_type = 'COLLECTION'
settings.instance_collection = bpy.data.collections["Debris"]
settings.use_whole_collection = True
settings.use_collection_pick_random = True
settings.use_rotation_instance = True
settings.use_scale_instance = True
```

### Hair Particles

```python
settings.type = 'HAIR'
settings.count = 500
settings.hair_length = 0.3
settings.hair_step = 5

# Children
settings.child_type = 'INTERPOLATED'
settings.child_nbr = 10           # Viewport
settings.rendered_child_count = 50 # Render
settings.child_length = 1.0
settings.child_radius = 0.2
settings.clump_factor = 0.5
settings.roughness_1 = 0.02
settings.roughness_2 = 0.02
settings.roughness_endpoint = 0.05
```

### Effector Weights

```python
# Per-particle system force field weighting
ew = settings.effector_weights
ew.gravity = 1.0
ew.all = 1.0        # Global multiplier
ew.force = 1.0
ew.wind = 1.0
ew.vortex = 1.0
ew.magnetic = 0.0
ew.harmonic = 1.0
ew.charge = 1.0
ew.lennardjones = 0.0
ew.turbulence = 1.0
ew.drag = 1.0
ew.boid = 0.0
ew.texture = 0.0
ew.smoke_flow = 0.0
```

## Force Fields

### Adding Force Fields

```python
import bpy

# Add as new empty object
bpy.ops.object.effector_add(type='WIND', location=(0, 0, 3))
wind = bpy.context.active_object.field
wind.strength = 15.0
wind.noise = 3.0
wind.flow = 0.5
wind.use_max_distance = True
wind.distance_max = 20.0
wind.falloff_power = 1.0

# Add to existing object
obj = bpy.data.objects["MyObject"]
obj.field.type = 'FORCE'
obj.field.strength = -5.0  # Negative = attract
obj.field.falloff_type = 'SPHERE'
obj.field.falloff_power = 2.0
```

### Turbulence

```python
bpy.ops.object.effector_add(type='TURBULENCE', location=(0, 0, 2))
turb = bpy.context.active_object.field
turb.strength = 5.0
turb.size = 1.5         # Turbulence scale
turb.flow = 0.5
turb.noise = 2.0
turb.seed = 42
```

### Vortex

```python
bpy.ops.object.effector_add(type='VORTEX', location=(0, 0, 0))
vortex = bpy.context.active_object.field
vortex.strength = 10.0
vortex.inflow = -0.5  # Inward pull
vortex.shape = 'POINT'
```

### Animated Force Field

```python
# Animate strength
field = bpy.context.active_object.field
field.strength = 0.0
field.keyframe_insert(data_path="strength", frame=1)
field.strength = 20.0
field.keyframe_insert(data_path="strength", frame=50)
field.strength = 0.0
field.keyframe_insert(data_path="strength", frame=100)
```

## Baking & Caching

### Bake All Physics

```python
# Bake all physics caches in scene
bpy.ops.ptcache.bake_all(bake=True)

# Free all caches
bpy.ops.ptcache.free_bake_all()
```

### Bake Specific Cache

```python
# Select object with physics, then:
bpy.ops.ptcache.bake(bake=True)   # Bake active point cache
bpy.ops.ptcache.free_bake()        # Free active point cache
```

### Fluid Baking

```python
domain = bpy.data.objects["FluidDomain"]
bpy.context.view_layer.objects.active = domain
domain.select_set(True)

# Bake data (velocity field)
bpy.ops.fluid.bake_data()

# Bake noise detail (gas only)
bpy.ops.fluid.bake_noise()

# Bake mesh surface (liquid only)
bpy.ops.fluid.bake_mesh()

# Bake secondary particles (liquid only)
bpy.ops.fluid.bake_particles()

# Free individual caches
bpy.ops.fluid.free_data()
bpy.ops.fluid.free_noise()
bpy.ops.fluid.free_mesh()
bpy.ops.fluid.free_particles()

# Bake/free all at once
bpy.ops.fluid.bake_all()
bpy.ops.fluid.free_all()
```

### Cache Settings

```python
# Access point cache (cloth, soft body, particles, rigid body)
cache = modifier.point_cache  # or scene.rigidbody_world.point_cache

cache.frame_start = 1
cache.frame_end = 250
cache.use_disk_cache = False     # Cache to disk (slower but less RAM)
cache.use_library_path = True    # Use library path for cache
cache.compression_type = 'LIGHT' # NO, LIGHT, HEAVY
```

## Complete Scene Setup Example

### Falling Objects onto Cloth

```python
import bpy
import random

# Clear scene
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# Create ground (passive rigid body)
bpy.ops.mesh.primitive_plane_add(size=10, location=(0, 0, 0))
ground = bpy.context.active_object
ground.name = "Ground"
bpy.ops.rigidbody.object_add(type='PASSIVE')
ground.rigid_body.collision_shape = 'MESH'
bpy.ops.object.modifier_add(type='COLLISION')

# Create cloth
bpy.ops.mesh.primitive_grid_add(size=4, x_subdivisions=30, y_subdivisions=30, location=(0, 0, 3))
cloth = bpy.context.active_object
cloth.name = "Cloth"
bpy.ops.object.modifier_add(type='CLOTH')
cloth_settings = cloth.modifiers["Cloth"].settings
cloth_settings.mass = 0.3
cloth_settings.bending_stiffness = 0.5
cloth.modifiers["Cloth"].collision_settings.use_collision = True

# Create falling objects
for i in range(10):
    x = random.uniform(-1.5, 1.5)
    y = random.uniform(-1.5, 1.5)
    z = random.uniform(5, 8)
    bpy.ops.mesh.primitive_uv_sphere_add(radius=0.2, location=(x, y, z))
    ball = bpy.context.active_object
    ball.name = f"Ball_{i}"
    bpy.ops.rigidbody.object_add(type='ACTIVE')
    ball.rigid_body.mass = 0.5
    ball.rigid_body.collision_shape = 'SPHERE'
    ball.rigid_body.restitution = 0.3

# Set up rigid body world
scene = bpy.context.scene
if scene.rigidbody_world is None:
    bpy.ops.rigidbody.world_add()
scene.rigidbody_world.point_cache.frame_end = 250

# Set frame range
scene.frame_start = 1
scene.frame_end = 250
scene.frame_current = 1
```
