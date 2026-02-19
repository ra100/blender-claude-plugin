# Blender Python Scripting - API Reference

## Operators

### Basic Operator

```python
import bpy

class MYADDON_OT_simple(bpy.types.Operator):
    """Tooltip description"""
    bl_idname = "myaddon.simple"
    bl_label = "Simple Operator"
    bl_description = "Does something simple"
    bl_options = {'REGISTER', 'UNDO'}

    # Properties (shown in operator panel / redo sidebar)
    strength: bpy.props.FloatProperty(
        name="Strength",
        default=1.0,
        min=0.0,
        max=10.0,
    )
    mode: bpy.props.EnumProperty(
        name="Mode",
        items=[
            ('ADD', "Add", "Add mode"),
            ('SUBTRACT', "Subtract", "Subtract mode"),
        ],
        default='ADD',
    )

    @classmethod
    def poll(cls, context):
        return context.active_object is not None

    def execute(self, context):
        obj = context.active_object
        self.report({'INFO'}, f"Processed {obj.name} with strength {self.strength}")
        return {'FINISHED'}
```

### Modal Operator

```python
class MYADDON_OT_modal(bpy.types.Operator):
    bl_idname = "myaddon.modal"
    bl_label = "Modal Operator"
    bl_options = {'REGISTER', 'UNDO'}

    _timer = None

    def modal(self, context, event):
        if event.type == 'ESC':
            self.cancel(context)
            return {'CANCELLED'}

        if event.type == 'TIMER':
            # Do periodic work
            pass

        if event.type == 'LEFTMOUSE' and event.value == 'PRESS':
            # Finish on click
            return {'FINISHED'}

        return {'RUNNING_MODAL'}

    def execute(self, context):
        self._timer = context.window_manager.event_timer_add(0.1, window=context.window)
        context.window_manager.modal_handler_add(self)
        return {'RUNNING_MODAL'}

    def cancel(self, context):
        if self._timer:
            context.window_manager.event_timer_remove(self._timer)
```

### Mouse-Interactive Modal

```python
class MYADDON_OT_mouse_modal(bpy.types.Operator):
    bl_idname = "myaddon.mouse_modal"
    bl_label = "Mouse Modal"
    bl_options = {'REGISTER', 'UNDO'}

    def modal(self, context, event):
        if event.type == 'MOUSEMOVE':
            delta = event.mouse_x - self.init_x
            context.active_object.location.x = self.init_loc_x + delta * 0.01
            return {'RUNNING_MODAL'}

        elif event.type == 'LEFTMOUSE':
            return {'FINISHED'}

        elif event.type in {'RIGHTMOUSE', 'ESC'}:
            context.active_object.location.x = self.init_loc_x
            return {'CANCELLED'}

        return {'RUNNING_MODAL'}

    def invoke(self, context, event):
        if context.active_object:
            self.init_x = event.mouse_x
            self.init_loc_x = context.active_object.location.x
            context.window_manager.modal_handler_add(self)
            return {'RUNNING_MODAL'}
        else:
            self.report({'WARNING'}, "No active object")
            return {'CANCELLED'}
```

### File Dialog Operator

```python
from bpy_extras.io_utils import ImportHelper, ExportHelper

class MYADDON_OT_import(bpy.types.Operator, ImportHelper):
    bl_idname = "myaddon.import_data"
    bl_label = "Import Data"

    filename_ext = ".json"
    filter_glob: bpy.props.StringProperty(default="*.json", options={'HIDDEN'})

    def execute(self, context):
        import json
        with open(self.filepath, 'r') as f:
            data = json.load(f)
        self.report({'INFO'}, f"Imported from {self.filepath}")
        return {'FINISHED'}

class MYADDON_OT_export(bpy.types.Operator, ExportHelper):
    bl_idname = "myaddon.export_data"
    bl_label = "Export Data"

    filename_ext = ".json"
    filter_glob: bpy.props.StringProperty(default="*.json", options={'HIDDEN'})

    def execute(self, context):
        import json
        data = {"objects": [o.name for o in context.scene.objects]}
        with open(self.filepath, 'w') as f:
            json.dump(data, f, indent=2)
        return {'FINISHED'}
```

