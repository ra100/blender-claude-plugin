# Blender Geometry Nodes - Python API Reference

## Creating a Geometry Node Group

```python
import bpy

# Create new geometry node group
node_group = bpy.data.node_groups.new(name="MyNodeGroup", type='GeometryNodeTree')

# Access nodes and links
nodes = node_group.nodes
links = node_group.links

# Clear defaults
nodes.clear()
```

## Adding Nodes

All geometry nodes are added using `nodes.new(type=<type_string>)`.

### Node Type Naming Convention

Node type strings follow the pattern `GeometryNode<PascalCaseName>`:

```python
# Mesh primitives
cube = nodes.new(type='GeometryNodeMeshCube')
cylinder = nodes.new(type='GeometryNodeMeshCylinder')
ico_sphere = nodes.new(type='GeometryNodeMeshIcoSphere')
uv_sphere = nodes.new(type='GeometryNodeMeshUVSphere')
cone = nodes.new(type='GeometryNodeMeshCone')
grid = nodes.new(type='GeometryNodeMeshGrid')
circle = nodes.new(type='GeometryNodeMeshCircle')
line = nodes.new(type='GeometryNodeMeshLine')

# Curve primitives
arc = nodes.new(type='GeometryNodeCurvePrimitiveArc')
bezier = nodes.new(type='GeometryNodeCurvePrimitiveBezierSegment')
curve_circle = nodes.new(type='GeometryNodeCurvePrimitiveCircle')
curve_line = nodes.new(type='GeometryNodeCurvePrimitiveLine')
spiral = nodes.new(type='GeometryNodeCurvePrimitiveSpiral')
star = nodes.new(type='GeometryNodeCurvePrimitiveStar')
quadrilateral = nodes.new(type='GeometryNodeCurvePrimitiveQuadrilateral')

# Operations
join = nodes.new(type='GeometryNodeJoinGeometry')
transform = nodes.new(type='GeometryNodeTransform')
set_pos = nodes.new(type='GeometryNodeSetPosition')
set_mat = nodes.new(type='GeometryNodeSetMaterial')
delete = nodes.new(type='GeometryNodeDeleteGeometry')
separate = nodes.new(type='GeometryNodeSeparateGeometry')
merge = nodes.new(type='GeometryNodeMergeByDistance')
boolean_op = nodes.new(type='GeometryNodeMeshBoolean')
extrude = nodes.new(type='GeometryNodeExtrudeMesh')
subdivide = nodes.new(type='GeometryNodeSubdivisionSurface')

# Instances
instance_on = nodes.new(type='GeometryNodeInstanceOnPoints')
realize = nodes.new(type='GeometryNodeRealizeInstances')
rotate_inst = nodes.new(type='GeometryNodeRotateInstances')
scale_inst = nodes.new(type='GeometryNodeScaleInstances')

# Point distribution
distribute = nodes.new(type='GeometryNodeDistributePointsOnFaces')
distribute_vol = nodes.new(type='GeometryNodeDistributePointsInVolume')

# Attributes
store_attr = nodes.new(type='GeometryNodeStoreNamedAttribute')
named_attr = nodes.new(type='GeometryNodeInputNamedAttribute')
capture = nodes.new(type='GeometryNodeCaptureAttribute')
remove_attr = nodes.new(type='GeometryNodeRemoveAttribute')

# Conversions
mesh_to_curve = nodes.new(type='GeometryNodeMeshToCurve')
curve_to_mesh = nodes.new(type='GeometryNodeCurveToMesh')
curve_to_points = nodes.new(type='GeometryNodeCurveToPoints')
mesh_to_points = nodes.new(type='GeometryNodeMeshToPoints')

# Input
position = nodes.new(type='GeometryNodeInputPosition')
normal = nodes.new(type='GeometryNodeInputNormal')
index = nodes.new(type='GeometryNodeInputIndex')
obj_info = nodes.new(type='GeometryNodeObjectInfo')
collection_info = nodes.new(type='GeometryNodeCollectionInfo')

# Sampling
raycast = nodes.new(type='GeometryNodeRaycast')
proximity = nodes.new(type='GeometryNodeProximity')
sample_index = nodes.new(type='GeometryNodeSampleIndex')
sample_nearest = nodes.new(type='GeometryNodeSampleNearest')

# Viewer
viewer = nodes.new(type='GeometryNodeViewer')
```

### Shared Shader/Function Nodes

Some utility nodes use `ShaderNode` or `FunctionNode` prefixes:

