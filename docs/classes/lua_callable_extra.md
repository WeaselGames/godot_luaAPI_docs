# LuaCallableExtra

## Description
A wrapper for [Callables](https://docs.godotengine.org/en/4.0/classes/class_callable.html) used to create functions that accept either any amount of arguments from Lua as a single [LuaTuple](lua_tuple.md) parameter or that accept a reference to the [LuaAPI](lua_api.md) object from which the method was invoked, or both.

## Properties
### argc _number_
An integer representing the number of expected arguments.

---

### tuple _bool_
Wether or not the last parameter should be of the type [LuaTuple](lua_tuple.md).

---

### wants_ref _bool_
Wether or not the method expects a reference to the [LuaAPI](lua_api.md) object that invoked it.

---

## Methods

### with_ref _LuaCallableExtra_ {#with_ref}

Static method that creates a LuaCallableExtra. A reference to the [LuaAPI](lua_api.md) object that invoked the method will be passed as an argument to the callable.

#### Parameters

| Parameters | Description |
| --- | --- |
| callable: [`Callable`](https://docs.godotengine.org/en/4.0/classes/class_callable.html) | The associated callable. |

#### Returns

_LuaCallableExtra_

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.push_variant("myFunction", LuaCallableExtra.with_ref(my_function))
	lua.do_string("myFunction()")
	
func my_function(lua_object: LuaAPI):
	print("I was invoked by ", lua_object)
```
``` title="Example Output"
I was invoked by <LuaAPI#-9223372010447437397>
```

---

### with_tuple _LuaCallableExtra_ {#with_tuple}

Static method that creates a LuaCallableExtra. A reference to the [LuaAPI](lua_api.md) object that invoked the method will be passed as an argument to the callable. By specifying the number of expected arguments in this function, the module will be able to add all, if any, extra arguments to a [LuaTuple](lua_tuple.md), which will always be the last parameter.

#### Parameters

| Parameters | Description |
| --- | --- |
| callable: [`Callable`](https://docs.godotengine.org/en/4.0/classes/class_callable.html) | The associated callable. |
| arg count: `number` | The number of expected arguments excluding the tuple one. |

#### Returns

_LuaCallableExtra_

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.push_variant(
		"somator",
		LuaCallableExtra.with_tuple(my_somator_function, 1)
	)
	lua.do_string("somator(1, 2, 3, 4)")
	
func my_somator_function(args: LuaTuple):
	var sum := 0
	for index in range(0, args.size()):
		sum += args.get_value(index)
	print(sum)
	return sum
```
``` title="Output"
10
```

---

### with_ref_and_tuple _LuaCallableExtra_ {#with_ref_and_tuple}

Static method that creates a LuaCallableExtra. By specifying the number of expected arguments in this function, the module will be able to add all, if any, extra arguments to a [LuaTuple](lua_tuple.md), which will always be the last parameter.

!!! note
	This method is similar to [with_ref](#with_ref) and [with_tuple](#with_tuple) combined.

#### Parameters

| Parameters | Description |
| --- | --- |
| callable: [`Callable`](https://docs.godotengine.org/en/4.0/classes/class_callable.html) | The associated callable. |
| arg count: `number` | The number of expected arguments excluding the tuple one. |

#### Returns

_LuaCallableExtra_

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()

	# This will replace the built-in print function with ours.
	lua.push_variant("print", LuaCallableExtra.with_ref_and_tuple(custom_print_function, 0))
    
	lua.do_string("""
		print('Hello!', ' How are you?', ' I havent seen you in ', 10, ' days.')
	""")
	
func custom_print_function(lua_object: LuaAPI, args: LuaTuple):
	print(args.to_array(), " -- ", lua_object)
```

---

### set_info _void_ {#bind}

Alternative way to configure a [LuaCallableExtra](lua_callable_extra.md). This method is particularly useful for methods that expect both a [LuaTuple](lua_tuple.md) and a reference to the [LuaAPI](lua_api.md) object.

#### Parameters

| Parameters | Description |
| --- | --- |
| callable: [`Callable`](https://docs.godotengine.org/en/4.0/classes/class_callable.html) | The associated callable. |
| arg count: `number` | The number of expected arguments excluding the tuple one. |
| isTuple: `bool` | Wether or not the last parameter should be of the type [LuaTuple](lua_tuple.md). |
| wantsRef: `bool` | Wether or not to pass a reference to the [LuaAPI](lua_api.md) object. |

#### Returns

_void_

#### Example

```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()

	# Wrap custom_print_function with a LuaCallableExtra.
	var luaCallableExtra = LuaCallableExtra.new()
	luaCallableExtra.set_info(custom_print_function, 0, true, true)

	# This will replace the built-in print function with ours.
	lua.push_variant("print", luaCallableExtra)
    
	lua.do_string("""
		print('Hello!', ' How are you?', ' I havent seen you in ', 10, ' days.')
	""")
	
func custom_print_function(lua_object: LuaAPI, args: LuaTuple):
	print(args.to_array(), " -- ", lua_object)
```

---