### Invoke with Confirmation

```python
class MYADDON_OT_confirm(bpy.types.Operator):
    bl_idname = "myaddon.confirm"
    bl_label = "Delete All Objects"

    def invoke(self, context, event):
        return context.window_manager.invoke_confirm(self, event)

    def execute(self, context):
        bpy.ops.object.select_all(action='SELECT')
        bpy.ops.object.delete()
        return {'FINISHED'}
```

### Invoke with Properties Dialog

```python
class MYADDON_OT_with_props(bpy.types.Operator):
    bl_idname = "myaddon.with_props"
    bl_label = "Create Grid"

    count_x: bpy.props.IntProperty(name="Count X", default=5, min=1, max=100)
    count_y: bpy.props.IntProperty(name="Count Y", default=5, min=1, max=100)
    spacing: bpy.props.FloatProperty(name="Spacing", default=2.0, min=0.1)

    def invoke(self, context, event):
        return context.window_manager.invoke_props_dialog(self)

    def execute(self, context):
        for x in range(self.count_x):
            for y in range(self.count_y):
                bpy.ops.mesh.primitive_cube_add(
                    location=(x * self.spacing, y * self.spacing, 0)
                )
        return {'FINISHED'}
```

## Panels

### Basic Panel

```python
class MYADDON_PT_main(bpy.types.Panel):
    bl_label = "My Add-on"
    bl_idname = "MYADDON_PT_main"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = "My Tab"

    def draw(self, context):
        layout = self.layout
        layout.operator("myaddon.simple")
        layout.prop(context.scene, "my_custom_prop")
```

### Sub-Panel

```python
class MYADDON_PT_sub(bpy.types.Panel):
    bl_label = "Sub Settings"
    bl_idname = "MYADDON_PT_sub"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = "My Tab"
    bl_parent_id = "MYADDON_PT_main"
    bl_options = {'DEFAULT_CLOSED'}

    def draw(self, context):
        layout = self.layout
        layout.label(text="Advanced Settings")
```

### Conditional Panel

```python
class MYADDON_PT_conditional(bpy.types.Panel):
    bl_label = "Mesh Tools"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = "My Tab"

    @classmethod
    def poll(cls, context):
        return context.active_object and context.active_object.type == 'MESH'

    def draw(self, context):
        layout = self.layout
        obj = context.active_object
        layout.label(text=f"Vertices: {len(obj.data.vertices)}")
        layout.label(text=f"Faces: {len(obj.data.polygons)}")
```

### Panel in Properties Editor

```python
class MYADDON_PT_object_props(bpy.types.Panel):
    bl_label = "My Object Properties"
    bl_space_type = 'PROPERTIES'
    bl_region_type = 'WINDOW'
    bl_context = "object"  # object, modifier, particle, physics, constraint, data, material, render, scene, world

    def draw(self, context):
        layout = self.layout
        obj = context.object
        layout.prop(obj, "name")
```

### Layout Helpers

```python
def draw(self, context):
    layout = self.layout

    # Row (horizontal)
    row = layout.row(align=True)
    row.operator("myaddon.op_a")
    row.operator("myaddon.op_b")

    # Column (vertical, default)
    col = layout.column(align=True)
    col.prop(context.scene, "prop_a")
    col.prop(context.scene, "prop_b")

    # Box (framed group)
    box = layout.box()
    box.label(text="Settings")
    box.prop(context.scene, "prop_c")

    # Split (proportional columns)
    split = layout.split(factor=0.3)
    split.label(text="Label:")
    split.prop(context.scene, "prop_d", text="")

    # Grid flow
    grid = layout.grid_flow(row_major=True, columns=3, align=True)
    for i in range(9):
        grid.operator("myaddon.simple", text=str(i))

    # Separator
    layout.separator()

    # Enabled/active control
    row = layout.row()
    row.enabled = context.active_object is not None
    row.operator("myaddon.simple")
```

