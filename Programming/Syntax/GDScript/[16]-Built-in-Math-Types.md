[Previous](./[15]-Enums-and-Constants.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[17]-Static-Typing.md)

*Data Structures*

# Lesson 16 - Built-in Math Types: Vectors, Colors, Rects & Transforms

Games are full of positions, directions, sizes, colors, and rotations. Godot gives you built-in types for all of them. They are **value types** (assigning one copies it), they are fast, and they come with many helpful methods. This lesson introduces the ones you will use most.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 16.1 `Vector2` and `Vector2i`

A **`Vector2`** holds two floats, `x` and `y`. It is used for 2D positions, directions, velocities, and sizes. A **`Vector2i`** holds two **integers** and is used for grid coordinates and pixel sizes.

```gdscript
extends Node


func _ready() -> void:
	var position := Vector2(100, 50)
	print(position.x)          # 100.0
	print(position.y)          # 50.0

	position.x = 120.0
	position.y += 10.0
	print(position)            # (120, 60)

	var cell := Vector2i(3, 4)
	print(cell)                # (3, 4)
```

### Useful constants

```gdscript
extends Node


func _ready() -> void:
	print(Vector2.ZERO)    # (0, 0)
	print(Vector2.ONE)     # (1, 1)
	print(Vector2.UP)      # (0, -1)   In 2D, y points DOWN on screen, so UP is negative y
	print(Vector2.DOWN)    # (0, 1)
	print(Vector2.LEFT)    # (-1, 0)
	print(Vector2.RIGHT)   # (1, 0)
```

### Arithmetic

Vectors can be added, subtracted, multiplied, and divided:

```gdscript
extends Node


func _ready() -> void:
	var a := Vector2(1, 2)
	var b := Vector2(3, 4)

	print(a + b)         # (4, 6)
	print(b - a)         # (2, 2)
	print(a * 3)         # (3, 6)     scale by a number
	print(a * b)         # (3, 8)     component by component
	print(b / 2)         # (1.5, 2)
	print(-a)            # (-1, -2)
	print(a == Vector2(1, 2))   # true
```

### Converting between `Vector2` and `Vector2i`

```gdscript
extends Node


func _ready() -> void:
	var v := Vector2(3.7, 4.2)
	var i := Vector2i(v)         # (3, 4)   (decimals are cut off)
	var back := Vector2(i)       # (3, 4)
	print(i, back)
	print(v.floor(), v.ceil(), v.round())   # (3, 4) (4, 5) (4, 4)
```

Common uses of `Vector2` in a node: `position`, `global_position`, `scale`, `velocity`, and `size` are all vectors.

---

## 16.2 `Vector3` and `Vector3i`

`Vector3` adds a `z` component and is used for 3D positions, directions, and scales. `Vector3i` is the integer version, used for voxel and grid coordinates.

```gdscript
extends Node


func _ready() -> void:
	var p := Vector3(1, 2, 3)
	print(p.x, p.y, p.z)       # 123  (printed together)
	p.z = 10.0
	print(p)                   # (1, 2, 10)

	print(Vector3.UP)          # (0, 1, 0)
	print(Vector3.FORWARD)     # (0, 0, -1)
	print(Vector3.RIGHT)       # (1, 0, 0)
	print(Vector3.ZERO)        # (0, 0, 0)

	print(Vector3(1, 2, 3) + Vector3(4, 5, 6))    # (5, 7, 9)
	print(Vector3(1, 2, 3) * 2)                   # (2, 4, 6)
```

In Godot's 3D coordinate system:

- **+X** points right, **+Y** points up, and **-Z** points forward. (`Vector3.FORWARD` is `(0, 0, -1)`.)
- This differs from 2D, where +Y points down.

Everything in the next sub-lesson also applies to `Vector3`.

---

## 16.3 Vector Math: Length, Normalize, Dot, Cross, Distance, Direction

### Length

The **length** (also called magnitude) of a vector is its distance from the origin:

```gdscript
extends Node


func _ready() -> void:
	var v := Vector2(3, 4)
	print(v.length())            # 5.0    (the Pythagorean theorem)
	print(v.length_squared())    # 25.0   (faster, no square root)
```

Use `length_squared()` for comparisons (for example `v.length_squared() < 100 * 100`) when speed matters.

### Normalize

A **normalized** vector keeps its direction but has a length of exactly `1`. It is also called a **unit vector** and is used for pure directions.

```gdscript
extends Node


func _ready() -> void:
	var v := Vector2(3, 4)
	var dir := v.normalized()
	print(dir)                   # (0.6, 0.8)
	print(dir.length())          # 1.0

	print(Vector2.ZERO.normalized())   # (0, 0)  (safe: no division by zero)
```

