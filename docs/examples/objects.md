# Working with Objects in Lua
Normally when using `push_variant()` or the return value of a GDScript function that Lua calls. The value is copied to Lua. However, types that inherit from Objects work a bit differently. Objects will be pushed to Lua as [UserData](https://www.lua.org/pil/28.1.html). This means they are passed by reference to or from Lua. 

In order to call functions and get values from the object we assign the mt_Object metatable to it. This is a custom meta table we defined for Objects. This allows for methods to be called on the object, values to be get/set on the Object and much more.

By default every field and method is available to Lua. You can limit what Lua has access to be defining either the `lua_fields()` method. Which must return an Array of string names for the allowed fields/methods. You can also use the `__index()` method. Which takes reference to the [LuaAPI](../classes/lua_api.md) and a string of the field being requested by Lua. If you would like to allow the field, simply return the value. You may also return a [LuaError](../classes/lua_error.md) to raise a error on the Lua state.

Below is a example of passing a Object to Lua:
```gdscript
extends Node2D

var lua: LuaAPI

class Player:
	var pos = Vector2(0, 0)
	# If lua_fields is not defined or returns a empty array, all functions and fields will be aval.
	func lua_fields():
		return ["pos", "move_forward"]
	func move_forward():
		pos.x+=1

var player2: Player

func _ready():
	lua = LuaAPI.new()
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

For some this is still too limiting though. So we also allow you to override almost any metamethod on the object. Here is a example of that:
```gdscript
extends Node2D

var lua: LuaAPI

class Player:
	var pos = Vector2(1, 0)
	# Most metamethods can be overriden. The function names are the same as the metamethods.
	func __index(ref: LuaAPI, index):
		if index=="pos":
			return pos
		else:
			return LuaError.new_error("Invalid index '%s'" % index)
	func move_forward():
		pos.x+=1

func _ready():
	lua = LuaAPI.new()
	lua.expose_constructor("Player", Player)

	var err = lua.do_string("""
    player = Player()
    print(player.pos.x)
    player.move_forward() -- This will cause our custom error
    """)

	if err is LuaError:
		print(err.message)

	var player = lua.pull_variant("player")
	print(player.pos)
```

The first argument passed to the metamethod is always reference to the [LuaAPI](../classes/lua_api.md) object. Besides that the arguments will be the same as if you were using it in Lua.