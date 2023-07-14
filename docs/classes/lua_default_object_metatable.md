# LuaDefaultObjectMetatable

## Description

This is the default [LuaObjectMetatable](lua_object_metatable.md) used by the [LuaAPI](lua_api.md) class as the object_metatable value. It provides v2.0 like funcinality in regards to Object metamethod overides. 

By default all fields/methods will be allowed on an object, if the object defines a `lua_fields` method, the array of field names it returns will be treated as a blacklist. If permissive is changed to false, it will be treated as a whitelist instead.

Before any value is returned or any metamethod is called, this metatable will first see if the object defines a method with the same name as the metamethod. If it does, that will be called instead with the normal lua arguments you expect, plus referance to the [LuaAPI](lua_api.md) object as the first argument.

---

## lua_fields
By default every field and method is available to Lua. You can limit what Lua has access to by defining either the `lua_fields()` method, which must return an Array of the names of fields/methods. By default this array is treated as a blacklist of blocked methos. If you set [permissive](../classes/lua_api.md#permissive-bool) to true, it will be treated as a whitelist. The other way to limit access is by defining the `__index()` method, which takes reference to the [LuaAPI](../classes/lua_api.md) and a string of the field being requested by Lua. You can then return any value you please to lua.

---

## Properties
### permissive _Bool_
When set to true all methods will be allowed on Objects be default and lua_fields is treated as a blacklist. When set to false, lua_fields is treated as a whitelist.

Default value is true.