# The LuaCallable Type

## Description
The LuaCallable type extends the CallableCustom type. It is used when a Lua function is returned or pulled from Lua. In this case, a copy of the function is created and a reference to it is stored. The CallableCustom holds this reference ID and a reference to the Lua state, which enables the function to be called at a later time. By using LuaCallable, you can save a reference to a Lua function and call it whenever needed.

!!! warning
    Due to no support for the CallableCustom type in GDExtension. This type is not present in the GDExtension version of this addon.

## Example
```gdscript
extends Node

var lua: LuaAPI = LuaAPI.new()

func _ready():
	lua.do_string("""
	function set_location(pos)
		pos = pos + Vector2(1.5, 9)
		return pos
	end
	""")
	var set_location = lua.pull_variant("set_location")
	var value2 = set_location.call(Vector2(1, 1))
	print(value2)
```
