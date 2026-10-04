[Previous](./[18]-Classes-and-Scripts.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[20]-Properties-Setters-and-Getters.md)

*Types and Classes*

# Lesson 19 - Inheritance and Polymorphism

Many things in a game are *kinds of* other things: a bat is a kind of enemy, an enemy is a kind of character. **Inheritance** lets a class start with everything a parent class has and then add or change only what is different. **Polymorphism** lets you treat all those related objects the same way while each one still behaves in its own way. This lesson shows how both work in GDScript, and when to choose a different tool.

---

## 19.1 Extending Classes

You have used inheritance since [Lesson 3](./[3]-Running-GDScript-Code.md): `extends Node2D` means "my class **is a** `Node2D` and has everything a `Node2D` has". You can extend your own classes in the same way.

```gdscript
# animal.gd
class_name Animal
extends RefCounted

var animal_name: String
var energy: int = 10


func _init(new_name: String = "Animal") -> void:
	animal_name = new_name


func speak() -> String:
	return "..."


func describe() -> String:
	return "%s says %s" % [animal_name, speak()]
```

```gdscript
# dog.gd
class_name Dog
extends Animal


func fetch() -> String:
	return "%s fetches the stick" % animal_name
```

`Dog` has not declared `animal_name`, `energy`, `speak()`, or `describe()`, yet it has all of them because it **inherited** them from `Animal`:

```gdscript
extends Node


func _ready() -> void:
	var rex := Dog.new("Rex")
	print(rex.describe())     # Rex says ...
	print(rex.fetch())        # Rex fetches the stick
	print(rex.energy)         # 10
```

Vocabulary:

| Term | Meaning | Example |
| --- | --- | --- |
| **Parent class** (base, superclass) | The class being extended | `Animal` |
| **Child class** (derived, subclass) | The class that extends it | `Dog` |
| **Inheritance chain** | Parent, grandparent, and so on up to the engine | `Dog` -> `Animal` -> `RefCounted` -> `Object` |

### Ways to name the parent

```gdscript
extends Animal                    # By class_name (the most common)
extends Node2D                    # An engine class
extends "res://animal.gd"         # By file path (when the parent has no class_name)
```

### Rules and limits

- A class has **exactly one parent**. GDScript has no multiple inheritance.
- A child gets all of its parent's variables, functions, signals, constants, and enums.
- A child **cannot redeclare** a member variable with the same name as one in its parent. That is an error. Choose a different name, or set the inherited variable's value in `_init()`.
- GDScript has no `private` or `protected` keywords. A child can freely use everything its parent has, including names that start with `_`.

Because every class is built on the one before it, an `is` check matches the whole chain:

```gdscript
extends Node


func _ready() -> void:
	var rex := Dog.new("Rex")
	print(rex is Dog)        # true
	print(rex is Animal)     # true  (a Dog is an Animal)
	print(rex is RefCounted) # true
	print(rex is Node)       # false
```

---

## 19.2 Overriding Methods and Calling `super`

A child class can **override** a method by defining a function with the **same name**. Godot then uses the child's version for objects of the child class.

```gdscript
# dog.gd
class_name Dog
extends Animal


func speak() -> String:
	return "Woof!"
```

```gdscript
extends Node


func _ready() -> void:
	var generic := Animal.new("Thing")
	var rex := Dog.new("Rex")
	print(generic.describe())   # Thing says ...
	print(rex.describe())       # Rex says Woof!
```

Look closely at the last line. `describe()` is written **only** in `Animal`, but it calls `speak()`, and for a `Dog` object, `speak()` runs the `Dog` version. The parent's code ends up using the child's behavior automatically.

### Calling the parent's version with `super`

Sometimes you want to **add to** the parent's behavior instead of replacing it. Inside a method, **`super()`** calls the parent's version of **the same method**:

```gdscript
# puppy.gd
class_name Puppy
extends Dog


func speak() -> String:
	return super() + " (squeaky)"
```

```gdscript
extends Node


func _ready() -> void:
	var pup := Puppy.new("Bit")
	print(pup.describe())   # Bit says Woof! (squeaky)
```

`super()` inside `Puppy.speak()` runs `Dog.speak()`, which returns `"Woof!"`.

You can also call a **different** parent method by name with `super.method_name()`:

```gdscript
# puppy.gd (excerpt)
func report() -> String:
	return super.describe()    # Calls Dog's/Animal's describe(), ignoring any override here
```