A classic use is making diagonal movement the same speed as straight movement:

```gdscript
extends Node

const SPEED := 200.0


func get_velocity() -> Vector2:
	var input := Input.get_vector("move_left", "move_right", "move_up", "move_down")
	return input * SPEED      # get_vector already limits the length to 1
```

### Distance and direction

```gdscript
extends Node


func _ready() -> void:
	var player := Vector2(0, 0)
	var enemy := Vector2(30, 40)

	print(player.distance_to(enemy))           # 50.0
	print(player.distance_squared_to(enemy))   # 2500.0
	print(player.direction_to(enemy))          # (0.6, 0.8)  a unit vector pointing from player to enemy
	print(enemy - player)                      # (30, 40)    the raw offset (not normalized)
	print(player.angle_to_point(enemy))        # angle in radians
```

`direction_to()` is the shortcut for `(target - from).normalized()`. To move something toward a target:

```gdscript
extends Node2D

var speed := 100.0
var target := Vector2(400, 300)


func _process(delta: float) -> void:
	position += position.direction_to(target) * speed * delta
```

Or use `move_toward()` to avoid overshooting:

```gdscript
extends Node2D

var target := Vector2(400, 300)


func _process(delta: float) -> void:
	position = position.move_toward(target, 100.0 * delta)
```

### Dot product

The **dot product** of two vectors is a single number that tells you how much they point in the **same direction**:

```gdscript
extends Node


func _ready() -> void:
	var a := Vector2.RIGHT
	print(a.dot(Vector2.RIGHT))    # 1.0   same direction
	print(a.dot(Vector2.UP))       # 0.0   perpendicular
	print(a.dot(Vector2.LEFT))     # -1.0  opposite directions
```

For **unit vectors**, a result near `1` means "facing the same way", `0` means "at a right angle", and `-1` means "facing opposite ways". It is used for field-of-view checks:

```gdscript
extends Node2D

var facing := Vector2.RIGHT


func can_see(target_position: Vector2) -> bool:
	var to_target := global_position.direction_to(target_position)
	return facing.dot(to_target) > 0.5     # Roughly within 60 degrees in front
```

### Cross product (3D)

In 3D, the **cross product** returns a vector perpendicular to both inputs. It is used for surface normals and finding a "sideways" direction:

```gdscript
extends Node


func _ready() -> void:
	var right := Vector3.RIGHT
	var up := Vector3.UP
	print(right.cross(up))     # (0, 0, 1)
```

In 2D, `Vector2.cross()` returns a single number (the z part), whose sign tells you whether the second vector is clockwise or counter-clockwise from the first.

### Other helpful methods

```gdscript
extends Node


func _ready() -> void:
	var v := Vector2(3, 4)
	print(v.rotated(PI / 2))             # (-4, 3)  rotated 90 degrees
	print(v.limit_length(2.0))           # shortened to a maximum length of 2
	print(v.lerp(Vector2.ZERO, 0.5))     # (1.5, 2)  halfway to zero
	print(v.clamp(Vector2.ZERO, Vector2(2, 2)))   # (2, 2)
	print(v.bounce(Vector2.UP))          # reflect off a surface with the given normal
	print(v.angle())                     # angle from the positive X axis, in radians
	print(v.abs(), v.sign())             # (3, 4) (1, 1)
	print(Vector2.from_angle(PI))        # (-1, 0) roughly: a unit vector at the given angle
```

---

## 16.4 `Color`

A **`Color`** stores red, green, blue, and alpha (opacity) values, each from `0.0` to `1.0`.

```gdscript
extends Node


func _ready() -> void:
	var red := Color(1, 0, 0)               # Red, fully opaque
	var half_blue := Color(0, 0, 1, 0.5)    # Blue, 50% transparent
	print(red.r, red.g, red.b, red.a)       # 1.00.00.01.0 (printed together)
	print(half_blue.a)                      # 0.5
```

### Ways to create a color

```gdscript
extends Node


func _ready() -> void:
	var a := Color.CORNFLOWER_BLUE          # Named constants (about 150)
	var b := Color(0.2, 0.6, 1.0)           # RGB from 0 to 1
	var c := Color8(51, 153, 255)           # RGB from 0 to 255
	var d := Color("#3399ff")               # From a hex code
	var e := Color.html("#3399ff80")        # Hex with alpha (RRGGBBAA)
	var f := Color.from_hsv(0.6, 0.8, 1.0)  # Hue, saturation, value (all 0 to 1)
	print(a, b, c, d, e, f)
```

