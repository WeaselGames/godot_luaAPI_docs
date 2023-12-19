Godot Lua API DotNet Notes
===============

<!-- TOC -->
* [Godot Lua API DotNet Notes](#godot-lua-api-dotnet-notes)
  * [Introduction](#introduction)
  * [Commands](#commands)
  * [Example Notes](#example-notes)
<!-- TOC -->

Introduction
-------

When working with this version of the editor, or the extension addon, you will need to use specific versions of the 
nuget packages for your project. This has been tested with v2.1-beta6.

For this document, we will use *nix style paths. If you are a Windows user, please convert lines like `/path/to/` to 
`C:\path\to\` so that it will work with your system. MacOS users can use the *nix paths.

The editor builds have a file named `mono_instructions.txt` included in the zipped release.
The contents of this file are as follows:

```
If you have GodotSharp locally cached dotnet may use one of them instead. To clear local cache run the following command:
dotnet nuget locals all --clear

To add the local nuget source, please run the following command:
dotnet nuget add source /path/to/nuget_packages/ --name LuaAPINugetSource
```

What this is saying is that you will want to open up a command prompt and do the following steps to install the correct 
nuget packages into your project. If you have downloaded the editor zip file, it comes with a directory named `nuget_packages` 
This is the directory that you will want to include into the second step in the instructions file. So, if you have extracted 
the Editor to `C:\Users\username\Godot` the correct path to use would be `C:\Users\username\Godot\nuget_packages`. 
Likewise, if you extracted the editor to `/home/username/Godot/` you would want to use `/home/username/Godot/nuget_packages`.

Make sure to enclose directories with spaces with either a `"` or `'` so that the path resolves correctly.

Additionally, you will most likely want to execute a `dotnet restore` command to install the correct nuget packages. See 
Commands below for an example on how to do this.

Commands
-------

In your command prompt, execute these commands in the following order.

`dotnet nuget locals all --clear`  -- This command clears the local package directory, so if updating, copy the proper 
packages from the archive, so that they are there before proceeding.

`dotnet nuget add source /path/to/nuget_packages --name LuaAPINugetSource` -- Only necessary if the source hasn't yet been added.

`dotnet restore '/pathtoproject/example_project.csproj' -f -s  LuaAPINugetSource`

This will set up the proper packages to work with the Editor / Add-on. Note: you may have to select the correct nuget 
source within your IDE. If so, please use the `LuaAPINugetSource` option. Note that in the third command, we are using 
the specific location (`-s <source>`) and we are forcing (`-f`) the restore. This is done to specifically use the custom 
nuget packages. If you get errors, you can use `dotnet nuget list source` to list the sources. Also, there's a `remove` 
command that will allow you to redo the source add. Note that putting a trailing `/` on the endo of the path will generally 
mess up the local source. 

Once you have done this, you will need to rebuild your project. You can do so either through your IDE or inside of the
Godot Editor. I highly recommend keeping this section handy, as you will need to use these for each new project
that you create. If you can, it's advisable to change the nuget sources so that the local source is first in the
list. This will make life easier. And then, with new projects, run the clear command, then restore the project in
your IDE, or simply build the project in Godot. (Godot will perform a restore in the build process.) By having the
local sources first in the list, this will ensure that they are put in, and that your project will work.

A note on the LuaAPI specific nuget packages: They are included in the Mono (DotNet) builds from the `Releases` tab on
the Github page.

Additional help with the Nuget Packages installation:
In some cases the packages will fail to restore, and if that happens to you, this is something that you can do to try
to make it work. You will need to remove the existing `LuaAPINugetSource` that you made above, and then put this file
in your project directory. As it uses an absolute path, others will need to change it to their location, if they are
part of your team. (Like an open source project, or if they are compiling the code themselves.)
* Create a nuget.config file in the root of your project or solution (if it doesn't already exist).
* Open or create the nuget.config file, and add the following:

```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <add key="LuaAPINugetSource" value="/path/to/editor/editor-mono/nuget_packages" />
        <!-- Add other package sources if needed -->
      </packageSources>
    </configuration>
```

* Replace `/path/to/editor/editor-mono/nuget_packages` with the correct path to your local NuGet source. Note the
  lack of a trailing slash.
* Save the nuget.config file.

With this configuration, the NuGet restore process will automatically consider the sources listed in the nuget.config.
Note that windows users may have other issues with dotnet, files being marked unsafe because they originated from other
computers, not being done as an administrator, etc. Sadly, those are on the user to fix as it is beyond the scope of a
getting started file.


Example Notes
-------

As you can expect, the method names in the LuaAPI class are in Dotnet style. So, something like `LuaAPI.push_variant` 
would be `LuaAPI.PushVariant`

Starter Example:
```csharp
using Godot;
using System;

public partial class Node2D : Godot.Node2D
{
	// Called when the node enters the scene tree for the first time.
	public override void _Ready()
	{
		GD.Print("Hello from C#");
		LuaApi lua = new LuaApi();
		lua.DoString("print(\"Hello from Lua\")");
	}
}
```
Note that `lua.DoString()` is the dotnet version of `lua.do_string()`.

The above starter example will produce its output into the Godot Output window in the Godot Editor. Here is the exact 
output it produces.
```
Hello from C#
Hello from Lua
```

More [examples](csexamples%2FEXAMPLE1.md) will be included very soon (see Wiki).