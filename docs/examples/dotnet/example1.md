Godot Lua API C# Examples
===============

All future examples will be in the [Wiki](https://luaapi.weaselgames.info/latest).

Getting Started Example (In C#)
-------

In this example, we recreate the main GDScript example from the README.md file. I have fully commented this
example, to explain what each part does. Specific differences and a special note on this example: In C# you
cannot assign a Method to a `Variant` for use with `LuaAPI.PushVariant`. So, to get around that, and make the
example work, we will first wrap our called function in a `Callable` variable (wrapper). Please note the line
`Callable print = new Callable(this, MethodName.LuaPrint);` in the example below.

* Notes on Using `LuaFunctionRef`: It is the preferred way to call functions from within your Lua code base, if you do 
  not know the function name ahead of time, or if the function is not global. `LuaFunctionRef` will still be invokable 
  if it is a global function, and that global function was overwritten, as it is copied into the lua state, and then 
  referenced inside of C#. Removing all references to the LuaFunctionRef will release it out of the lua state and free 
  up the state's memory. This means that `LuaAPI.CallFunction()` uses less resources in the lua state, so keep that in mind. 

This example also shows how to use the newly added `LuaFunctionRef` class to `invoke` a lua function. This allows
for creating a call back function to respond to things like events. Note how we use `.As<LuaFunctionRef>()` to cast
the Variant `val` into a `LuaFunctionRef` variable (`get_message`). Additionally, we do not need to use the nullable
invoke style common to most event firing, as the `get_message` variable is not null. The `.Invoke()` method takes a
`Godot.Collections.Array` as its only parameter. In this example, we make use of the shorthand to create a `new` object
for the Godot Array class. This shorthand is only possible because of the way that Godot defines the `Array` class, and
is not a normal C# `new Object()` creation style.

```csharp
using Godot;

public partial class Node2D : Godot.Node2D {
	private LuaApi lua = new LuaApi();

	public void LuaPrint(string message) {
		GD.Print(message);
	}

	public override void _Ready() {
		GD.Print("Starting Node2D.cs.");

		// All builtin libraries are available to bind with. Use OS and IO at your own risk.
		// BindLibraries requires a "Godot Array" so, let's build one.
		Godot.Collections.Array libraries = new() {
			"base",  // Base Lua commands
			"table", // Table functionality.
			"string" // String Specific functionality.
		};
		lua.BindLibraries(libraries); // Assign the specified libraries to the LuaAPI object.

		// In C#, .PushVariant does not work with Methods, so we use Callable to wrap our function.
		Callable print = new Callable(this, MethodName.LuaPrint);
		// Assign the Callable, so that the API can call our function.
		// Note, the lua function "cs_print" is now callable within Lua script.
		lua.PushVariant("cs_print", print);
		// Assign a Lua Variable named "message" and give it a value.
		lua.PushVariant("message", "Hello lua!");

		// Use .DoString() to execute our Lua code.
		LuaError error = lua.DoString("cs_print(message)");
		// Check for errors, and if there are any, Print them to the Godot Console.
		if (error != null && error.Message != "") {
			GD.Print("An error occurred calling DoString.");
			GD.Print("ERROR %d: %s", error.Type, error.Message);
		}

		error = lua.DoString(@"
                                  for i=1,10,1 do
                                  	cs_print(message)
                                  end
                                  function get_message()
                                  	return ""This message was sent from 'get_message()'""
                                  end
                                  ");

		// Check for errors, and if there are any, Print them to the Godot Console.
		if (error != null && error.Message != "") {
			GD.Print("An error occurred calling DoString.");
			GD.Print("ERROR %d: %s", error.Type, error.Message);
		}
		
		// Let's pull our lua function from the lua code.
		var val = lua.PullVariant("get_message");
		// Check to see if it returned an error, or a value.
		if (val.GetType() == typeof(LuaError)) {
			GD.Print("ERROR %d: %s", error.Type, error.Message);
			return;
		}

		// We create a LuaFunctionRef as our reference to the Lua code's function,
		// then we use .As<LuaFunctionRef>() to cast it as a LuaFunctionRef.
		LuaFunctionRef get_message = val.As<LuaFunctionRef>();
		if (get_message == null) {
			GD.Print("ERROR: get_message is null.");
			return;
		}

		// Calling Lua (code) functions requires a Godot.Collections.Array as the container
		// for the parameters passed in. 
		Godot.Collections.Array Params = new();
		// We use .Invoke to actually call the lua function within the Lua State. 
		// And, finally, we log the output of the function to Godot Output Console.
		GD.Print(get_message.Invoke(Params));

	}
}
```