### Constructors and `super`

When you create a child object, the parent's `_init()` runs too:

- If the parent's `_init()` can be called with **no arguments** (it has none, or all have defaults), Godot calls it for you automatically.
- If the parent's `_init()` has **required** parameters, the child's `_init()` must call `super(...)` with them.

```gdscript
# vehicle.gd
class_name Vehicle
extends RefCounted

var wheels: int


func _init(wheel_count: int) -> void:     # Required parameter
	wheels = wheel_count
```

```gdscript
# bicycle.gd
class_name Bicycle
extends Vehicle


func _init() -> void:
	super(2)                               # Must pass the argument up to Vehicle
```

```gdscript
extends Node


func _ready() -> void:
	print(Bicycle.new().wheels)    # 2
```

### Overriding engine callbacks

Methods such as `_ready()` and `_process()` are also overridable, and **the engine only calls the most-derived version**. If a parent script has logic in `_ready()`, the child must call `super()` or that logic is skipped:

```gdscript
# base_enemy.gd
class_name BaseEnemy
extends Node2D


func _ready() -> void:
	print("BaseEnemy: setting up health bar")
```

```gdscript
# bat.gd
class_name Bat
extends BaseEnemy


func _ready() -> void:
	super()                              # Run the parent's setup first
	print("Bat: setting up wings")
```

Output when a `Bat` enters the scene:

```
BaseEnemy: setting up health bar
Bat: setting up wings
```

Without `super()`, only the `Bat` line would print. Forgetting `super()` in `_ready()` is one of the most common inheritance bugs.

### Matching signatures

An override should keep the **same parameters and return type** as the parent's method. If the types do not match, GDScript reports an error or warning, because code written for the parent would break.

---

## 19.3 Polymorphism

**Polymorphism** means "many forms". It lets you write code that works with the **parent type**, while each object does the right thing for its **own** type.

```gdscript
# cat.gd
class_name Cat
extends Animal


func speak() -> String:
	return "Meow!"
```

```gdscript
extends Node


func _ready() -> void:
	var animals: Array[Animal] = [
		Dog.new("Rex"),
		Cat.new("Tom"),
		Puppy.new("Bit"),
		Animal.new("Thing"),
	]

	for animal in animals:
		print(animal.describe())
```

Output:

```
Rex says Woof!
Tom says Meow!
Bit says Woof! (squeaky)
Thing says ...
```

The loop knows only that each item is an `Animal`. It does not check for dogs or cats, yet every animal responds differently. If you invent a `Cow` class next month, this loop works for it **without any change**.

This is the main benefit of inheritance. Compare it to a version without it:

```gdscript
extends Node


# Hard to maintain: every new animal needs a new branch here
func describe_bad(kind: String) -> String:
	if kind == "dog":
		return "Woof!"
	elif kind == "cat":
		return "Meow!"
	return "..."
```

### Polymorphism in Godot's own classes

You use it all the time. A function that takes a `Node2D` accepts a `Sprite2D`, a `CharacterBody2D`, or any other 2D node:

```gdscript
extends Node


func move_right(thing: Node2D, distance: float) -> void:
	thing.position.x += distance


func _ready() -> void:
	var sprite := Sprite2D.new()
	var body := CharacterBody2D.new()
	move_right(sprite, 10.0)
	move_right(body, 10.0)
	print(sprite.position, " ", body.position)    # (10, 0) (10, 0)
	sprite.free()
	body.free()
```

### Type checks and casts in a hierarchy

When you need to treat an object as a more specific type, use `is` and `as` ([Lesson 17](./[17]-Static-Typing.md)):

```gdscript
extends Node


func inspect(animal: Animal) -> void:
	if animal is Dog:
		var dog := animal as Dog
		print(dog.fetch())
	else:
		print(animal.animal_name, " cannot fetch")


func _ready() -> void:
	inspect(Dog.new("Rex"))    # Rex fetches the stick
	inspect(Cat.new("Tom"))    # Tom cannot fetch
```

If you find yourself writing many `is` checks, consider adding the behavior as an overridable method on the parent instead.

### Duck typing

GDScript is also happy to work **without** a shared parent. "If it walks like a duck..." means: do not ask what the object *is*, ask what it *can do*:

```gdscript
extends Node


func hit(target: Object) -> void:
	if target.has_method("take_damage"):
		target.take_damage(5)
	else:
		print("Cannot damage that")
```