```python
# Math (shared with shader nodes)
math = nodes.new(type='ShaderNodeMath')
math.operation = 'ADD'  # ADD, SUBTRACT, MULTIPLY, DIVIDE, POWER, etc.

vector_math = nodes.new(type='ShaderNodeVectorMath')
vector_math.operation = 'ADD'

mix = nodes.new(type='ShaderNodeMix')
combine_xyz = nodes.new(type='ShaderNodeCombineXYZ')
separate_xyz = nodes.new(type='ShaderNodeSeparateXYZ')
map_range = nodes.new(type='ShaderNodeMapRange')
clamp = nodes.new(type='ShaderNodeClamp')
color_ramp = nodes.new(type='ShaderNodeValToRGB')
rgb_curves = nodes.new(type='ShaderNodeRGBCurve')

# Textures
noise = nodes.new(type='ShaderNodeTexNoise')
voronoi = nodes.new(type='ShaderNodeTexVoronoi')
wave = nodes.new(type='ShaderNodeTexWave')
musgrave = nodes.new(type='ShaderNodeTexMusgrave')  # Deprecated in 4.x+
checker = nodes.new(type='ShaderNodeTexChecker')
gradient = nodes.new(type='ShaderNodeTexGradient')
magic = nodes.new(type='ShaderNodeTexMagic')
brick = nodes.new(type='ShaderNodeTexBrick')
white_noise = nodes.new(type='ShaderNodeTexWhiteNoise')

# Function nodes
boolean_math = nodes.new(type='FunctionNodeBooleanMath')
compare = nodes.new(type='FunctionNodeCompare')
random = nodes.new(type='FunctionNodeRandomValue')
switch_node = nodes.new(type='GeometryNodeSwitch')
rotate_euler = nodes.new(type='FunctionNodeRotateEuler')
align_rotation = nodes.new(type='FunctionNodeAlignEulerToVector')
```

## Linking Nodes

```python
# Basic linking: output socket -> input socket
links.new(cube.outputs['Mesh'], transform.inputs['Geometry'])
links.new(transform.outputs['Geometry'], join.inputs['Geometry'])

# By index
links.new(cube.outputs[0], transform.inputs[0])
```

## Setting Input Values

```python
# By socket name
cube.inputs['Size'].default_value = (2.0, 2.0, 2.0)
cube.inputs['Vertices X'].default_value = 10

# For single-value sockets
math.inputs[0].default_value = 5.0
math.inputs[1].default_value = 3.0

# Boolean
switch_node.inputs['Switch'].default_value = True

# Integer
distribute.inputs['Density'].default_value = 10.0
```

## Node Positioning

```python
cube.location = (-400, 0)
transform.location = (-200, 0)
join.location = (0, 0)
```

## Group Input/Output Interface

```python
# Add sockets to group interface (Blender 4.x+ API)
interface = node_group.interface

# Add input sockets
interface.new_socket(name="My Geometry", in_out='INPUT', socket_type='NodeSocketGeometry')
interface.new_socket(name="Scale", in_out='INPUT', socket_type='NodeSocketFloat')
interface.new_socket(name="Count", in_out='INPUT', socket_type='NodeSocketInt')

# Add output sockets
interface.new_socket(name="Result", in_out='OUTPUT', socket_type='NodeSocketGeometry')

# Socket types:
# NodeSocketFloat, NodeSocketVector, NodeSocketColor, NodeSocketBool,
# NodeSocketInt, NodeSocketString, NodeSocketGeometry, NodeSocketObject,
# NodeSocketCollection, NodeSocketMaterial, NodeSocketImage,
# NodeSocketRotation, NodeSocketMatrix, NodeSocketMenu
```

## Applying Geometry Nodes Modifier

```python
obj = bpy.context.active_object
modifier = obj.modifiers.new(name="GeometryNodes", type='NODES')
modifier.node_group = node_group

# Set modifier input values
# Input identifier format: Socket_N (where N is the socket index)
modifier["Socket_2"] = 5.0  # Set the value of the second input socket
```

## Common Patterns

### Frame Nodes for Organization

```python
frame = nodes.new(type='NodeFrame')
frame.label = "Scatter Setup"
frame.use_custom_color = True
frame.color = (0.2, 0.3, 0.4)

# Parent nodes to frame
distribute.parent = frame
instance_on.parent = frame
```

### Reroute for Clean Wiring

```python
reroute = nodes.new(type='NodeReroute')
reroute.location = (100, -200)
links.new(some_output, reroute.inputs[0])
links.new(reroute.outputs[0], some_input)
```
