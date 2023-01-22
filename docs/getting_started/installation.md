# Installation

The first thing you will need to do is obtain a copy of the editor built with this module. You can either compile from source yourself or download one of the [releases](https://github.com/WeaselGames/godot_luaAPI/releases).

## Compiling from source

### Getting the sources
- Start by cloning the 3.4.4-stable branch of the Godot engine  
`git clone https://github.com/godotengine/godot -b 3.4.4-stable`
- Next cd into godot/modules and clone this repo  
`cd godot/modules`  
`git clone --recurse-submodules -b v1.1-stable https://github.com/WeaselGames/godot_luaAPI luaAPI`

### Compiling
This module does not requires any extra tooling to compile besides that required by Godot its self. So from this point you can follow the tutorial on the [Godot wiki](https://docs.godotengine.org/en/stable/development/compiling/).