## Menus

### Custom Menu

```python
class MYADDON_MT_main(bpy.types.Menu):
    bl_label = "My Menu"
    bl_idname = "MYADDON_MT_main"

    def draw(self, context):
        layout = self.layout
        layout.operator("myaddon.simple")
        layout.separator()
        layout.menu("MYADDON_MT_sub")

class MYADDON_MT_sub(bpy.types.Menu):
    bl_label = "Sub Menu"
    bl_idname = "MYADDON_MT_sub"

    def draw(self, context):
        layout = self.layout
        layout.operator("myaddon.op_a")
        layout.operator("myaddon.op_b")

# Append to existing menu
def draw_menu(self, context):
    self.layout.menu("MYADDON_MT_main")

def register():
    bpy.types.VIEW3D_MT_object.append(draw_menu)

def unregister():
    bpy.types.VIEW3D_MT_object.remove(draw_menu)
```

### Pie Menu

```python
class MYADDON_MT_pie(bpy.types.Menu):
    bl_label = "My Pie"
    bl_idname = "MYADDON_MT_pie"

    def draw(self, context):
        layout = self.layout
        pie = layout.menu_pie()
        pie.operator("myaddon.op_a")  # Left
        pie.operator("myaddon.op_b")  # Right
        pie.operator("myaddon.op_c")  # Bottom
        pie.operator("myaddon.op_d")  # Top
```

## PropertyGroups

### Basic PropertyGroup

```python
class MYADDON_PG_settings(bpy.types.PropertyGroup):
    enabled: bpy.props.BoolProperty(name="Enabled", default=True)
    count: bpy.props.IntProperty(name="Count", default=5, min=1, max=100)
    scale: bpy.props.FloatProperty(name="Scale", default=1.0, min=0.01)
    mode: bpy.props.EnumProperty(
        name="Mode",
        items=[
            ('FAST', "Fast", "Fast mode"),
            ('QUALITY', "Quality", "Quality mode"),
        ],
    )
    color: bpy.props.FloatVectorProperty(
        name="Color", subtype='COLOR',
        default=(1.0, 1.0, 1.0), min=0.0, max=1.0, size=3,
    )

def register():
    bpy.utils.register_class(MYADDON_PG_settings)
    bpy.types.Scene.my_settings = bpy.props.PointerProperty(type=MYADDON_PG_settings)

def unregister():
    del bpy.types.Scene.my_settings
    bpy.utils.unregister_class(MYADDON_PG_settings)
```

### CollectionProperty (Dynamic Lists)

```python
class MYADDON_PG_item(bpy.types.PropertyGroup):
    name: bpy.props.StringProperty(name="Name", default="Item")
    value: bpy.props.FloatProperty(name="Value", default=0.0)

class MYADDON_PG_settings(bpy.types.PropertyGroup):
    items: bpy.props.CollectionProperty(type=MYADDON_PG_item)
    active_index: bpy.props.IntProperty()

# Usage
settings = context.scene.my_settings
item = settings.items.add()
item.name = "New Item"
item.value = 1.5
settings.items.remove(0)  # Remove first item
```

### UIList

```python
class MYADDON_UL_items(bpy.types.UIList):
    def draw_item(self, context, layout, data, item, icon, active_data, active_property, index):
        if self.layout_type in {'DEFAULT', 'COMPACT'}:
            layout.prop(item, "name", text="", emboss=False, icon='OBJECT_DATA')
            layout.prop(item, "value", text="")
        elif self.layout_type == 'GRID':
            layout.alignment = 'CENTER'
            layout.label(text="", icon='OBJECT_DATA')

# In panel draw:
def draw(self, context):
    settings = context.scene.my_settings
    layout = self.layout
    layout.template_list("MYADDON_UL_items", "", settings, "items", settings, "active_index")
```

## Handlers

### Application Handlers

