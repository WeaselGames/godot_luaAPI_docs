# Lua

## Description

The Lua class is used to interact with Lua from GDScript.  

This class provides the methods to execute Lua code; to call Lua functions from GDScript; to read and write the value of global Lua variables and more.

!!! info
	The following Lua libraries are included by default: base, math, string and table.  
	You can learn more about the available libraries and their methods in the official [Lua manual](http://www.lua.org/manual/5.4/manual.html#6).

## Methods

### call_function *Variant* {#call_function}

Calls a global Lua function from GDscript.  

You can pass any number of arguments to the Lua function by adding them to the array parameter.

!!! warning
	You may check if the function exists first with the [lua_function_exists](#lua_function_exists) method.

!!! warning
	if you call this function with the `protectedCall` parameter set to `#!gdscript false` and an error occurs, the whole engine will crash!

#### Parameters

| Parameters                | Default Value 	| Description                                          						|
| ------------------------- | ----------------- |-------------------------------------------------------------------------- |
| luaFunctionName: `String` | None 				| Name of the Lua function to be invoked.              						|
| args: `Array`             | None 				| Array of arguments to be passed to the Lua function. 						|
| protectedCall: `bool`     | `#!gdscript true` | Whether or not the Lua function should be called with a protected call. 	|
| callbackCaller: `Object`  | `#!gdscript null` | The object that has the callback. 										|
| callback: `String`  		| `#!gdscript ""` 	| The name of the callback which will handle any errors. 					|

#### Returns

_Variant_

#### Example
	
```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func lua_call_back(err):
	# This function will be called if there is a Lua error.
	print(err)

func _ready():
	lua.do_file("user://luaFile.lua")
	if( lua.lua_function_exists("set_colours") ):
		# call_function will return a Variant if Lua returns nothing the value will be null
		var value = lua.call_function( "set_colours", ["red", "blue"], true, self, "lua_call_back")
		if value != null:
			print(value)
		else:
			print("no value returned")
```

---

### do_file *void* {#do_file}

Loads the content of a file by its **absolute path** into the Lua state by passing it to [do_string](#do_string).

!!! warning
    You might experience issues when loading a file in the "res://" directory after the game has been exported. In this case, it is recommended to load the file's contents as a string and use the [do_string](#do_string) method instead.

!!! warning
	if you call this function with the `protectedCall` parameter set to `#!gdscript false` and an error occurs, the whole engine will crash!

#### Parameters

| Parameters         		| Default Value 	| Description                    |
| ------------------------- | ----------------- | ------------------------------ |
| filePath: `String` 		| None			 	| **Absolute** path to the file. |
| protectedCall: `bool`     | `#!gdscript true` | Whether or not the Lua function should be called with a protected call. 	|
| callbackCaller: `Object`  | `#!gdscript null` | The object that has the callback. 										|
| callback: `String`  		| `#!gdscript ""` 	| The name of the callback which will handle any errors. 					|

#### Returns

*void*

#### Example

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.do_file("user://luaFile.lua")
```

---

### do_string *void* {#do_string}

Loads the content of a string into the Lua state, executing it.

!!! warning
	if you call this function with the `protectedCall` parameter set to `#!gdscript false` and an error occurs, the whole engine will crash!

#### Parameters

| Parameters         		| Default Value 	| Description                    |
| ------------------------- | ----------------- | ------------------------------ |
| content: `String` 		| None 				| A string containing Lua code to be executed. 								|
| protectedCall: `bool`     | `#!gdscript true` | Whether or not the Lua function should be called with a protected call. 	|
| callbackCaller: `Object`  | `#!gdscript null` | The object that has the callback. 										|
| callback: `String`  		| `#!gdscript ""` 	| The name of the callback which will handle any errors. 					|

#### Returns

*void*

#### Examples

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.do_string("for i=1,10,1 do print('Hello lua!') end")
```

---

### expose_function *void* {#expose_function}

Defines a Lua function with name `luaFunctionName`. When Lua code calls it, it will call the `gdFunction` method of `nodeObject`, so make sure the object still exists at this moment.

!!! warning
	Make sure that the `nodeObject` still exists and has not been freed by the time the `gdFunction` is called.

#### Parameters

| Parameters         			| Description                    |
| ------------------------- 	| ------------------------------ |
| nodeObject: `Object` 			| The object which has the callback. 		|
| gdFunction: `String`     		| The name of the GDScript callback. 					|
| luaFunctionName: `String`  	| The name which the Lua function will take. 		|

#### Returns

*void*

#### Examples

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func lua_add(a, b):
	return a + b

func _ready():
	lua.expose_function(self, "lua_add", "add")
	lua.do_string("print(add(2, 4))")
```
``` title="Output"
6
```

---

### kill_all *void* {#kill_all}

Kills all current Lua threads in execution.

#### Parameters

None

#### Returns

*void*

#### Example

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.set_threaded(true)
	lua.do_string("while true do pass end")
	lua.kill_all()
```

---

### lua_function_exists *bool* {#lua_function_exists}

Returns `#!gdscript true` if there is a Lua function with the provided name.

#### Parameters

| Parameters             	| Description                            |
| ---------------------- 	| -------------------------------------- |
| luaFunctionName: `String` | The name of the Lua function to check. |

#### Returns

_bool_

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

var lua: Lua

func _ready():
	lua = Lua.new()
	lua.do_string("myNumber = 10")

	# Read the value of myNumber.
	var my_number = lua.pull_variant("myNumber")
	print(my_number)
```
``` title="Output"
10
```

---

### push_variant *bool* {#push_variant}

Pushes a copy of a variant (`value`) to the Lua stack as the global variable `variableName`.

#### Parameters

| Parameters             | Description                           |
| ---------------------- | ------------------------------------- |
| variableName: `String` | The name of the Lua variable to write to. |
| value: `Variant`       | The value to be written.             |


#### Returns

*bool*

#### Example

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()
var gdscript_string = "Hello lua!"

func _ready():
	lua.push_variant(gdscript_string, "myString")
	lua.do_string("print(myString)")
```
``` title="Output"
Hello Lua!
```

---

### set_threaded *void* {#set_threaded}

Set whether Lua runs on a separate thread. By default, Lua runs on the same thread.

#### Parameters

| Parameters				  | Description                           |
| --------------------------- | ------------------------------------- |
| runOnSeparateThread: `bool` | Whether Lua should run on a separate thread or not. |

#### Returns

*void*

#### Example

```gdscript linenums="1"
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.set_threaded(true)
	lua.do_string("while true do print("The game will not freeze") end" )
```

---