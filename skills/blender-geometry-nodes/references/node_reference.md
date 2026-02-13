# Blender 5.0 Geometry Nodes - Complete Reference

Approximately 373 distinct geometry node implementations across 16 top-level categories.

## Socket Data Types

Float, Vector, RGBA, Boolean, Rotation, Matrix, Integer, String, Object, Geometry, Collection, Image, Material, Menu, Bundle, Closure, Font

## Geometry Domains

- **Point**: Vertices (mesh), control points (curves), points (point cloud)
- **Edge**: Mesh edges only
- **Face**: Mesh faces only
- **Face Corner**: Mesh face corners (loops)
- **Spline**: Individual curves within a curves component
- **Instance**: Top-level instances
- **Layer**: Grease Pencil layers

---

## 1. INPUT NODES

### Constant

| Node          | Description                        |
| ------------- | ---------------------------------- |
| Bool          | Constant boolean value             |
| Collection    | References a collection data-block |
| Color         | Constant RGBA color value          |
| Image         | References an image data-block     |
| Integer       | Constant integer value             |
| Material      | References a material data-block   |
| Object        | References an object data-block    |
| Rotation      | Constant rotation value            |
| String        | Constant string value              |
| Value (Float) | Constant float value               |
| Vector        | Constant 3D vector value           |

### Gizmo (Tool mode only)

| Node            | Description                                                 |
| --------------- | ----------------------------------------------------------- |
| Dial Gizmo      | Interactive dial gizmo for rotation input in viewport       |
| Linear Gizmo    | Interactive linear gizmo for distance/translation input     |
| Transform Gizmo | Full transform gizmo (translate, rotate, scale) in viewport |

### Group

| Node        | Description                                                 |
| ----------- | ----------------------------------------------------------- |
| Group Input | Exposes inputs to the node group, visible on modifier panel |

### Import

| Node        | Description                              |
| ----------- | ---------------------------------------- |
| Import CSV  | Imports data from a CSV file             |
| Import OBJ  | Imports geometry from an OBJ file        |
| Import PLY  | Imports geometry from a PLY file         |
| Import STL  | Imports geometry from an STL file        |
| Import Text | Imports text content from a file         |
| Import VDB  | Imports volume data from an OpenVDB file |

### Scene

| Node                | Description                                                          |
| ------------------- | -------------------------------------------------------------------- |
| 3D Cursor           | Outputs the 3D cursor position and rotation                          |
| Active Camera       | Outputs the active scene camera object                               |
| Camera Info         | Outputs camera properties (focal length, sensor size, etc.)          |
| Bone Info           | Outputs bone transform data from an armature                         |
| Collection Children | Lists child collections of a given collection                        |
| Collection Info     | Outputs geometry from all objects in a collection                    |
| Image Info          | Outputs image dimensions and metadata                                |
| Is Viewport         | True if rendering in viewport, false for final render                |
| Mouse Position      | Current mouse position (tool mode only)                              |
| Object Info         | Outputs transform, geometry, and other data from a referenced object |
| Scene Time          | Current frame number and time in seconds                             |
| Self Object         | References the object the modifier is applied to                     |
| Viewport Transform  | Current viewport camera transform                                    |

---

## 2. OUTPUT NODES

| Node          | Description                                                               |
| ------------- | ------------------------------------------------------------------------- |
| Enable Output | Conditionally enables or disables a group output                          |
| Group Output  | Defines the output of the geometry node group                             |
| Viewer        | Displays geometry or field data in the viewport/spreadsheet for debugging |
| Warning       | Displays a warning message in the modifier panel                          |

---

## 3. ATTRIBUTE NODES

| Node                   | Description                                                                               |
| ---------------------- | ----------------------------------------------------------------------------------------- |
| Attribute Statistic    | Computes statistics (min, max, mean, median, sum, std deviation, variance) of a field     |
| Domain Size            | Outputs the number of elements in each domain (point count, edge count, face count, etc.) |
| Blur Attribute         | Smooths/blurs attribute values across neighboring elements                                |
| Capture Attribute      | Captures and stores a field value on geometry for later use                               |
| Remove Named Attribute | Deletes a named attribute; removing built-in attributes resets them to defaults           |
| Store Named Attribute  | Stores field data as a named attribute on geometry                                        |

---

## 4. CURVE NODES

### Read

