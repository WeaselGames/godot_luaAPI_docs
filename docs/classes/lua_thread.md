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

### load_file _[LuaError](lua_error.md)_ {#load_file}

Loads a file by its **absolute path** into the thread's state.

!!! warning
    You might experience issues when loading a file in the "res://" directory after the game has been exported. In this case, it is recommended to load the file's contents as a string and use the [load_string](#load_string) method instead.

#### Parameters

| Parameters | Description |
| --- | --- |
| filePath: `String` | path to the file. |

#### Returns

_[LuaError](lua_error.md)_

---

### load_string _void_ {#load_string}

#### Parameters

| Parameters | Description |
| --- | --- |
| content: `String` | A string containing Lua code to be executed. |

#### Returns

_void_

---

### resume _Variant_ {#resume}

Resumes or starts the thread. Will either return an Array of arguments passed by Lua through the yield() function or a LuaError.

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

## Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI
var thread: LuaThread
	
func _ready():
	lua = LuaAPI.new()
	## Despite the name, this is not like a OS thread. It is a coroutine.
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
	## If the thread has finished executing or if not enough time has passed, do not resume the thread.
	if thread.is_done() || timeSince <= yieldTime:
		return
	## thread.resume will either return a LuaError or an Array.
	var ret = thread.resume()
	if ret is LuaError:
		print("ERROR %d: " % ret.type + ret.msg)
		return
	## Assumes the user will always pass the number of seconds to pause the thread for.
	yieldTime = ret[0]
	timeSince = 0
```