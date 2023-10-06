# Working with Objects in Lua
Normally, when using `push_variant()` or the return value of a GDScript function that Lua calls, the value is copied to Lua. However, types that inherit from [Object](https://docs.godotengine.org/en/stable/classes/class_object.html) work a bit differently. Objects will be pushed to Lua as [UserData](https://www.lua.org/pil/28.1.html). This means they are passed by reference to or from Lua.

## Controlling access to fields and methods
!!! info "In the background..."
	In order to call functions and get values from the object we assign the mt_Object metatable to it. This is a custom metatable we defined for Objects. This allows for methods to be called on the object, values to be get/set on the Object and much more.
### With the default object metatable
By default every field and method is available to Lua. You can limit what Lua has access to by defining either the `lua_fields()` method, which must return an Array of the names of fields/methods. By default this array is treated as a blacklist of methods. If you set [permissive](../classes/lua_default_object_metatable.md#permissive-bool) to `false`, it will be treated as a whitelist instead. The other way to limit access is by defining the `__index()` method, which takes reference to the [LuaAPI](../classes/lua_api.md) object and a string of the field being requested by Lua. You can then return any value you please to lua.

This is all made possible by the [LuaDefaultObjectMetatable](../classes/lua_default_object_metatable.md) assigned to the `object_metatable` field on the [LuaAPI](../classes/lua_api.md) object by default. For even more control, you can define your own object metatable. See the [LuaObjectMetatable](../classes/lua_object_metatable.md) class.

Below is an example of passing a Object to Lua:
```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

class Player:
	var pos = Vector2(0, 0)
	# By default lua_fields returns a blacklist of methods to block, since we set permissive to false however, it is treated as a whitelist instead.
	func lua_fields():
		return ["pos", "move_forward"]
	func move_forward():
		pos.x+=1

var player2: Player

func _ready():
	lua = LuaAPI.new()
	# The default metatable is a LuaDefaultObjectMetatable instance. This provides v2.0 like functionality 
	lua.object_metatable.permissive = false
	player2 = Player.new()
	lua.push_variant("getPlayer2", func(): return player2)
	lua.expose_constructor("Player", Player)

	var err = lua.do_string("""
    player = Player()
    player.move_forward()
    print(player.pos.x)

    player2 = getPlayer2()
    player2.pos = Vector2(50, 1)
    print(player2.pos)
    """)

    if err is LuaError:
		print(err.message)

	var player = lua.pull_variant("player")
	print(player.pos)
	print(player2.pos)
```
### Overriding metamethods
For some, the previous approach is still too limiting though. So we also allow you to override almost any metamethod on the object.

!!! note
	The first argument passed to the metamethod is always reference to the [LuaAPI](../classes/lua_api.md) object. Besides that the arguments will be the same as if you were using it in Lua.

!!! info
	You can learn more about all available metamethods in the official [Lua manual](https://www.lua.org/manual/5.3/manual.html#2.4).

#### __index
The __index metamethod is called when Lua tries to get a value from an object. This is when you are using the `.index` or the `['index']` operator in Lua. Essentially, this is what the `lua_fields()` method is doing. However, you may want to do more than just return a value. You can also raise an error or return a custom value. 

The __index method must take two arguments. The first is a reference to the [LuaAPI](../classes/lua_api.md) object. The second is the name of the field/method being requested. The method may return the value of the field/method or a [LuaError](../classes/lua_error.md) to raise an error.

 Here is a example of that:
```gdscript linenums="1"
extends Node2D

var lua: LuaAPI

class Player:
	var pos = Vector2(1, 0)
	# Most metamethods can be overridden. The function names are the same as the metamethods.
	func __index(ref: LuaAPI, index):
		if index=="pos":
			return pos
		elif index=="move_backward":
			return move_backward
		else:
			return LuaError.new_error("Invalid index '%s'" % index)
	
	func move_backward():
		pos.x+=1

	func move_forward():
		pos.x+=1

func _ready():
	lua = LuaAPI.new()
	lua.expose_constructor("Player", Player)

	var err = lua.do_string("""
    player = Player()
    print(player.pos.x)
	player.move_backward()
    player.move_forward() -- This will cause our custom error
    """)

	if err is LuaError:
		print(err.message)

	var player = lua.pull_variant("player")
	print(player.pos)
```

---

#### __newindex
The __newindex metamethod is called when Lua tries to set a value on an object. This is the same as if you were using the `=` operator in Lua. Essentially, this is what the `lua_fields()` method is doing. However, you may want to do more than just set a value. You can also raise an error or set a custom value.

The __newindex method must take three arguments. The first is a reference to the [LuaAPI](../classes/lua_api.md) object. The second is the name of the field/method being set. The third is the value being set. The method may a [LuaError](../classes/lua_error.md) to raise an error.

 Here is a example of that:
```gdscript
extends Node2D

var lua: LuaAPI

class Player:
	var pos = Vector2(1, 0)
	# Most metamethods can be overridden. The function names are the same as the metamethods.
	func __newindex(ref: LuaAPI, index, value):
		if index=="pos":
			pos = value
		else:
			return LuaError.new_error("Invalid index '%s'" % index)
	func move_forward():
		pos.x+=1

func _ready():
	lua = LuaAPI.new()
	lua.expose_constructor("Player", Player)

	var err = lua.do_string("""
	player = Player()
	player.pos = Vector2(50, 1)
	print(player.pos)
	player.move_forward = 5 -- This will cause our custom error
	""")

	if err is LuaError:
		print(err.message)

	var player = lua.pull_variant("player")
	print(player.pos)
```

---