| Node                  | Description                                                                  |
| --------------------- | ---------------------------------------------------------------------------- |
| Handle Positions      | Outputs positions of Bezier curve handles (left and right)                   |
| Curve Length          | Total length of each spline or all splines combined                          |
| Curve Tangent         | Tangent direction vector at each curve point                                 |
| Curve Tilt            | Tilt angle at each curve control point                                       |
| Endpoint Selection    | Selects the first and/or last N points of each spline                        |
| Handle Type Selection | Selects curve points based on Bezier handle type (free, auto, vector, align) |
| Spline Cyclic         | Whether each spline is cyclic (closed)                                       |
| Spline Length         | Number of points and length of each spline                                   |
| Spline Parameter      | Factor (0-1) and length along each spline for every point                    |
| Spline Resolution     | Resolution of NURBS and Bezier splines                                       |

### Sample

| Node         | Description                                                           |
| ------------ | --------------------------------------------------------------------- |
| Sample Curve | Samples data from curves at specified positions (by factor or length) |

### Write

| Node                  | Description                                                 |
| --------------------- | ----------------------------------------------------------- |
| Set Curve Normal      | Sets the normal evaluation mode (minimum twist, Z-up, free) |
| Set Curve Radius      | Sets radius at each curve control point                     |
| Set Curve Tilt        | Sets tilt rotation at each curve control point              |
| Set Handle Positions  | Sets positions of Bezier curve handles                      |
| Set Handle Type       | Sets Bezier handle type (free, auto, vector, align)         |
| Set Spline Cyclic     | Sets whether splines are cyclic (closed loops)              |
| Set Spline Resolution | Sets resolution for NURBS/Bezier evaluation                 |
| Set Spline Type       | Converts spline types (Bezier, NURBS, Poly, Catmull-Rom)    |

### Operations

| Node                     | Description                                                             |
| ------------------------ | ----------------------------------------------------------------------- |
| Curve to Mesh            | Converts curves to mesh by sweeping a profile curve along them          |
| Curve to Points          | Converts curves to point cloud, sampling at evaluated or control points |
| Curves to Grease Pencil  | Converts curve geometry to Grease Pencil strokes                        |
| Deform Curves on Surface | Deforms curves attached to a surface when the surface deforms           |
| Curve Fill               | Fills planar closed curves with a mesh face                             |
| Curve Fillet             | Rounds or chamfers curve corners                                        |
| Interpolate Curves       | Creates new curves by interpolating between guide curves                |
| Resample Curve           | Resamples curves to a specified number of points or length              |
| Reverse Curve            | Reverses the direction of splines                                       |
| Subdivide Curve          | Adds points between existing control points                             |
| Trim Curve               | Trims/cuts curves at specified start and end positions                  |

### Primitives

| Node             | Description                                                                  |
| ---------------- | ---------------------------------------------------------------------------- |
| Arc              | Creates an arc curve from radius/angle or three points                       |
| Bezier Segment   | Creates a single Bezier curve segment from control points                    |
| Circle           | Creates a circular curve                                                     |
| Line             | Creates a straight line curve                                                |
| Quadratic Bezier | Creates a quadratic Bezier curve from three points                           |
| Quadrilateral    | Creates a four-sided polygon curve (rectangle, diamond, parallelogram, etc.) |
| Spiral           | Creates a spiral curve                                                       |
| Star             | Creates a star-shaped curve with inner and outer radii                       |

### Topology

| Node                  | Description                                                  |
| --------------------- | ------------------------------------------------------------ |
| Curve of Point        | Index of the spline that a given point belongs to            |
| Offset Point in Curve | Offsets a point index within its curve by a specified amount |
| Points of Curve       | Point indices that belong to a given spline                  |

---

## 5. GREASE PENCIL NODES

### Read

| Node                  | Description                                 |
| --------------------- | ------------------------------------------- |
| Named Layer Selection | Selects Grease Pencil strokes by layer name |

### Write

| Node                         | Description                                |
| ---------------------------- | ------------------------------------------ |
| Set Grease Pencil Color      | Sets vertex color on Grease Pencil strokes |
| Set Grease Pencil Depth Mode | Sets depth ordering mode for layers        |
| Set Grease Pencil Softness   | Sets softness/feathering of strokes        |

### Operations

| Node                    | Description                                      |
| ----------------------- | ------------------------------------------------ |
| Grease Pencil to Curves | Converts Grease Pencil strokes to curve geometry |
| Merge Layers            | Merges multiple Grease Pencil layers into one    |

