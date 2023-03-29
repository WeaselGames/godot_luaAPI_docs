# Home

## Godot LuaAPI V2.0-beta
!!! warning
    this is a **beta** version of the addon made for Godot v4.0-stable. Which means frequent recompiles may be required and compatibility is not guaranteed between updates. Please see the branch [v1.1-stable](https://github.com/WeaselGames/lua/tree/v1.1-stable) for godot v3.x.

<figure markdown>  
  ![Godot LuaAPI logo](./assets/luaapi_logo.png)
  <figcaption>Art created by <a href="https://www.instagram.com/redheadalex1" target="_blank">Alex</a></figcaption>
</figure>

This is a Godot engine addon that adds Lua API support via GDScript. Importantly this is **NOT** meant to be a replacement for or alternative to GDScript. This addon provides no functionality to program your game out of the box. This addon allows you to create custom Modding API's in a sandboxed environment. You have control of what people can and can not do within that sandbox.

To use you can either [Compile from source](getting_started/installation#compiling-from-source) or you can download one of the [nightly builds](getting_started/installation#nightly-builds).

By default the Lua print function is set to print to the GDEditor console. This can be changed by exposing your own print function as it will overwrite the existing one.

## Features

- Run Lua directly from a string or a text file.
- Push any Variant as a global.
- Call Lua functions from GDScript.
- Choose which libraries you want Lua to have access to.
- Custom LuaCallable type which allows you to get a Lua function as a Callable.
- LuaError type which is used to report any errors this addon or Lua run into.
- LuaThread type which creates a Lua thread. This is not a OS thread but a coroutine.
- Object passed as userdata.
- Objects can override most of the Lua metamethods. I.E. \_\_index by defining a function with the same name.
- Callables passed as userdata, which allows you to push a Callable as a Lua function.
- Basic types are passed as userdata (currently: Vector2, Vector3, Color, Rect2, Plane) with a useful metatable.

If a feature is missing that you would like to see feel free to create a [Feature Request](https://github.com/WeaselGames/lua/issues/new?assignees=&labels=feature%20request&template=feature_request.md&title=) or submit a PR.
