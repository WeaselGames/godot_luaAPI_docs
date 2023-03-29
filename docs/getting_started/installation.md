# Installation

The first thing you will need to do is obtain a copy of the editor built with this module. You can either compile from source yourself or download one of the nightly builds. Or you can download the GDExtension plugin.
## Nightly Builds
- [⚙️ GDExtension](https://nightly.link/WeaselGames/godot_luaAPI/workflows/gdextension/main/godot_luaAPI.zip)
- [🐧 Linux Editor](https://nightly.link/WeaselGames/godot_luaAPI/workflows/linux/main/linux-editor.zip)
- [🐧 Linux Template](https://nightly.link/WeaselGames/godot_luaAPI/workflows/linux/main/linux-template-release.zip)
- [🐧 Linux Template Debug](https://nightly.link/WeaselGames/godot_luaAPI/workflows/linux/main/linux-template-debug.zip)
- [🎨 Windows Editor](https://nightly.link/WeaselGames/godot_luaAPI/workflows/windows/main/windows-editor.zip)
- [🎨 Windows Template](https://nightly.link/WeaselGames/godot_luaAPI/workflows/windows/main/windows-template.zip)
- [🎨 Windows Template Debug](https://nightly.link/WeaselGames/godot_luaAPI/workflows/windows/main/windows-template-debug.zip)
- [🍎 MacOS Editor](https://nightly.link/WeaselGames/godot_luaAPI/workflows/macos/main/macos-editor.zip)
- [🍎 MacOS Template](https://nightly.link/WeaselGames/godot_luaAPI/workflows/macos/main/macos-template.zip)
- [🍎 MacOS Template Debug](https://nightly.link/WeaselGames/godot_luaAPI/workflows/macos/main/macos-template-debug.zip)

## Compiling from source

### Getting the sources
- Start by cloning the main branch of the Godot engine  
`git clone https://github.com/godotengine/godot`
- Next cd into godot/modules and clone this repo  
`cd godot/modules`  
`git clone --recurse-submodules https://github.com/WeaselGames/godot_luaAPI luaAPI`

### Compiling
This module does not requires any extra tooling to compile besides that required by Godot its self. So from this point you can follow the tutorial on the [Godot wiki](https://docs.godotengine.org/en/latest/contributing/development/compiling/index.html).