---

## 6. GEOMETRY NODES (Operations)

### Read

| Node                | Description                                                      |
| ------------------- | ---------------------------------------------------------------- |
| ID                  | Reads the built-in `id` attribute (stable identifier for points) |
| Index               | Index of each element in its domain                              |
| Named Attribute     | Reads a named attribute by its string name                       |
| Normal              | Normal vector for each element                                   |
| Position            | Position of each point element                                   |
| Radius              | Radius attribute of points or curves                             |
| Tool Selection      | Current selection in tool mode                                   |
| Tool Active Element | Active element in tool mode                                      |
| Get Geometry Bundle | Retrieves geometry from a bundle data type                       |

### Sample

| Node               | Description                                                               |
| ------------------ | ------------------------------------------------------------------------- |
| Geometry Proximity | Closest point/edge/face on target geometry; outputs distance and position |
| Index of Nearest   | Index of the nearest element in another geometry                          |
| Raycast            | Casts rays from points; returns hit position, normal, distance, attribute |
| Sample Index       | Samples field values at specific element indices                          |
| Sample Nearest     | Finds nearest element and samples its attribute values                    |

### Write

| Node                | Description                                  |
| ------------------- | -------------------------------------------- |
| Set Geometry Bundle | Stores geometry into a bundle                |
| Set Geometry Name   | Assigns a name to the geometry data          |
| Set ID              | Sets the `id` attribute on geometry elements |
| Set Position        | Sets position of point-domain elements       |
| Tool Set Selection  | Sets selection state in tool mode            |

### Operations

| Node                 | Description                                                                        |
| -------------------- | ---------------------------------------------------------------------------------- |
| Bake                 | Bakes/caches geometry data for performance or export                               |
| Bounding Box         | Computes the axis-aligned bounding box                                             |
| Convex Hull          | Generates the convex hull mesh                                                     |
| Delete Geometry      | Removes elements based on a selection field                                        |
| Duplicate Elements   | Duplicates selected elements a specified number of times                           |
| Join Geometry        | Merges multiple geometry inputs into one                                           |
| Merge by Distance    | Merges points within a specified distance threshold                                |
| Sort Elements        | Sorts elements by a specified field value                                          |
| Transform Geometry   | Applies translation, rotation, and scale                                           |
| Separate Components  | Splits geometry into component types (mesh, curve, point cloud, volume, instances) |
| Separate Geometry    | Splits geometry into two outputs based on selection                                |
| Split to Instances   | Splits geometry into individual instances based on a group ID                      |
| Geometry to Instance | Converts geometry into an instance                                                 |

### Material

| Node               | Description                                      |
| ------------------ | ------------------------------------------------ |
| Replace Material   | Replaces one material with another               |
| Material Index     | Reads the material index of each face            |
| Material Selection | Selects elements assigned to a specific material |
| Set Material       | Assigns a material to geometry                   |
| Set Material Index | Sets the material index for faces                |

---

## 7. MESH NODES

### Read

| Node                  | Description                                               |
| --------------------- | --------------------------------------------------------- |
| Edge Angle            | Angle between faces adjacent to each edge                 |
| Edge Neighbors        | Number of faces connected to each edge                    |
| Edge Vertices         | Vertex positions and indices of each edge                 |
| Edges to Face Groups  | Groups faces based on boundary edges                      |
| Face Area             | Area of each face                                         |
| Face Group Boundaries | Identifies edges that are boundaries between face groups  |
| Face Neighbors        | Number of edges/vertices and adjacent faces for each face |
| Face is Planar        | Tests whether each face is planar within a threshold      |
| Shade Smooth          | Reads the smooth shading attribute                        |
| Edge Smooth           | Reads the edge smooth/sharp attribute                     |
| Mesh Island           | Island index and count for disconnected mesh parts        |
| Shortest Edge Paths   | Computes shortest paths between vertices along edges      |
| Vertex Neighbors      | Number of edges and faces connected to each vertex        |

### Sample

| Node                   | Description                                                      |
| ---------------------- | ---------------------------------------------------------------- |
| Sample Nearest Surface | Samples the closest point on a mesh surface and reads attributes |
| Sample UV Surface      | Samples a mesh surface at specified UV coordinates               |

### Write

