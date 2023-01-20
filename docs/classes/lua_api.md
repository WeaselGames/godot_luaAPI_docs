# LuaAPI

## Description

The LuaAPI class is used to interact with Lua from GDScript.  
This class provides the methods to execute Lua code; to call Lua functions from GDScript; to read and write the value of global Lua variables; to create a Lua constructor for a GDScript class and more.

## Methods

### bind_libs _void_ {#bind_libs}

Takes an array of strings and binds the Lua state to each library. It is case insensitive.  
Please keep in mind that some libraries like IO and OS can be dangerous to use.

#### Parameters



| Parameters         | Description                                                                                                                                                               |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| libraries: `Array` | Array of all Lua libraries you would like to make available. The libraries are "base", "coroutine", "debug", "table", "string", "math", "io", "os", "utf8" and "package". |

#### Returns

_void_

#### Example

```gdscript
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	#All libraries are available. Use the OS and IO libraries at your own risk.
	lua.bind_libs(["base", "table", "string"])
```

---

### call_function *Variant* {#call_function}

Calls a global Lua function from GDscript.  
You can pass any number of arguments to the Lua function by adding them to the array parameter.

#### Parameters

| Parameters                | Description                                          |
| ------------------------- | ---------------------------------------------------- |
| luaFunctionName: `String` | Name of the Lua function to be invoked.              |
| args: `Array`             | Array of arguments to be passed to the Lua function. |

#### Returns

_Variant_

#### Example

```gdscript
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.do_file("user://file.lua")
	#Call the "test" Lua function with "Val1", 2 and true as arguments.
	lua.call_function("test", ["Val1", 2, true])
```

---

### do_file *LuaError* {#do_file}

Loads a file by its **absolute path** into the Lua state.

You might experience issues when loading a file in the "res://" directory after the game has been exported. In this case, it is recommended to load the file's contents as a string and use the do_string method instead.

#### Parameters

| Parameters         | Description                    |
| ------------------ | ------------------------------ |
| filePath: `String` | **Absolute** path to the file. |

#### Returns

_LuaError_

#### Example

```gdscript
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.do_file("user://luaFile.lua")
```

---

### do_string *LuaError* {#do_string}

Loads the content of a string into the Lua state, executing it.

#### Parameters

| Parameters        | Description                                  |
| ----------------- | -------------------------------------------- |
| content: `String` | A string containing Lua code to be executed. |

#### Returns

_LuaError_

#### Examples

```gdscript
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.do_string("for i=1,10,1 do print('Hello lua!') end")
```

---

### expose_constructor *LuaError* {#expose_constructor}

#### Parameters

| Parameters                | Description                         |
| ------------------------- | ----------------------------------- |
| gdscriptClass: `Object`   | A GDScript class.                   |
| constructorName: `String` | The name of the constructor in Lua. |

#### Returns

_LuaError_

#### Examples

```gdscript
extends Node2D
var lua: LuaAPI
class Player:
	var pos = Vector2(0, 0)
	#If lua_funcs is not defined or returns an empty array, all class methods will be available in Lua.
	func lua_funcs():
		return ["move_forward"]
	#lua_fields behaves similarly to lua_funcs but for fields.
	func lua_fields():
		return ["pos"]

	func move_forward():
		pos.x += 1

var player2: Player

func _ready():
	lua = LuaAPI.new()
	lua.expose_constructor(Player, "Player")
	lua.do_string("player = Player() player.move_forward() print(player.pos.x)")
	var player = lua.pull_variant("player")
	print(player.pos)
```

The string passed to _do_string_ is the following Lua code:

```lua
player = Player()
player.move_forward()
print(player.pos.x)
```

---

### function_exists *bool* {#function_exists}

Returns true if there is a Lua function with the provided name.

#### Parameters

| Parameters             | Description                            |
| ---------------------- | -------------------------------------- |
| functionName: `String` | The name of the Lua function to check. |

#### Returns

_bool_

#### Example

```gdscript
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.do_file("user://file.lua")
	#Assuming the code in file.lua defined a Lua function with the name "test", the following statement should print("yes").
	if(lua_function_exists("test")):
		print("yes")
	else:
		print("no")
```

---

### pull_variant *Variant* {#pull_variant}

Reads the value of a variable by its name from the Lua state.

#### Parameters

| Parameters             | Description                           |
| ---------------------- | ------------------------------------- |
| variableName: `String` | The name of the Lua variable to read. |

#### Returns

_Variant_

#### Example

```gdscript
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.expose_constructor(Player, "Player")
	lua.do_string("myNumber = 13")
	#Read the value of myNumber
	var my_number = lua.pull_variant("myNumber")
	print(my_number)
```

---

### push_variant *LuaError* {#push_variant}

Pushes a copy of a variant (`value`) to the Lua stack as the global variable `variableName`.

#### Parameters

| Parameters             | Description                           |
| ---------------------- | ------------------------------------- |
| value: `Variant`       | The value to be assigned.             |
| variableName: `String` | The name of the Lua variable to read. |

#### Returns

_LuaError_

#### Example

```gdscript
extends Node2D

var lua: LuaAPI
var test = "Hello lua!"

func _ready():
	lua = LuaAPI.new()
	lua.push_variant(test, "str")
	lua.do_string("print(str)")
```
