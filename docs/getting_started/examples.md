# Examples

### Running Lua code from a string

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.do_string("for i=1,10,1 do print('Hello lua!') end")
```

---

### Running Lua code from a file

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.do_file("user://luaFile.lua")
```

---

### Pushing a Variant as a global

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()
var test = "Hello lua!"

func _ready():
	lua.push_variant(test, "str")
	lua.do_string("print(str)")
```

---

### Exposing a GDScript function to Lua

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func lua_add(a, b):
	return a + b

func _ready():
	lua.expose_function(self, "lua_add", "add")
	lua.do_string("print(add(2, 4))")
```

---

### Calling a Lua function from GDScript

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.do_file("user://luaFile.lua")
	if( lua.lua_function_exists("set_colours") ):
		# call_function will return a Variant if lua returns nothing the value will be null
		var value = lua.call_function( "set_colours", ["red", "blue"])
		if value != null:
			print(value)
		else:
			print("no value returned")	
```

---

### Capturing Lua errors

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func luaCallBack(err):
	print(err)

func _ready():
	lua.do_string("print(This wont work)", true , self, "luaCallBack")
```

---

### Enable threading
```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()


func _ready():
	lua.set_threaded(true)
	lua.do_string("while true do print("The game will not freeze") end" )
```

---

### Kill all Lua threads
```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()


func _ready():
	lua.do_string("while true do pass end")
	lua.kill_all()
```