| Node             | Description                                         |
| ---------------- | --------------------------------------------------- |
| Set Face Set     | Assigns face set IDs (tool mode)                    |
| Set Mesh Normal  | Sets custom normals on mesh faces or corners        |
| Set Shade Smooth | Enables or disables smooth shading per face or edge |

### Operations

| Node                    | Description                                                               |
| ----------------------- | ------------------------------------------------------------------------- |
| Dual Mesh               | Creates the dual of a mesh (vertices become faces, faces become vertices) |
| Edge Paths to Curves    | Converts a selection of edge paths into curve geometry                    |
| Edge Paths to Selection | Converts edge paths into a selection field                                |
| Extrude Mesh            | Extrudes vertices, edges, or faces along normals or custom direction      |
| Flip Faces              | Reverses winding order (normal direction) of selected faces               |
| Mesh Boolean            | Boolean operations (union, intersection, difference) between meshes       |
| Mesh to Curve           | Converts mesh edges to curve geometry                                     |
| Mesh to Density Grid    | Converts mesh to a density volume grid                                    |
| Mesh to Points          | Converts mesh elements to a point cloud                                   |
| Mesh to SDF Grid        | Converts mesh surface to a signed distance field volume grid              |
| Mesh to Volume          | Converts mesh to a volume (legacy, prefer Grid nodes)                     |
| Scale Elements          | Scales individual mesh elements from their centers                        |
| Split Edges             | Splits edges, duplicating vertices at the split                           |
| Subdivide Mesh          | Subdivides mesh faces by adding vertices and edges                        |
| Subdivision Surface     | Applies Catmull-Clark or simple subdivision surface smoothing             |
| Triangulate             | Converts all faces to triangles                                           |

### Primitives

| Node       | Description                                         |
| ---------- | --------------------------------------------------- |
| Cone       | Configurable vertices, radius, depth, and fill type |
| Cube       | Configurable size and subdivisions                  |
| Cylinder   | Configurable vertices, radius, depth, and fill type |
| Grid       | Planar grid mesh with configurable subdivisions     |
| Ico Sphere | Configurable radius and subdivisions                |
| Circle     | Edge ring or filled disc                            |
| Line       | Straight or from points                             |
| UV Sphere  | Configurable segments and rings                     |

### Topology

| Node                  | Description                                  |
| --------------------- | -------------------------------------------- |
| Corners of Edge       | Lists the face corners connected to an edge  |
| Corners of Face       | Lists the face corners of a face             |
| Corners of Vertex     | Lists the face corners connected to a vertex |
| Edges of Corner       | Edges adjacent to a face corner              |
| Edges of Vertex       | Edges connected to a vertex                  |
| Face of Corner        | The face that a corner belongs to            |
| Offset Corner in Face | Offsets a corner index within its face       |
| Vertex of Corner      | The vertex that a corner is connected to     |

### UV

| Node         | Description                                     |
| ------------ | ----------------------------------------------- |
| Pack Islands | Packs UV islands into 0-1 UV space              |
| UV Tangent   | Computes tangent vectors from UV maps           |
| Unwrap       | Generates UV coordinates by unwrapping the mesh |

---

## 8. INSTANCE NODES

| Node                   | Description                                                                        |
| ---------------------- | ---------------------------------------------------------------------------------- |
| Instance on Points     | Places an instance at each point; supports per-instance rotation, scale, selection |
| Instances to Points    | Converts instance origins to a point cloud                                         |
| Realize Instances      | Converts instances into real geometry data                                         |
| Rotate Instances       | Rotates instances around their individual pivots                                   |
| Scale Instances        | Scales instances from their individual pivots                                      |
| Set Instance Transform | Sets the full 4x4 transform matrix of each instance                                |
| Translate Instances    | Moves instances by a specified offset                                              |
| Instance Bounds        | Bounding box dimensions of each instance                                           |
| Instance Transform     | Reads the full transform matrix of each instance                                   |
| Instance Rotation      | Reads the rotation component of each instance's transform                          |
| Instance Scale         | Reads the scale component of each instance's transform                             |

---

## 9. POINT NODES

| Node                        | Description                                                  |
| --------------------------- | ------------------------------------------------------------ |
| Distribute Points in Grid   | Distributes points in a regular 3D grid pattern              |
| Distribute Points in Volume | Distributes points inside a volume (random or grid)          |
| Distribute Points on Faces  | Distributes points on mesh surfaces (random or Poisson disk) |
| Points                      | Creates a point cloud with a specified number of points      |
| Points to Curves            | Connects points into curve geometry based on grouping        |
| Points to SDF Grid          | Converts points to a signed distance field volume grid       |
| Points to Vertices          | Converts point cloud points to mesh vertices                 |
| Points to Volume            | Converts a point cloud to a volume                           |
| Set Point Radius            | Sets the display/render radius of point cloud points         |

