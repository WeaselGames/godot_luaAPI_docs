# Development Environment
Developing for this addon can be a little tricky at first. Its highly recommended that you have a decent amount of experience with Godot and C++ if you plan on contributing. 

## What you will need
- An IDE, we will discuss [Visual Studio Code](https://code.visualstudio.com/) here but feel free to use others.
- Respective platform build requirements as specified [here](https://docs.godotengine.org/en/stable/contributing/development/compiling/index.html). If on Windows its recommended to use mingw over Visual Studio. As Visual Studio tends to give very unhelpful error messages. We recommend [TDM-GCC](https://jmeubank.github.io/tdm-gcc/) for mingw.
- A copy of the latest godot 4.0 stable build and the godot source code.

## Setting up VSCode

### Recommended Addons
- [Microsoft C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools).
- [Clang-Format](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format).
- [CS 128 Clang-Tidy](https://marketplace.visualstudio.com/items?itemName=CS128.cs128-clang-tidy).
- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

### Setup Compilation Tasks
Tasks in VSCode help a lot to quickly rebuild the project. To setup compilation tasks create the file `.vscode/tasks.json` in the base repo folder. Below is an example config, make sure to replace all [PLACE HOLDERS].
```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Build Module",
      "type": "shell",
      "command": "scons target=editor debug_symbols=yes custom_modules=${workspaceFolder} compiledb=yes && cp compile_commands.json ${workspaceFolder}/compile_commands.json",
      "problemMatcher": "$msCompile",
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "options": {
          "cwd": "[PATH TO GODOT SOURCE FOLDER]"
        }
    },
    {
      "label": "Build GDExtension",
      "type": "shell",
      "command": "scons target=template_debug debug_symbols=yes",
      "problemMatcher": "$msCompile",
      "group": {
        "kind": "build",
        "isDefault": false
      },
      "options": {
          "cwd": "${workspaceFolder}"
        }
    }
  ]
}
```

### Setup Debugging
For debugging you must have [GDB](https://www.sourceware.org/gdb/) installed. Start be creating the file `.vscode/launch.json` in the base repo folder. Below is an example config, make sure to replace all [PLACE HOLDERS].
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Module Debug",
      "type": "cppdbg",
      "request": "launch",
      "program": "[PATH TO GODOT SOURCE FOLDER]/bin/godot.[PLATFORM].editor.x86_64",
      "args": [/*"--editor"*/], // Uncomment if you would like to run the editor. Keep in mind debugging the project will not launch in GDP, only the editor its self.
      "stopAtEntry": false,
      "cwd": "[PATH TO MODULE TEST PROJECT]",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "miDebuggerPath": "[PATH TO GDB]",
      "setupCommands": [
          {
              "description": "Enable pretty-printing for gdb",
              "text": "-enable-pretty-printing",
              "ignoreFailures": true
          }
      ]
  },
  {
      "name": "GDExtension Debug",
      "type": "cppdbg",
      "request": "launch",
      "program": "[PATH TO STABLE GODOT 4.0 BUILD]",
      "args": [/*"--editor"*/], // Uncomment if you would like to run the editor. Keep in mind debugging the project will not launch in GDP, only the editor its self.
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}/project",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "miDebuggerPath": "[PATH TO GDB]",
      "setupCommands": [
          {
              "description": "Enable pretty-printing for gdb",
              "text": "-enable-pretty-printing",
              "ignoreFailures": true
          }
      ]
  },
  ]
}
```

### Other Helpful Settings
In the above compilation tasks we tell SCons to build a `compile_commands.json` file. Which is a universal format for IDE's to see what exact command is needed to compile each file. This will prevent VSCode form having a ton of random include errors since the godot source is outside of the project folder. We need to tell VSCode that his file exists and about godot-cpp includes. To do so create the file `.vscode/c_cpp_properties.json` in the base repo folder. Below is an example config, make sure to replace all [PLACE HOLDERS].
```json
{
  "configurations": [
    {
      "name": "[Linux, Win32 or Mac]",
      "includePath": [
          "${workspaceFolder}/**",
          "${workspaceFolder}/external/**",
          "${workspaceFolder}/external/godot-cpp/include/**",
          "${workspaceFolder}/external/godot-cpp/gen/include/**"
      ],
      "defines": [
          // "LAPI_GDEXTENSION"
      ],
      "cStandard": "c17",
      "cppStandard": "c++14",
      "compileCommands": "compile_commands.json"
    }
  ],
  "version": 4
}
```

Another helpful configuration is associating SCon files with python and enabling format on save. If you enable this make sure you have clang-format and clang-tidy installed and set as the default formatter. To do so create the file `.vscode/settings.json` in the base repo folder. Below is an example config, make sure to replace all [PLACE HOLDERS].
```json
{
  "editor.formatOnSave": true,
  "files.associations": {
    "SCsub": "python",
    "SConstruct": "python",
    "SConscript": "python",
  }
}
```

## Scripts
We provide several scripts in the `scripts` folder. Some are meant to be run by actions. But some are also useful locally. All scripts are meant to be ran from the base repo folder.

- **clang_format.sh** This script will auto format all source files according to the Clang-Format rules.
- **clang_format.sh** This script checks generic file formatting rules, it works based off the current files staged in git. Not actual files. But will automatically update them (will not stage).
- **codespell.sh** This script requires [codespell](https://github.com/codespell-project/codespell) to be installed on your system. It will find common spelling mistakes in comments and string and automatically fix them.