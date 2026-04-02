---
title: "Transformations"
date: 2026-02-27
tags: ["Project", "C++", "OpenGL", "Computer Graphics", "Transformations"]
author: ["Theophilus Eriata"]
description: "An implementatio of a 'Pirate Game', featuring textured rendering, transformation pipelines, collision logic, and gameplay mechanics."
summary: "Implements the complete Pirate Game using OpenGL 3.3, including player/enemy movement, cannonball physics, UI text, and shader-based transformations."
cover:
    image: "images/banner.png"
    alt: "Game Banner."
    relative: true
editPost:
  URL: "https://github.com/TheophilusE/Transformations"
  Text: "GitHub Repository"
showToc: true
---

## Overview

The program renders multiple textured objects, applies all transformations in the vertex shader, implements player/enemy movement, collision logic, cannonball physics, UI text, and restart functionality.

{{< youtube y8-FkxlQSFE >}}

## Keyboard Controls

**Player Movement**
* Left Arrow - Rotate ship counter‑clockwise
* Right Arrow - Rotate ship clockwise
* Up Arrow - Move forward in the direction the ship is facing
* Down Arrow - Move backward in the direction the ship is facing

**Combat**
* Spacebar - Fire cannon (0.5s reload time)

**Game Control**
* R - Restart the game after win/lose
* ESC - Quit the program

## Development Environment
* Operating System: Windows 11 / Linux Fedora 43
* Compiler / Toolchain: MSVC (Visual Studio 2022), Clang 20
* Graphics API: OpenGL 3.3 Core Profile
* Libraries Used: GLFW, GLAD, STB, FMT, GLM, ArgH, ImGui (provided in boilerplate)

**Compilation Instructions**
* Open the project folder in Visual Studio 2022.
* Ensure CMakeLists.txt is detected automatically.
* Configure and generate the build using the default preset.
* Build the project (Ctrl + Shift + B).
* Run the executable from Visual Studio or from the build directory.

## Troubleshooting

- If CMake cannot find or build the thirdparty libraries, verify your generator (Ninja, Visual Studio, etc.) and that your environment contains a suitable C/C++ toolchain (MSVC, GCC, clang-cl, mingw, etc.).
- If you encounter shader compilation errors, the runtime prints shader link/compile logs to the console, so check the console output.
- If the application runs but shows a blank window, ensure sRGB and depth states are supported by your GPU/drivers. Updating GPU drivers on the machine can help.