---

## 10. VOLUME NODES

### Read

| Node           | Description                                                    |
| -------------- | -------------------------------------------------------------- |
| Get Named Grid | Retrieves a volume grid by its name                            |
| Grid Info      | Outputs info about a volume grid (resolution, transform, etc.) |
| Voxel Index    | Outputs the integer voxel index for volume elements            |

### Sample

| Node              | Description                                        |
| ----------------- | -------------------------------------------------- |
| Sample Grid       | Samples a volume grid at a position                |
| Sample Grid Index | Samples a volume grid at a specific voxel index    |
| Grid Advect       | Advects (transports) a grid using a velocity field |
| Grid Curl         | Computes the curl of a vector grid                 |
| Grid Divergence   | Computes the divergence of a vector grid           |
| Grid Gradient     | Computes the gradient of a scalar grid             |
| Grid Laplacian    | Computes the Laplacian of a grid                   |

### Write

| Node                | Description                                          |
| ------------------- | ---------------------------------------------------- |
| Set Grid Background | Sets the background (default) value of a volume grid |
| Set Grid Transform  | Sets the transform of a volume grid                  |
| Store Named Grid    | Stores a grid with a specified name into the volume  |

### Operations

| Node                    | Description                                                       |
| ----------------------- | ----------------------------------------------------------------- |
| Grid to Mesh            | Converts a volume grid to mesh (marching cubes)                   |
| Grid to Points          | Converts active voxels in a grid to point cloud                   |
| Volume to Mesh          | Converts volume data to mesh (legacy)                             |
| SDF Grid Boolean        | Boolean operations on SDF grids (union, intersection, difference) |
| SDF Grid Fillet         | Rounds edges/intersections of SDF grids                           |
| SDF Grid Laplacian      | Applies Laplacian smoothing to an SDF grid                        |
| SDF Grid Mean           | Applies mean filter smoothing to an SDF grid                      |
| SDF Grid Mean Curvature | Computes mean curvature flow on an SDF grid                       |
| SDF Grid Median         | Applies median filter to an SDF grid                              |
| SDF Grid Offset         | Offsets (dilates/erodes) an SDF grid surface                      |
| Field to Grid           | Converts a field to a volume grid                                 |
| Grid Clip               | Clips grid values to a range                                      |
| Grid Dilate and Erode   | Dilates or erodes active voxels in a grid                         |
| Grid Mean               | Applies mean filtering to a generic grid                          |
| Grid Median             | Applies median filtering to a generic grid                        |
| Grid Prune              | Removes inactive or uniform tiles to save memory                  |
| Grid Voxelize           | Converts geometry/data into voxelized grid representation         |

### Primitives

| Node               | Description                                    |
| ------------------ | ---------------------------------------------- |
| Cube Grid Topology | Creates a grid topology in the shape of a cube |
| Volume Cube        | Creates a cube-shaped volume                   |

---

## 11. SIMULATION

| Node            | Description                                                                                              |
| --------------- | -------------------------------------------------------------------------------------------------------- |
| Simulation Zone | State persists between frames; output of frame N becomes input of frame N+1. Includes Delta Time output. |

---

## 12. COLOR NODES

| Node                  | Description                                                   |
| --------------------- | ------------------------------------------------------------- |
| Blackbody             | Converts temperature (Kelvin) to RGB color                    |
| Gamma                 | Applies gamma correction to a color                           |
| Color Ramp (ValToRGB) | Maps a float value to a color using a customizable gradient   |
| RGB Curves            | Adjusts color channels using editable curve controls          |
| Color Mix             | Blends two colors using various blending modes                |
| Combine Color         | Combines individual channel values (RGB/HSV/HSL) into a color |
| Separate Color        | Splits a color into its individual channel components         |

---

## 13. TEXTURE NODES

