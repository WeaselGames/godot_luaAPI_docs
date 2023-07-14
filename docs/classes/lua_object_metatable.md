# LuaObjectMetatable

## Description

This is a pure interface class that allows you to define a custom object metatabel by extending it. 

---

## Methods

### __index _Variant_ {#__index}
This is a virtual method.

The indexing access operation table[key]. This event happens when table is not a table or when key is not present in table. The metavalue is looked up in the metatable of table.

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| index: `String`             | The index trying to be accessed.                           |

#### Returns

_Variant_

---

### __newindex *[LuaError](lua_error.md)* {#__newindex}
This is a virtual method.

The indexing assignment table[key] = value. Like the index event, this event happens when table is not a table or when key is not present in table. The metavalue is looked up in the metatable of table.

Like with indexing, the metavalue for this event can be either a function, a table, or any value with an __newindex metavalue. If it is a function, it is called with table, key, and value as arguments. Otherwise, Lua repeats the indexing assignment over this metavalue with the same key and value. This assignment is regular, not raw, and therefore can trigger another __newindex metavalue.

Whenever a __newindex metavalue is invoked, Lua does not perform the primitive assignment. If needed, the metamethod itself can call rawset to do the assignment. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| index: `String`             | The index trying to be accessed.                           |
| value: `Variant`            | The value being assigned to the index.                     |

#### Returns

*[LuaError](lua_error.md)*

---

### __add _Variant_ {#__add}
This is a virtual method.

The addition (+) operation. If any operand for an addition is not a number, Lua will try to call a metamethod. It starts by checking the first operand (even if it is a number); if that operand does not define a metamethod for __add, then Lua will check the second operand. If Lua can find a metamethod, it calls the metamethod with the two operands as arguments, and the result of the call (adjusted to one value) is the result of the operation. Otherwise, if no metamethod is found, Lua raises an error. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __band _Variant_ {#__band}
This is a virtual method.

The bitwise AND (&) operation. Behavior similar to the addition operation, except that Lua will try a metamethod if any operand is neither an integer nor a float coercible to an integer.

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __bnot _Variant_ {#__bnot}
This is a virtual method.

The bitwise NOT (unary ~) operation. Behavior similar to the bitwise AND operation.

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |

#### Returns

_Variant_

---

### __bor _Variant_ {#__bor}
This is a virtual method.

The bitwise OR (|) operation. Behavior similar to the bitwise AND operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __bxor _Variant_ {#__bxor}
This is a virtual method.

The bitwise exclusive OR (binary ~) operation. Behavior similar to the bitwise AND operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __call _Variant_ {#__call}
This is a virtual method.

The call operation. Used when the object is called as a function.

#### Parameters

| Parameters                        | Description                                                |
| --------------------------------- | -----------------------------------------------------------|
| obj: `Object`                     | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)*       | Referance of the LuaAPI object for the state.              |
| other: *[LuaTuple](lua_tuple.md)* | Arguments passed to the method call.                       |

#### Returns

_Variant_

---

### __concat _Variant_ {#__concat}
This is a virtual method.

The concatenation (..) operation. Behavior similar to the addition operation, except that Lua will try a metamethod if any operand is neither a string nor a number (which is always coercible to a string). 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __eq _Variant_ {#__eq}
This is a virtual method.

The equal (==) operation. Behavior similar to the addition operation, except that Lua will try a metamethod only when the values being compared are either both tables or both full userdata and they are not primitively equal. The result of the call is always converted to a boolean. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __gc _Variant_ {#__gc}
This is a virtual method.

Called when the lua garbage collector trys to collect the object.

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |

#### Returns

_Variant_

---

### __idiv _Variant_ {#__idiv}
This is a virtual method.

The floor division (//) operation. Behavior similar to the addition operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __le _bool_ {#__le}
This is a virtual method.

The less equal (<=) operation. Behavior similar to the less than operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_bool_

---

### __len _int_ {#__len}
This is a virtual method.

The length (#) operation. If the object is not a string, Lua will try its metamethod. If there is a metamethod, Lua calls it with the object as argument, and the result of the call (always adjusted to one value) is the result of the operation. If there is no metamethod but the object is a table, then Lua uses the table length operation. Otherwise, Lua raises an error. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |

#### Returns

_int_

---

### __lt _bool_ {#__lt}
This is a virtual method.

The less than (<) operation. Behavior similar to the addition operation, except that Lua will try a metamethod only when the values being compared are neither both numbers nor both strings. Moreover, the result of the call is always converted to a boolean. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_bool_

---

### __metatable _Variant_ {#__metatable}
This is a virtual method.

If object does not have a metatable, returns nil. Otherwise, if the object's metatable has a __metatable field, returns the associated value. Otherwise, returns the metatable of the given object. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |

#### Returns

_Variant_

---

### __mod _Variant_ {#__mod}
This is a virtual method.

The modulo (%) operation. Behavior similar to the addition operation.

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __mul _Variant_ {#__mul}
This is a virtual method.

The multiplication (*) operation. Behavior similar to the addition operation.

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __pow _Variant_ {#__pow}
This is a virtual method.

The exponentiation (^) operation. Behavior similar to the addition operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __shl _Variant_ {#__shl}
This is a virtual method.

The bitwise left shift (<<) operation. Behavior similar to the bitwise AND operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __shr _Variant_ {#__shr}
This is a virtual method.

The bitwise right shift (>>) operation. Behavior similar to the bitwise AND operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __sub _Variant_ {#__sub}
This is a virtual method.

The subtraction (-) operation. Behavior similar to the addition operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |
| other: `Variant`            | The other value passed to the operator.                    |

#### Returns

_Variant_

---

### __tostring _String_ {#__tostring}
This is a virtual method.

The tostring operation. Used when tostring is called on the object.

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |

#### Returns

_String_

---

### __unm _Variant_ {#__unm}
This is a virtual method.

The negation (unary -) operation. Behavior similar to the addition operation. 

#### Parameters

| Parameters                  | Description                                                |
| --------------------------- | -----------------------------------------------------------|
| obj: `Object`               | Referance of the object the metamethod is being called on. |
| lua: *[LuaAPI](lua_api.md)* | Referance of the LuaAPI object for the state.              |

#### Returns

_Variant_

---