`has_method()` checks by name at runtime. This is flexible, but the editor cannot check it for you, so prefer a shared parent class when the types are under your control. For checking properties, use `"name" in object` ([Lesson 7](./[7]-Operators-and-Expressions.md)).

---

## 19.4 Base Classes that Must Be Overridden

Sometimes a parent class describes *what* must exist but cannot say *how*. A `Shape` knows every shape has an area but cannot compute it without knowing which shape it is. In other languages such a class is called **abstract**.

### The traditional pattern

For most Godot 4 versions, the usual approach is a method that reports a clear error when it is not overridden:

```gdscript
# shape.gd
class_name Shape
extends RefCounted


func area() -> float:
	push_error("Shape.area() must be overridden in a child class")
	return 0.0


func describe() -> String:
	return "%s with area %.2f" % [get_shape_name(), area()]


func get_shape_name() -> String:
	return "Shape"
```

```gdscript
# circle.gd
class_name Circle
extends Shape

var radius: float


func _init(new_radius: float = 1.0) -> void:
	radius = new_radius


func area() -> float:
	return PI * radius * radius


func get_shape_name() -> String:
	return "Circle"
```

```gdscript
# rectangle.gd
class_name Rectangle
extends Shape

var width: float
var height: float


func _init(new_width: float = 1.0, new_height: float = 1.0) -> void:
	width = new_width
	height = new_height


func area() -> float:
	return width * height


func get_shape_name() -> String:
	return "Rectangle"
```

```gdscript
extends Node


func _ready() -> void:
	var shapes: Array[Shape] = [Circle.new(2.0), Rectangle.new(3.0, 4.0)]
	for shape in shapes:
		print(shape.describe())
```

Output:

```
Circle with area 12.57
Rectangle with area 12.00
```

### Built-in abstract support

Newer versions of Godot (**4.5 and later**) add an `@abstract` annotation, so the engine itself can refuse to create an instance of a base class and can require children to implement certain methods. Check the **built-in documentation** (`F1`, [Lesson 2](./[2]-Editor-Tour.md)) for your version before relying on it. If you must support older 4.x versions, use the pattern above.

---

## 19.5 Composition: "Has a" vs "Is a"

Inheritance describes an **is a** relationship (a `Dog` *is an* `Animal`). Another relationship is **has a** (a `Player` *has* health). Modeling "has a" with inheritance leads to fragile, tangled trees:

- A flying enemy that can also swim needs two parents, which GDScript does not allow.
- A change in a high-level parent can break every child below it.
- Deep chains (`A` -> `B` -> `C` -> `D` -> `E`) are hard to read, because behavior is spread across many files.

**Composition** builds objects by giving them **parts**. Godot is designed around this: scenes are made of nodes, and each node does one job. A reusable behavior becomes its own small node that you add as a child:

```gdscript
# health_component.gd
class_name HealthComponent
extends Node

signal died

@export var max_health: int = 10

var health: int


func _ready() -> void:
	health = max_health


func take_damage(amount: int) -> void:
	health = maxi(health - amount, 0)
	if health == 0:
		died.emit()
```

```
Slime (CharacterBody2D)
├── Sprite2D
├── CollisionShape2D
└── HealthComponent      <- reusable part
```

```gdscript
# slime.gd
extends CharacterBody2D

@onready var health_component: HealthComponent = $HealthComponent


func _ready() -> void:
	health_component.died.connect(queue_free)


func hit_by_player() -> void:
	health_component.take_damage(3)
```

The same `HealthComponent` can be dropped onto a slime, a crate, or the player, with no shared parent class. Signals ([Lesson 26](./[26]-Signals.md)) let the parts report events to their owner.

### Which should I use?

| Situation | Prefer |
| --- | --- |
| Objects are genuinely specialized versions of one thing (many enemy types sharing movement code) | **Inheritance** |
| A behavior is optional or shared by unrelated things (health, inventory, hitbox) | **Composition** (child nodes or resources) |
| The inheritance chain is getting deeper than two or three levels | Rethink with **composition** |
| You just need to attach data | A custom `Resource` ([Lesson 23](./[23]-Custom-Resources.md)) |

A helpful rule: **use inheritance to share what something *is*, and composition to share what something *does*.** Many good designs use both: a short inheritance chain for the core type, and components for optional abilities.

---

[Previous](./[18]-Classes-and-Scripts.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[20]-Properties-Setters-and-Getters.md)