```python
from bpy.app.handlers import persistent

@persistent
def on_frame_change(scene):
    """Called every frame change"""
    frame = scene.frame_current
    for obj in scene.objects:
        if obj.get("auto_rotate"):
            obj.rotation_euler.z = frame * 0.1

@persistent
def on_load_post(dummy):
    """Called after file is loaded"""
    print("File loaded:", bpy.data.filepath)

@persistent
def on_render_pre(scene):
    """Called before each render"""
    print(f"Rendering frame {scene.frame_current}")

@persistent
def on_save_pre(dummy):
    """Called before file save"""
    print("Saving file...")

@persistent
def on_depsgraph_update(scene, depsgraph):
    """Called when dependency graph updates"""
    for update in depsgraph.updates:
        print(f"Updated: {update.id.name}")

def register():
    bpy.app.handlers.frame_change_post.append(on_frame_change)
    bpy.app.handlers.load_post.append(on_load_post)
    bpy.app.handlers.render_pre.append(on_render_pre)
    bpy.app.handlers.save_pre.append(on_save_pre)
    bpy.app.handlers.depsgraph_update_post.append(on_depsgraph_update)

def unregister():
    bpy.app.handlers.frame_change_post.remove(on_frame_change)
    bpy.app.handlers.load_post.remove(on_load_post)
    bpy.app.handlers.render_pre.remove(on_render_pre)
    bpy.app.handlers.save_pre.remove(on_save_pre)
    bpy.app.handlers.depsgraph_update_post.remove(on_depsgraph_update)
```

### Available Handlers

```python
bpy.app.handlers.frame_change_pre      # Before frame change
bpy.app.handlers.frame_change_post     # After frame change
bpy.app.handlers.render_pre            # Before render
bpy.app.handlers.render_post           # After render
bpy.app.handlers.render_cancel         # Render cancelled
bpy.app.handlers.render_complete       # Render completed
bpy.app.handlers.render_init           # Render engine init
bpy.app.handlers.render_stats          # Render stats update
bpy.app.handlers.render_write          # After render frame write
bpy.app.handlers.load_pre              # Before file load
bpy.app.handlers.load_post             # After file load
bpy.app.handlers.save_pre              # Before file save
bpy.app.handlers.save_post             # After file save
bpy.app.handlers.undo_pre              # Before undo
bpy.app.handlers.undo_post             # After undo
bpy.app.handlers.redo_pre              # Before redo
bpy.app.handlers.redo_post             # After redo
bpy.app.handlers.depsgraph_update_pre  # Before depsgraph update
bpy.app.handlers.depsgraph_update_post # After depsgraph update
bpy.app.handlers.version_update        # After version update
bpy.app.handlers.object_bake_pre       # Before object bake
bpy.app.handlers.object_bake_complete  # After object bake
```

## Timers

```python
def my_timer():
    """Return interval in seconds for next call, or None to stop"""
    print("Timer tick")
    return 1.0  # Call again in 1 second

def register():
    bpy.app.timers.register(my_timer, first_interval=0.5)

def unregister():
    if bpy.app.timers.is_registered(my_timer):
        bpy.app.timers.unregister(my_timer)
```

### Timer with State

```python
def create_countdown(count):
    def timer_func():
        nonlocal count
        count -= 1
        print(f"Countdown: {count}")
        if count <= 0:
            print("Done!")
            return None  # Stop timer
        return 1.0  # Continue every second
    return timer_func

bpy.app.timers.register(create_countdown(10))
```

## Keymaps

```python
addon_keymaps = []

def register():
    wm = bpy.context.window_manager
    kc = wm.keyconfigs.addon
    if kc:
        km = kc.keymaps.new(name='3D View', space_type='VIEW_3D')
        kmi = km.keymap_items.new("myaddon.simple", type='Q', value='PRESS', shift=True)
        kmi.properties.strength = 2.0  # Set operator property
        addon_keymaps.append((km, kmi))

def unregister():
    for km, kmi in addon_keymaps:
        km.keymap_items.remove(kmi)
    addon_keymaps.clear()
```

## Add-on Preferences