| Node                | Description                                                               |
| ------------------- | ------------------------------------------------------------------------- |
| Brick Texture       | Procedural brick pattern                                                  |
| Checker Texture     | Checkerboard pattern                                                      |
| Gabor Texture       | Directional noise (new in 5.0)                                            |
| Gradient Texture    | Gradient pattern (linear, quadratic, easing, diagonal, spherical, radial) |
| Image Texture       | Samples colors from an image at specified coordinates                     |
| Magic Texture       | Psychedelic/turbulent color pattern                                       |
| Noise Texture       | Perlin/fBm noise                                                          |
| Voronoi Texture     | Voronoi/Worley cell noise patterns                                        |
| Wave Texture        | Procedural wave patterns (bands or rings)                                 |
| White Noise Texture | Per-point random values (uniform white noise)                             |

---

## 14. UTILITY NODES

### Math

| Node             | Description                                                                        |
| ---------------- | ---------------------------------------------------------------------------------- |
| Bit Math         | Bitwise operations on integers (AND, OR, XOR, NOT, shift)                          |
| Boolean Math     | Boolean logic (AND, OR, NOT, NAND, NOR, XNOR, XOR, NIMPLY)                         |
| Integer Math     | Integer arithmetic operations                                                      |
| Clamp            | Restricts a value to a min and max range                                           |
| Compare          | Compares two values, outputs boolean                                               |
| Float Curve      | Remaps a float value using a custom curve widget                                   |
| Float to Integer | Converts float to integer (round, floor, ceiling, truncate)                        |
| Hash Value       | Generates a deterministic hash from input values                                   |
| Map Range        | Remaps a value from one range to another                                           |
| Math             | Standard math operations (add, subtract, multiply, divide, power, log, trig, etc.) |
| Mix (Float)      | Linearly interpolates between two float values                                     |

### Text / String

| Node               | Description                                                          |
| ------------------ | -------------------------------------------------------------------- |
| Format String      | Creates a formatted string from template with variable substitutions |
| String Join        | Concatenates strings with optional delimiter                         |
| Match String       | Tests if a string matches a pattern                                  |
| Replace String     | Replaces occurrences of a substring                                  |
| Slice String       | Extracts a substring by start position and length                    |
| Find in String     | Finds the position of a substring                                    |
| String Length      | Number of characters in a string                                     |
| String to Curves   | Converts text into curve geometry using a font                       |
| String to Value    | Parses a string as a numerical value                                 |
| Value to String    | Converts a number to string                                          |
| Special Characters | Outputs special characters (newline, tab, etc.)                      |

### Vector

| Node             | Description                                                            |
| ---------------- | ---------------------------------------------------------------------- |
| Combine XYZ      | Combines three floats into a vector                                    |
| Vector Map Range | Remaps vector components from one range to another                     |
| Mix Vector       | Linearly interpolates between two vectors                              |
| Separate XYZ     | Splits a vector into X, Y, Z components                                |
| Radial Tiling    | Tiles geometry or values in a radial pattern                           |
| Vector Curves    | Remaps vector components using curve widgets                           |
| Vector Math      | Vector operations (add, subtract, dot, cross, normalize, length, etc.) |
| Vector Rotate    | Rotates a vector around an axis                                        |

### Field

| Node               | Description                                                      |
| ------------------ | ---------------------------------------------------------------- |
| Accumulate Field   | Running total (prefix sum) of a field over a domain              |
| Evaluate at Index  | Evaluates a field at a specific element index                    |
| Evaluate on Domain | Evaluates a field on a different domain than the current context |
| Field Average      | Average of a field over a domain                                 |
| Field Min and Max  | Minimum and maximum values of a field                            |
| Field Variance     | Variance of a field over a domain                                |

### Rotation

| Node                     | Description                                                   |
| ------------------------ | ------------------------------------------------------------- |
| Align Rotation to Vector | Adjusts a rotation so an axis points toward a target vector   |
| Axes to Rotation         | Creates a rotation from primary and secondary axis directions |
| Axis Angle to Rotation   | Creates a rotation from an axis and angle                     |
| Euler to Rotation        | Converts Euler angles to a rotation value                     |
| Invert Rotation          | Inverts/negates a rotation                                    |
| Mix Rotation             | Interpolates between two rotations (slerp)                    |
| Rotate Rotation          | Combines two rotations                                        |
| Rotate Vector            | Applies a rotation to a vector                                |
| Rotation to Axis Angle   | Decomposes a rotation into axis and angle                     |
| Rotation to Euler        | Converts a rotation to Euler angles                           |
| Rotation to Quaternion   | Converts a rotation to quaternion components (W, X, Y, Z)     |
| Quaternion to Rotation   | Creates a rotation from quaternion components                 |

