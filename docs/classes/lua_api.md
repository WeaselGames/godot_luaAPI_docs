# LuaAPI

## Description

The LuaAPI class is used to interact with Lua from GDScript.  

This class provides the methods to execute Lua code; to call Lua functions from GDScript; to read and write the value of global Lua variables; to create a Lua constructor for a GDScript class and more.

## Methods

### bind_libraries _void_ {#bind_libraries}

Takes an array of strings and binds the Lua state to each library. It is case insensitive.  

!!! danger
	The IO, Package and OS libraries have some dangerous methods such as `os.execute`, `require` or `io.write`. Make these libraries available to the end user at your own risk.

!!! info
	You can learn more about the available libraries and their methods in the official [Lua manual](http://www.lua.org/manual/5.4/manual.html#6).

#### Parameters

| Parameters         | Description                                                                                                                                                               |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| libraries: `Array` | Array of all Lua libraries you would like to make available. The libraries are "base", "coroutine", "debug", "table", "string", "math", "io", "os", "utf8" and "package". |

#### Returns

_void_

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	# All libraries are available.
	# Use the OS and IO libraries at your own risk.
	lua.bind_libraries(["base", "table", "string"])
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
	
```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.bind_libraries(["base"])
	lua.do_file("user://file.lua")

	# Call the "test" Lua function with "Val1", 2 and true as arguments.
	lua.call_function("test", ["Val1", 2, true])
```
??? example "Example: user://file.lua"
	```lua title="user://file.lua"
	function test(myString, myNumber, myBoolean)
		print("I got "..myString..", "..tostring(myNumber)..", "..tostring(myBoolean))
	end
	```
	``` title="Output"
	I got Val1, 2, true
	```

---

### do_file *[LuaError](lua_error.md)?* {#do_file}

Loads a file by its **absolute path** into the Lua state.

!!! warning
    You might experience issues when loading a file in the "res://" directory after the game has been exported. In this case, it is recommended to load the file's contents as a string and use the [do_string](#do_string) method instead.

#### Parameters

| Parameters         | Description                    |
| ------------------ | ------------------------------ |
| filePath: `String` | **Absolute** path to the file. |

#### Returns

*[LuaError](lua_error.md)* or `#!gdscript null` depending on whether there is an error or not.

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.do_file("user://luaFile.lua")
```

---

### do_string *[LuaError](lua_error.md)?* {#do_string}

Loads the content of a string into the Lua state, executing it.

#### Parameters

| Parameters        | Description                                  |
| ----------------- | -------------------------------------------- |
| content: `String` | A string containing Lua code to be executed. |

#### Returns

*[LuaError](lua_error.md)* or `#!gdscript null` depending on whether there is an error or not.

#### Examples

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.do_string("for i=1,10,1 do print('Hello lua!') end")
```

---

### expose_constructor *[LuaError](lua_error.md)?* {#expose_constructor}

Exposes the constructor of a class to Lua as a global method.

#### Parameters

| Parameters                | Description                         |
| ------------------------- | ----------------------------------- |
| constructorName: `String` | The name of the constructor in Lua. |
| gdscriptClass: `Object`   | A GDScript class or any Object that can be created with the `.new` method.                   |


#### Returns

*[LuaError](lua_error.md)* or `#!gdscript null` depending on whether there is an error or not.

#### Examples

```gdscript linenums="1"
extends Node2D
var lua: LuaAPI

class Player:
	var pos = Vector2(0, 0)
	# If lua_funcs is not defined or returns an empty array, all class methods will be available in Lua.
	func lua_funcs():
		return ["move_forward"]
	# lua_fields behaves similarly to lua_funcs but for fields.
	func lua_fields():
		return ["pos"]

	func move_forward():
		pos.x += 1

func _ready():
	lua = LuaAPI.new()
	lua.expose_constructor("Player", Player)
	lua.do_string("player = Player() player.move_forward() print(player.pos.x)")
	var player = lua.pull_variant("player")
	print(player.pos)
```

The string passed to `lua.do_string` is the following Lua code:

```lua
player = Player()
player.move_forward()
print(player.pos.x)
```

---

### function_exists *bool* {#function_exists}

Returns `#!gdscript true` if there is a Lua function with the provided name.

#### Parameters

| Parameters             | Description                            |
| ---------------------- | -------------------------------------- |
| functionName: `String` | The name of the Lua function to check. |

#### Returns

_bool_

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.do_file("user://file.lua")

	# Check if the "test" Lua function is defined.
	if(lua.function_exists("test")):
		print("yes")
	else:
		print("no")
```

??? example "Example: user://file.lua"
	```lua title="user://file.lua"
	-- The "test" function is defined in Lua.
	function test()
		-- Code here...
	end
	```
	``` title="Output"
	yes
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

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.do_string("myNumber = 10")

	# Read the value of myNumber.
	var my_number = lua.pull_variant("myNumber")
	print(my_number)
```
``` title="Output"
10
```

---

### push_variant *[LuaError](lua_error.md)?* {#push_variant}

Pushes a copy of a variant (`value`) to the Lua stack as the global variable `variableName`.

#### Parameters

| Parameters             | Description                           |
| ---------------------- | ------------------------------------- |
| variableName: `String` | The name of the Lua variable to write to. |
| value: `Variant`       | The value to be written.             |


#### Returns

*[LuaError](lua_error.md)* or `#!gdscript null` depending on whether there is an error or not.

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI
var test = "Hello Lua!"

func _ready():
	lua = LuaAPI.new()
	lua.push_variant("str", test)
	lua.do_string("print(str)")
```
``` title="Output"
Hello Lua!
```

---

### set_hook *[LuaCoroutine](lua_coroutine.md)* {#do_file}

Sets the hook for the state. The hook will be called on the events specified by the mask. The count specifies how many instructions should be executed before the hook is called. If count is 0, the hook will be called on every instruction. The hook will be called with the following arguments: [code]hook(parent, event, line)[/code]. The parent is the LuaAPI object that owns the current state.

This is useful for preventing infinite loops, but it should be kept in mind there will be a large performance tax.

#### Parameters

| Parameters             | Description                                                         |
| ---------------------- | ------------------------------------------------------------------- |
| hook: `Callable`       | The function to be called as a hook.                                |
| mask: `int`            | The hook even mask.                                                 |
| count: `int`           | Specifies how many instructions should be executed before the hook. |

#### Returns

_void_

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI
var coroutine: LuaCoroutine

func _lua_hook(lua: LuaAPI, event: int, line: int):
	var co: LuaCoroutine = lua.get_running_coroutine()
	co.yield_state([1])

func _ready():
	lua = LuaAPI.new()
	# Despite the name, this is not like a OS coroutine. It is a coroutine.
	coroutine = lua.new_coroutine()
	coroutine.set_hook(_lua_hook, lua.HOOK_MASK_COUNT, 8)
	coroutine.load_string("
	for i=1,2,1 do
		print('Hello world!')
	end
	")

var yieldTime = 0
var timeSince = 0
var goodBye = false
func _process(delta):
	
	timeSince += delta
	# If the coroutine has finished executing or if not enough time has passed, do not resume the coroutine.
	if coroutine.is_done() || timeSince <= yieldTime:
		if !goodBye:
			lua.do_string("""
			for i = 0,4,1 do 
				print('Good Bye World!')
			end
			""")
			goodBye=true
		return
	goodBye=false
	# coroutine.resume will either return a LuaError or an Array.
	var ret = coroutine.resume()
	if ret is LuaError:
		print("ERROR %d: " % ret.type + ret.message)
		return
	if coroutine.is_done():
		return
	
	yieldTime = ret[0]
	timeSince = 0
```
``` title="Output"
Hello world!
Good Bye World!
Good Bye World!
Good Bye World!
Good Bye World!
Good Bye World!
Hello world!
Good Bye World!
Good Bye World!
Good Bye World!
Good Bye World!
Good Bye World!
```

---

### new_coroutine *[LuaCoroutine](lua_coroutine.md)* {#new_coroutine}

Creates and binds a LuaCoroutine object to a LuaAPI object. This method is equivalent to creating a new coroutine object and then calling its [bind](#bind) method.

#### Returns

*[LuaCoroutine](lua_coroutine.md)*

---

### get_running_coroutine *[LuaCoroutine](lua_coroutine.md)* {#do_file}

Intended to be called from a lua hook. Returns the current running coroutine.

#### Returns

*[LuaCoroutine](lua_coroutine.md)*