### Working with colors

```gdscript
extends Node


func _ready() -> void:
	var c := Color.RED
	c.a = 0.5                                  # Change opacity
	print(c.to_html())                         # "ff000080"
	print(Color.RED.lerp(Color.BLUE, 0.5))     # Halfway between red and blue
	print(Color.RED.darkened(0.3))             # Darker
	print(Color.RED.lightened(0.3))            # Lighter
	print(Color.RED.inverted())                # (0, 1, 1, 1)
	print(Color.RED.h, Color.RED.s, Color.RED.v)   # hue, saturation, value
```

Colors are used for the `modulate` and `self_modulate` properties of nodes (a tint), for drawing, for labels, and for many other things:

```gdscript
extends Sprite2D


func flash_red() -> void:
	modulate = Color.RED


func fade(amount: float) -> void:
	modulate.a = amount      # Change only the transparency
```

Note: `Color(1, 1, 1)` (white) is the "no tint" value for `modulate`.

---

## 16.5 `Rect2` and `AABB`

### `Rect2`

A **`Rect2`** is a 2D rectangle defined by a `position` (the top-left corner) and a `size`. It is used for collision-free overlap checks, texture regions, UI areas, and bounds. `Rect2i` is the integer version.

```gdscript
extends Node


func _ready() -> void:
	var rect := Rect2(10, 20, 100, 50)        # x, y, width, height
	var same := Rect2(Vector2(10, 20), Vector2(100, 50))

	print(rect.position)     # (10, 20)
	print(rect.size)         # (100, 50)
	print(rect.end)          # (110, 70)   the bottom-right corner
	print(rect.get_center()) # (60, 45)
	print(rect.area)         # 5000.0
```

Useful tests:

```gdscript
extends Node


func _ready() -> void:
	var rect := Rect2(0, 0, 100, 100)

	print(rect.has_point(Vector2(50, 50)))     # true  (the point is inside)
	print(rect.has_point(Vector2(150, 50)))    # false

	var other := Rect2(80, 80, 50, 50)
	print(rect.intersects(other))              # true
	print(rect.intersection(other))            # [P: (80, 80), S: (20, 20)]
	print(rect.merge(other))                   # the smallest rect containing both
	print(rect.grow(10))                       # expanded by 10 on every side
```

A common use is keeping a character inside the screen:

```gdscript
extends Node2D


func _process(_delta: float) -> void:
	var screen := get_viewport_rect()
	position = position.clamp(screen.position, screen.end)
```

### `AABB`

An **`AABB`** (Axis-Aligned Bounding Box) is the 3D version: a `position` and a `size`, both `Vector3`. It is used for bounds of meshes and 3D volumes.

```gdscript
extends Node


func _ready() -> void:
	var box := AABB(Vector3(0, 0, 0), Vector3(2, 2, 2))
	print(box.get_center())                   # (1, 1, 1)
	print(box.has_point(Vector3(1, 1, 1)))    # true
	print(box.get_volume())                   # 8.0
	print(box.intersects(AABB(Vector3(1, 1, 1), Vector3(2, 2, 2))))   # true
```

---

## 16.6 `Transform2D`, `Transform3D`, and `Basis`

A **transform** combines a position, a rotation, and a scale into one value. Every `Node2D` and `Node3D` has one that describes where it is and how it is oriented.

### `Transform2D`

A `Transform2D` has three `Vector2` columns: `x` and `y` (the rotation and scale) and `origin` (the position).

```gdscript
extends Node


func _ready() -> void:
	var t := Transform2D(PI / 2, Vector2(100, 50))    # rotation in radians, then position
	print(t.origin)           # (100, 50)
	print(t.get_rotation())   # 1.570796...
	print(t.get_scale())      # (1, 1)

	# Transform a point from local space to the parent's space
	var world_point := t * Vector2(10, 0)
	print(world_point)        # roughly (100, 60)

	# And back again
	print(t.affine_inverse() * world_point)   # roughly (10, 0)
```

In a node, the transform properties are:

```gdscript
extends Node2D


func _ready() -> void:
	print(position)           # Position relative to the parent
	print(global_position)    # Position in the world
	print(rotation)           # Radians
	print(rotation_degrees)   # Degrees
	print(scale)
	print(transform)          # Local Transform2D
	print(global_transform)   # World Transform2D
```

Useful helpers: `to_local(global_point)` and `to_global(local_point)`.

### `Basis`

A **`Basis`** is a 3x3 matrix for **rotation and scale in 3D** (no position). Its three vectors `x`, `y`, `z` are the directions of the local axes.