```python
class MYADDON_AP_preferences(bpy.types.AddonPreferences):
    bl_idname = __name__  # Must match module name

    api_key: bpy.props.StringProperty(name="API Key", subtype='PASSWORD')
    debug_mode: bpy.props.BoolProperty(name="Debug Mode", default=False)
    output_path: bpy.props.StringProperty(name="Output Path", subtype='DIR_PATH')

    def draw(self, context):
        layout = self.layout
        layout.prop(self, "api_key")
        layout.prop(self, "debug_mode")
        layout.prop(self, "output_path")

# Access preferences
def get_prefs():
    return bpy.context.preferences.addons[__name__].preferences
```

## Context Override (Blender 4.0+ / 5.x)

```python
# Override active object
with bpy.context.temp_override(active_object=obj, selected_objects=[obj]):
    bpy.ops.object.shade_smooth()

# Override area type for viewport operations
for area in bpy.context.screen.areas:
    if area.type == 'VIEW_3D':
        for region in area.regions:
            if region.type == 'WINDOW':
                with bpy.context.temp_override(area=area, region=region):
                    bpy.ops.view3d.view_all()
                break
        break
```

## Script Utilities

### Select/Deselect

```python
bpy.ops.object.select_all(action='DESELECT')
obj.select_set(True)
bpy.context.view_layer.objects.active = obj
```

### Delete Objects

```python
bpy.ops.object.select_all(action='DESELECT')
for obj in objects_to_delete:
    obj.select_set(True)
bpy.ops.object.delete()
```

### Duplicate Object

```python
new_obj = obj.copy()
new_obj.data = obj.data.copy()  # Deep copy mesh data
bpy.context.collection.objects.link(new_obj)
```

### Collection Management

```python
# Create collection
coll = bpy.data.collections.new("My Collection")
bpy.context.scene.collection.children.link(coll)

# Move object to collection
coll.objects.link(obj)
bpy.context.scene.collection.objects.unlink(obj)  # Remove from default
```

### Purge Unused Data

```python
# Remove orphan data blocks
bpy.ops.outliner.orphans_purge(do_local_ids=True, do_linked_ids=True, do_recursive=True)
```

### Scene/Render Settings

```python
scene = bpy.context.scene

# Resolution
scene.render.resolution_x = 1920
scene.render.resolution_y = 1080
scene.render.resolution_percentage = 100

# Frame range
scene.frame_start = 1
scene.frame_end = 250
scene.frame_current = 1

# Output format
scene.render.image_settings.file_format = 'PNG'  # PNG, JPEG, OPEN_EXR, FFMPEG, etc.
scene.render.filepath = "/tmp/render_"

# Render engine
scene.render.engine = 'CYCLES'  # CYCLES, BLENDER_EEVEE, BLENDER_WORKBENCH

# Cycles settings
scene.cycles.samples = 128
scene.cycles.use_denoising = True
scene.cycles.device = 'GPU'

# EEVEE settings (Blender 5.x)
scene.eevee.taa_render_samples = 64
```

## Operator Return Values

| Value               | Meaning                            |
| ------------------- | ---------------------------------- |
| `{'FINISHED'}`      | Operation completed successfully   |
| `{'CANCELLED'}`     | Operation cancelled (no undo push) |
| `{'RUNNING_MODAL'}` | Operator continues running modally |
| `{'PASS_THROUGH'}`  | Pass event to other operators      |
| `{'INTERFACE'}`     | Handle as interface event          |

## bl_options Flags

| Flag             | Description                                |
| ---------------- | ------------------------------------------ |
| `'REGISTER'`     | Show in operator search, enable redo panel |
| `'UNDO'`         | Push undo step                             |
| `'UNDO_GROUPED'` | Group multiple undo steps                  |
| `'BLOCKING'`     | Block other operators while running        |
| `'MACRO'`        | Part of a macro                            |
| `'GRAB_CURSOR'`  | Grab mouse cursor during modal             |
| `'PRESET'`       | Show preset selector in redo panel         |
| `'INTERNAL'`     | Not shown in search or menus               |
