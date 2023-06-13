# LuaCoroutine

## Description
The LuaCoroutine class is used to create and manage Lua coroutines. Lua coroutines bound to the same Lua object share the same global table.
  
In contrast to the [LuaAPI](lua_api.md) class, any file or string you load into the state will not be immediately executed. Everytime the [resume](#resume) method is called, the Lua code will execute until its end is reached or until `yield` is called from Lua.  
  

Lua code running inside a LuaCoroutine has access to the `yield` function which can accept and return any number of arguments to GDScript through the [resume](#resume) function of the coroutine. Typically, you will use this function to pause the Lua code for as long as the user specifies in its arguments (see the [example](#example) below).

## Methods

### bind _void_ {#bind}

Binds the coroutine to a LuaAPI object. All coroutines attached to the same object share the same resources.

#### Parameters

| Parameters | Description |
| --- | --- |
| lua: [`LuaAPI`](lua_api.md) | The LuaAPI object to bind to. |

#### Returns

_void_

---

### load_file _[LuaError](lua_error.md)?_ {#load_file}

Loads a file by its **absolute path** into the coroutine's state.

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

Resumes or starts the coroutine. Will either return an Array of arguments passed by Lua through the yield() function or a [LuaError](lua_error.md).

#### Parameters

None

#### Returns

_Variant_

---

### is_done _bool_ {#is_done}

Returns `#!gdscript true` if the coroutine has finished executing.

#### Parameters

None

#### Returns

_bool_

---
### yield_await *Signal* {#yield_await}

!!! warning
	This method is still experimental and may cause issues.

This method attempts to allow you to hault execution in a GDScript method called by a lua coroutine.
It takes an array as an argument, which is then passed to `LuaCoroutine.resume()`.

#### Parameters

| Parameters                | Description                                          |
| ------------------------- | ---------------------------------------------------- |
| args: `Array`             | Array of arguments to be passed to LuaCoroutine.resume(). |

#### Returns

_Signal_

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI
var coroutine: LuaCoroutine

func wait_for_object():
    print("before")
    await coroutine.yield_await([get_tree().create_timer(3).timeout])
    print("after")
    return 1

func _ready():
    lua = LuaAPI.new()
    coroutine = lua.new_coroutine()
    coroutine.push_variant("WaitForObjectAsync", wait_for_object)
    coroutine.load_string("""
    print(WaitForObjectAsync())
    """)

var yieldTime = 0
var timeSince = 0
func _process(delta):
    timeSince += delta.
    if coroutine.is_done() || timeSince <= yieldTime:
        return
    var ret = coroutine.resume()
    if ret is LuaError:
        print("ERROR %d: " % ret.type + ret.msg)
        return
    if ret:
        if ret[0] is Signal:
            set_process(false)
            await ret[0]
            yieldTime = 0
            timeSince = 1
            set_process(true)
            return
        yieldTime = ret[0]
        timeSince = 0
```

---

### yield_state *[LuaError](lua_error.md)* {#yield_state}

!!! warning
	This method is still experimental and may cause issues.

#### Parameters

| Parameters             | Description                                  |
| ---------------------- | -------------------------------------------- |
| args: `Array`          | Arguments to be passed to [resume](#resume). |

Must be called from a lua hook. Will yield the lua state and pass arguments to [resume](#resume). Can be destructive.

#### Returns

*[LuaError](lua_error.md)*

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
var coroutine: LuaCoroutine

func _ready():
	lua = LuaAPI.new()
	lua.bind_libraries(["base"])
	coroutine = lua.new_coroutine()
	coroutine.load_file("user://file.lua")
	coroutine.resume()
	# Call the "test" Lua function with "Val1", 2 and true as arguments.
	coroutine.call_function("test", ["Val1", 2, true])
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
var coroutine: LuaCoroutine

func _ready():
	lua = LuaAPI.new()
	coroutine = lua.new_coroutine()
	coroutine.load_file("user://file.lua")
	coroutine.resume()

	# Check if the "test" Lua function is defined.
	if(coroutine.function_exists("test")):
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
var coroutine: LuaCoroutine

func _ready():
	lua = LuaAPI.new()
	coroutine = lua.new_coroutine()
	coroutine.load_string("myNumber = 10")
	coroutine.resume()

	# Read the value of myNumber.
	var my_number = coroutine.pull_variant("myNumber")
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
var coroutine: LuaCoroutine
var test = "Hello Lua!"

func _ready():
	lua = LuaAPI.new()
	coroutine = lua.new_coroutine()
	coroutine.push_variant("str", test)
	coroutine.load_string("print(str)")
	coroutine.resume()
```
``` title="Output"
Hello Lua!
```

---

## Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI
var coroutine: LuaCoroutine
	
func _ready():
	lua = LuaAPI.new()
	# Despite the name, this is not like a OS coroutine. It is a coroutine.
	coroutine = lua.new_coroutine()
	coroutine.load_string("
	while true do
		-- yield is exposed to Lua when the coroutine is bound.
		yield(1)
		print('Hello world!')
	end
	")
	
var yieldTime = 0
var timeSince = 0
func _process(delta):
	timeSince += delta
	# If the coroutine has finished executing or if not enough time has passed, do not resume the coroutine.
	if coroutine.is_done() || timeSince <= yieldTime:
		return
	# coroutine.resume will either return a LuaError or an Array.
	var ret = coroutine.resume()
	if ret is LuaError:
		print("ERROR %d: " % ret.type + ret.msg)
		return
	# Assumes the user will always pass the number of seconds to pause the coroutine for.
	yieldTime = ret[0]
	timeSince = 0
```

---

### set_hook _void_ {#set_hook}

Sets the hook for the state. The hook will be called on the events specified by the mask. The count specifies how many instructions should be executed before the hook is called. If count is 0, the hook will be called on every instruction. The hook will be called with the following arguments: `hook(parent, event, line)`. The parent is the LuaAPI object that owns the current state.

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
