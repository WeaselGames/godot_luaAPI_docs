# LuaTuple

## Description

The LuaTuple class is used to receive any number of arguments from Lua and to return any number of values to Lua.

This class behaves like a stack but you can also convert it to and from an array.

## Methods

### from_array _LuaTuple_ {#with_ref}

Static method that creates a LuaTuple with the contents of an array.

#### Parameters

| Parameters | Description |
| --- | --- |
| content: `array` | The content with which the tuple will be populated. |

#### Returns

_LuaTuple_

#### Example
	
```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()
	lua.push_variant("getPosition", my_get_position)
	lua.do_string("""
	local x, y, z = getPosition()
	print(x, y, z)
	""")

func my_get_position():
	return LuaTuple.from_array([10, 3, 7])
```
``` title="Output"
10, 3, 7
```

---

### clear _void_ {#clear}

Removes all elements from the LuaTuple's stack.

#### Parameters

None

#### Returns

_void_

---

### get_value *Variant* {#get_value}

Gets a value from the LuaTuple's stack at the specified index. It does not pop the value from the stack.

#### Parameters

| Parameters                | Description                                          |
| ------------------------- | ---------------------------------------------------- |
| index: `number` | The index of the value to get.								   |

#### Returns

_Variant_

---

### is_empty *bool* {#is_empty}

Returns `#!gdscript true` if the LuaTuple's stack is empty and false otherwise.

#### Parameters

None

#### Returns

_bool_

---

### pop_back _Variant_ {#pop_back}

Removes the last element from the LuaTuple's stack and returns it.

#### Parameters

None

#### Returns

_Variant_

---

### pop_front _Variant_ {#pop_front}

Removes the first element from the LuaTuple's stack and returns it.

#### Parameters

None

#### Returns

_Variant_

---

### push_back _void_ {#push_back}

Adds a value to the end of the LuaTuple's stack.

#### Parameters

| Parameters             | Description                            |
| ---------------------- | -------------------------------------- |
| value: `Variant` | The value to be added to the stack. |

#### Returns

_void_

---

### push_front _void_ {#push_front}

Adds a value to the start of the LuaTuple's stack.

#### Parameters

| Parameters             | Description                           |
| ---------------------- | ------------------------------------- |
| variableName: `String` | The name of the Lua variable to read. |

#### Returns

_void_

---

### set_value *void* {#set_value}

Sets the value at the specified index in the LuaTuple's stack.

#### Parameters

| Parameters             | Description                           |
| ---------------------- | ------------------------------------- |
| index: `number` 		 | An integer representing the index. 	 |
| value: `Variant`       | The value to be set. 				 |


#### Returns

*void*

---

### size *number* {#size}

Returns the number of elements in the LuaTuple's stack.

#### Parameters

None

#### Returns

_number_

---

### to_array *array* {#to_array}

Converts the LuaTuple's stack to an array.

#### Parameters

None

#### Returns

_array_

#### Example
	
```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

func _ready():
	lua = LuaAPI.new()

	# Wrap custom_print_function with a LuaCallableExtra.
	var luaCallableExtra = LuaCallableExtra.new()
	luaCallableExtra.set_info(custom_print_function, 1, true, true)

	# This will replace the built-in print function with ours.
	lua.push_variant("print", luaCallableExtra)
    
	lua.do_string("""
		print('Hello!', ' How are you?', ' I havent seen you in ', 10, ' days.')
	""")
	
func custom_print_function(lua_object: LuaAPI, args: LuaTuple):
	print(args.to_array(), " -- ", lua_object)
```
``` title="Example Output"
["Hello!", " How are you?", " I havent seen you in ", 10, " days."] -- <LuaAPI#-9223372010447437394>
```

---