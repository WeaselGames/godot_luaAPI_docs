# LuaError

## Description
A LuaError represents an Error in Lua.  
  
  Most functions in this module return either a LuaError or null. You may also return a LuaError object from a GDScript function called from Lua in order to cause an error in Lua.

## Enumerations
### ErrorType
| Name | Value | Description |
| --- | --- | --- |
| ERR_TYPE | 1 | Invalid type error. For example, passing a string when an int was expected. |
| ERR_RUNTIME | 2 | Lua runtime error. |
| ERR_SYNTAX| 3 | Lua syntax error. |
| ERR_MEMORY| 4 | Lua ran out of memory. |
| ERR_ERR| 5 | Error while handling another error. |
| ERR_FILE| 6 | Error while opening or reading a file. |

---

## Properties
### msg _String_
A string containing the error message.

---

### type [_ErrorType_](#errortype)
An enum representing the type of the error.

---

## Methods
### new_err _LuaError_ {#new_err}
Static function that acts as a constructor for new instances of the LuaError class.

#### Parameters
| Parameters | Description |
| --- | --- |
| message: `String` | The error message. |
| type: `ErrorType` | An enum representing the type of this error. |

#### Returns
_LuaError_

#### Example
```gdscript
func test(n: int):
	if n != 5:
		## This will raise an error in the Lua state.
		return LuaError.new_err("N is not 5 but is %s" % n, LuaError.ERR_RUNTIME)
	return n+5

func _ready():
	lua = Lua.new()
	lua.push_variant(test, "test")
	## Most methods return a LuaError.
	## Calling "test" with a type that is not a int would also raise a error.
	var err = lua.do_string("test(6)")
	## If an error does not occur, err will be null.
	if err is LuaError:
		print("ERROR %d: " % err.type + err.msg)
```
