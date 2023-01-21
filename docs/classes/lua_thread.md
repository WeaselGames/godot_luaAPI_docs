# LuaThread

## Description
The confusingly named LuaThread class is used to create and manage Lua coroutines, not OS threads. Lua coroutines bound to the same Lua object share the same global variables.
  
In contrast to the [LuaAPI](lua_api.md) class, any file or string you load into the state will not be immediately executed. Everytime the [resume](#resume) method is called, the Lua code will execute until its end is reached or until `yield` is called from Lua.  
  

Lua code running inside a LuaThread has access to the `yield` function which can accept and return any number of arguments to GDScript through the [resume](#resume) function of the thread. Typically, you will use this function to pause the Lua code for as long as the user specifies in its arguments (see the [example](#example) below).

## Methods

### new_thread _LuaThread_ {#new_thread}

Static method that creates and binds a LuaThread object to a LuaAPI object. This method is equivalent to creating a new thread object and then calling its [bind](#bind) method.

#### Parameters

| Parameters | Description |
| --- | --- |
| lua: [`LuaAPI`](./LuaAPI) | The LuaAPI object to bind to. |

#### Returns

_LuaThread_

---

### bind _void_ {#bind}

Binds the thread to a LuaAPI object. All threads attached to the same object share the same resources.

#### Parameters

| Parameters | Description |
| --- | --- |
| lua: [`LuaAPI`](./LuaAPI) | The LuaAPI object to bind to. |

#### Returns

_void_

---

### load_file _[LuaError](lua_error.md)?_ {#load_file}

Loads a file by its **absolute path** into the thread's state.

!!! warning
	This method simply loads the contents of the file into the Lua state. It does **not** execute it.  
	You must use the [resume](#resume) method to execute the code that was loaded.

!!! warning
    You might experience issues when loading a file in the "res://" directory after the game has been exported. In this case, it is recommended to load the file's contents as a string and use the [load_string](#load_string) method instead.

#### Parameters

| Parameters | Description |
| --- | --- |
| filePath: `String` | Path to the file. |

#### Returns

*[LuaError](lua_error.md)* or `#!gdscript null` depending on whether there is an error or not.

---

### load_string _void_ {#load_string}

Loads the content of a string into the Lua state.

!!! warning
	This method simply loads a string of Lua code into the Lua state. It does **not** execute it.  
	You must use the [resume](#resume) method to execute the code that was loaded.

#### Parameters

| Parameters | Description |
| --- | --- |
| content: `String` | A string containing Lua code to be loaded. |

#### Returns

_void_

---

### resume _Variant_ {#resume}

Resumes or starts the thread. Will either return an Array of arguments passed by Lua through the yield() function or a [LuaError](lua_error.md).

#### Parameters

None

#### Returns

_Variant_

---

### is_done _bool_ {#is_done}

Returns true if the thread has finished executing.

#### Parameters

None

#### Returns

_bool_

---

### call_function *Variant* {#call_function}

!!! note
	This method is identical to [LuaAPI](lua_api.md)'s method with the same name.

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
var thread: LuaThread

func _ready():
	lua = LuaAPI.new()
	thread = LuaThread.new_thread(lua)
	thread.load_file("user://file.lua")
	thread.resume()
	# Call the "test" Lua function with "Val1", 2 and true as arguments.
	thread.call_function("test", ["Val1", 2, true])
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

### expose_constructor *[LuaError](lua_error.md)?* {#expose_constructor}

!!! note
	This method is identical to [LuaAPI](lua_api.md)'s method with the same name.

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
var thread: LuaThread

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
	thread = LuaThread.new_thread(lua)
	thread.expose_constructor("Player", Player)
	thread.load_string("player = Player() player.move_forward() print(player.pos.x)")
	thread.resume()
	var player = lua.pull_variant("player")
	print(player.pos)
```

The string passed to `thread.load_string` is the following Lua code:

```lua
player = Player()
player.move_forward()
print(player.pos.x)
```

---

### function_exists *bool* {#function_exists}

!!! note
	This method is identical to [LuaAPI](lua_api.md)'s method with the same name.

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
var thread: LuaThread

func _ready():
	lua = LuaAPI.new()
	thread = LuaThread.new_thread(lua)
	thread.load_file("user://file.lua")
	thread.resume()

	# Check if the "test" Lua function is defined.
	if(thread.function_exists("test")):
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

!!! note
	This method is identical to [LuaAPI](lua_api.md)'s method with the same name.

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
var thread: LuaThread

func _ready():
	lua = LuaAPI.new()
	thread = LuaThread.new_thread(lua)
	thread.load_string("myNumber = 10")
	thread.resume()

	# Read the value of myNumber.
	var my_number = thread.pull_variant("myNumber")
	print(my_number)
```
``` title="Output"
10
```

---

### push_variant *[LuaError](lua_error.md)?* {#push_variant}

!!! note
	This method is identical to [LuaAPI](lua_api.md)'s method with the same name.

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
var thread: LuaThread
var test = "Hello Lua!"

func _ready():
	lua = LuaAPI.new()
	thread = LuaThread.new_thread(lua)
	thread.push_variant("str", test)
	thread.load_string("print(str)")
	thread.resume()
```
``` title="Output"
Hello Lua!
```

---

## Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI
var thread: LuaThread
	
func _ready():
	lua = LuaAPI.new()
	# Despite the name, this is not like a OS thread. It is a coroutine.
	thread = LuaThread.new_thread(lua)
	thread.load_string("
	while true do
		-- yield is exposed to Lua when the thread is bound.
		yield(1)
		print('Hello world!')
	end
	")
	
var yieldTime = 0
var timeSince = 0
func _process(delta):
	timeSince += delta
	# If the thread has finished executing or if not enough time has passed, do not resume the thread.
	if thread.is_done() || timeSince <= yieldTime:
		return
	# thread.resume will either return a LuaError or an Array.
	var ret = thread.resume()
	if ret is LuaError:
		print("ERROR %d: " % ret.type + ret.msg)
		return
	# Assumes the user will always pass the number of seconds to pause the thread for.
	yieldTime = ret[0]
	timeSince = 0
```