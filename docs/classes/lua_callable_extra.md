# LuaCallableExtra

## Description
A wrapper for [Callables](https://docs.godotengine.org/en/4.0/classes/class_callable.html) used to create functions that accept either any amount of arguments from Lua as a single [LuaTuple](./LuaTuple) parameter or that accept a reference to the [LuaAPI](./LuaAPI) object from which the method was invoked, or both.

## Methods

### with_ref _LuaCallableExtra_ {#with_ref}

Static method that creates a LuaCallableExtra. A reference to the [LuaAPI](./LuaAPI) object that invoked the method will be passed as an argument.

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

Static method that creates a LuaCallableExtra. By specifying the number of expected arguments in this function, the module will be able to add all, if any, extra arguments to a [LuaTuple](./LuaTuple), which will always be the last parameter.

#### Parameters

| Parameters | Description |
| --- | --- |
| callable: [`Callable`](https://docs.godotengine.org/en/4.0/classes/class_callable.html) | The associated callable. |
| arg count: `number` | The number of expected arguments including the tuple one. |

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

### set_info _void_ {#bind}

Alternative way to configure a [LuaCallableExtra](.\LuaCallableExtra). This method is particularly useful for methods that expect both a [LuaTuple](./LuaTuple) and a reference to the [LuaAPI](./LuaAPI) object.

#### Parameters

| Parameters | Description |
| --- | --- |
| callable: [`Callable`](https://docs.godotengine.org/en/4.0/classes/class_callable.html) | The associated callable. |
| arg count: `number` | The number of expected arguments including the tuple one. |
| isTuple: `bool` | Wether or not the last parameter should be of the type [LuaTuple](./LuaTuple). |
| wantsRef: `bool` | Wether or not to pass a reference to [LuaAPI](./LuaAPI) object. |

#### Returns

_void_

---