```gdscript
extends Node


func _ready() -> void:
	var b := Basis.IDENTITY                       # No rotation, no scale
	var rotated := Basis(Vector3.UP, PI / 2)      # Rotate 90 degrees around the Y axis
	print(rotated * Vector3.FORWARD)              # roughly (-1, 0, 0)
	print(rotated.get_euler())                    # Euler angles in radians
	print(b.x, b.y, b.z)
```

### `Transform3D`

A **`Transform3D`** is a `Basis` plus an `origin` (a `Vector3` position):

```gdscript
extends Node


func _ready() -> void:
	var t := Transform3D(Basis.IDENTITY, Vector3(0, 5, 0))
	print(t.origin)                         # (0, 5, 0)
	print(t * Vector3(1, 0, 0))             # (1, 5, 0)

	var turned := t.rotated(Vector3.UP, PI)     # Rotated around the Y axis
	var moved := t.translated(Vector3(1, 0, 0)) # Moved by an offset
	print(turned.basis.get_euler(), moved.origin)
```

In a 3D node:

```gdscript
extends Node3D


func _process(delta: float) -> void:
	rotate_y(delta)                       # Turn around the Y axis
	translate(Vector3(0, 0, -delta))      # Move along local -Z (forward)
	print(global_transform.origin)
	print(-global_transform.basis.z)      # The direction the node is facing
```

You will rarely build a matrix by hand. Use `position`, `rotation`, and helper methods for everyday work, and use transforms when converting between coordinate spaces or for precise control.

---

## 16.7 `Quaternion` and Rotations

Rotations in 3D can be stored in a few ways:

| Form | Description | Strength | Weakness |
| --- | --- | --- | --- |
| **Euler angles** (`rotation`) | Three angles (x, y, z) | Easy to read and edit | **Gimbal lock**, and the order of axes matters |
| **Basis** | A 3x3 matrix | Includes scale, easy to transform vectors | Uses more memory |
| **Quaternion** | Four numbers representing an axis and angle | No gimbal lock, **smooth interpolation** | Not readable by humans |

### Creating quaternions

```gdscript
extends Node


func _ready() -> void:
	var q1 := Quaternion(Vector3.UP, PI / 2)           # Axis and angle: 90 degrees around Y
	var q2 := Quaternion.from_euler(Vector3(0, PI, 0)) # From Euler angles
	var identity := Quaternion.IDENTITY                # No rotation

	print(q1 * Vector3.FORWARD)          # Rotate a vector with the quaternion
	print(q1.get_euler())                # Convert back to Euler angles
	print(q1.normalized())
```

### Combining and interpolating

Multiplying quaternions combines rotations (the right one is applied first). `slerp()` (spherical linear interpolation) blends smoothly between two rotations:

```gdscript
extends Node3D

var target := Quaternion(Vector3.UP, PI)


func _process(delta: float) -> void:
	var current := Quaternion(transform.basis)
	var smooth := current.slerp(target, 3.0 * delta)
	transform.basis = Basis(smooth)
```

### Rotation properties in nodes

```gdscript
extends Node3D


func _ready() -> void:
	rotation = Vector3(0, PI / 2, 0)                 # Euler angles, in radians
	rotation_degrees = Vector3(0, 90, 0)             # Euler angles, in degrees
	basis = Basis(Quaternion(Vector3.UP, PI / 2))    # From a quaternion
	quaternion = Quaternion(Vector3.UP, PI / 2)      # Directly set (Godot 4)

	rotate_y(0.5)                  # Rotate around the local... Y axis, by an amount in radians
	look_at(Vector3(0, 0, -10))    # Face a point (needs to be inside the tree)
```

### 2D rotation is simpler

In 2D, a rotation is just **one number** (radians), so no quaternion is needed:

```gdscript
extends Node2D


func _process(delta: float) -> void:
	rotation += delta                                  # Spin
	rotation = global_position.angle_to_point(get_global_mouse_position())   # Face the mouse
```

### Practical advice

- Use `rotation_degrees` in the Inspector and when editing by hand. Use radians in math.
- Do not add or subtract Euler angles to combine rotations in 3D. Use `rotate_*()` methods, basis multiplication, or quaternion multiplication.
- Use a quaternion (`slerp`) or `Basis.slerp()` for smooth rotation between two orientations.
- Convert: `deg_to_rad()` and `rad_to_deg()`.
- Basis, quaternion, and rotation work is used again for 3D movement in [Lesson 33](./[33]-3D-Basics.md).

---

[Previous](./[15]-Enums-and-Constants.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[17]-Static-Typing.md)