### Matrix

| Node                | Description                                                         |
| ------------------- | ------------------------------------------------------------------- |
| Combine Matrix      | Creates a 4x4 matrix from column vectors                            |
| Combine Transform   | Creates a transform matrix from translation, rotation, and scale    |
| Determinant         | Computes the determinant of a matrix                                |
| Invert Matrix       | Computes the inverse of a matrix                                    |
| Matrix Multiply     | Multiplies two matrices                                             |
| Matrix SVD          | Singular Value Decomposition of a matrix                            |
| Project Point       | Projects a 3D point using a projection matrix                       |
| Separate Matrix     | Splits a 4x4 matrix into column vectors                             |
| Separate Transform  | Decomposes a transform matrix into translation, rotation, and scale |
| Transform Direction | Transforms a direction vector by a matrix (ignoring translation)    |
| Transform Point     | Transforms a point by a matrix                                      |
| Transpose Matrix    | Transposes a matrix                                                 |

### Bundle

| Node              | Description                                     |
| ----------------- | ----------------------------------------------- |
| Combine Bundle    | Creates a bundle from multiple named data items |
| Separate Bundle   | Extracts named data items from a bundle         |
| Get Bundle Item   | Retrieves a specific item from a bundle by name |
| Store Bundle Item | Stores a data item into a bundle                |
| Join Bundle       | Merges multiple bundles                         |

### Closure

| Node             | Description                                                                |
| ---------------- | -------------------------------------------------------------------------- |
| Closure Zone     | Defines a reusable sub-graph that can be passed around and evaluated later |
| Evaluate Closure | Evaluates/executes a closure with provided inputs                          |

### List

| Node          | Description                                        |
| ------------- | -------------------------------------------------- |
| Field to List | Converts a field (per-element) to an explicit list |
| List Get Item | Retrieves an item from a list by index             |
| List Length   | Number of items in a list                          |

### Flow Control

| Node                  | Description                                                                  |
| --------------------- | ---------------------------------------------------------------------------- |
| For Each Element Zone | Iterates over each element, executing zone body per element                  |
| Index Switch          | Selects one of several inputs based on an integer index                      |
| Menu Switch           | Selects one of several inputs based on a dropdown menu                       |
| Random Value          | Generates random values (float, integer, vector, boolean) with optional seed |
| Repeat Zone           | Executes contained nodes a specified number of iterations (loop)             |
| Switch                | Selects between two inputs based on a boolean condition                      |

---

## 15. LAYOUT NODES

| Node    | Description                                  |
| ------- | -------------------------------------------- |
| Frame   | Groups nodes visually inside a labeled box   |
| Reroute | Pass-through node for cleaner noodle routing |

---

## Blender 5.0 New Features (vs 4.x)

### New Node Types

- **Bundle system**: Combine Bundle, Separate Bundle, Get/Store Bundle Item, Join Bundle
- **Closure system**: Closure Zone, Evaluate Closure
- **For Each Element Zone**: Per-element iteration
- **Import nodes**: CSV, OBJ, PLY, STL, Text, VDB file import directly in geometry nodes
- **Gizmo nodes**: Dial, Linear, Transform gizmos for interactive viewport input
- **Matrix nodes**: Full 4x4 matrix math (combine, separate, multiply, SVD, determinant, invert, transpose, transform point/direction, project)
- **List operations**: Field to List, List Get Item, List Length
- **Grid/SDF operations**: Extensive volume grid manipulation (advect, curl, divergence, gradient, laplacian, clip, dilate/erode, mean, median, prune, voxelize, SDF boolean/fillet/offset)
- **Gabor Texture**: Directional procedural noise
- **Radial Tiling**: Radial pattern creation
- **Bit Math**: Bitwise integer operations
- **Format String, Match String, Find in String**: String processing
- **Field Average, Field Min and Max, Field Variance**: Statistical field analysis
- **Hash Value**: Deterministic hashing
- **Integer Math**: Separate from float Math
- **Camera Info, Bone Info**: Scene input nodes
- **Warning, Enable Output**: Output control
- **Set Geometry Name, Viewport Transform, Mouse Position, Image Info, Collection Children**

### New Socket Types

- **Bundle**: Heterogeneous named data container
- **Closure**: Deferred computation / callable sub-graph
- **Font**: Font data-block reference
- **Matrix**: 4x4 transformation matrix
