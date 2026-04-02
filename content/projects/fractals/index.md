---
title: "Fractals"
date: 2026-02-11
tags: ["Project", "C++", "OpenGL", "Computer Graphics", "Fractals"]
author: ["Theophilus Eriata"]
description: "A real-time fractal renderer, featuring five fractal generators implemented in modern C++ with OpenGL."
summary: "Implements Sierpiński, Menger, Koch, Hilbert, and Tree fractals with real-time rendering, per-vertex colour interpolation, and interactive controls."
cover:
    image: "images/banner.png"
    alt: "Game Banner."
    relative: true
editPost:
  URL: "https://github.com/TheophilusE/Fractal"
  Text: "GitHub Repository"
showToc: true
---

## Overview

This repository contains the implementation that generates and renders four fractal types (Sierpinski triangle, Menger carpet, Koch curve/snowflake variant, Tree, and Hilbert curve) using OpenGL.

{{< youtube urMfp7XUEAQ >}}

## Features

- Real-time rendering using OpenGL (GLFW + glad).
- Per-vertex colour interpolation for visualization.
- Keyboard controls to change fractal mode and iteration depth.

## Build

This project uses CMake. The thirdparty dependencies are included in the workspace, no external package manager is required.

Recommended (Ninja) steps from repository root:

```bash
mkdir build
cmake -S . -B build -G "Ninja"
cmake --build build --config Release
```

If you prefer Visual Studio generator (MSVC), for example Visual Studio 2026:

```bash
mkdir build
cmake -S . -B build -G "Visual Studio 18 2026"
cmake --build build --config Release
```

The produced binary is located under `build/` (the exact path depends on the generator and config). You can run it from the command line or via Visual Studio.

> The project is platform-agnostic and should build on Windows, Linux, and macOS with a suitable C++ toolchain and OpenGL support. Ensure your GPU drivers are up to date for best compatibility.

> My primary development environment was Windows 11 with Visual Studio 2026 and an NVIDIA/INTEL hybrid GPU setup with the latest MSVC compiler (Microsoft (R) C/C++ Optimizing Compiler Version 19.50.35724 for x64).

## Run

Run the compiled executable. The application opens a window and renders the currently selected fractal. Use the keyboard controls below to change settings interactively.

## Controls

- **Up / Down**: Increase / decrease the iteration depth (clamped automatically to avoid excessive memory usage).
- **M**: Cycle fractal mode: Sierpinski → Menger → Koch → Hilbert → Tree → Sierpinski.
- **R**: Re-generate the current fractal (useful after modifying shader code or parameters).

## Implementation Notes

- For clarity and convenience, the fractal generation logic is implemented in an in-file class `FractalGenerator` (inside `main.cpp`) that:
  - Encapsulates CPU-side vertex and colour buffers (`CPU_Geometry`) and uploads to `GPU_Geometry`.
  - Provides a small control API: `setMode()`, `incIteration()`, `decIteration()`, `regenerate()`, and helpers to query `drawMode()` and `vertexCount()`.
- The generator performs a conservative vertex estimate and reserves buffers to reduce reallocation overhead. Very large iteration requests are clamped to avoid exhausting memory/GPU resources.
- The render loop binds the `GPU_Geometry` owned by the generator and draws according to the generator's reported draw mode.

## Fractal references (citations)

- **Sierpiński triangle** - Origin: Wacław Sierpiński (1915).
  - https://en.wikipedia.org/wiki/Sierpi%C5%84ski_triangle
- **Menger carpet** - Origin: Karl Menger (1926).
  - https://en.wikipedia.org/wiki/Menger_carpet
- **Koch snowflake / Koch curve** - Origin: Helge von Koch (1904).
  - https://en.wikipedia.org/wiki/Koch_snowflake
- **Hilbert curve** (space-filling curve) - Origin: David Hilbert (1891).
  - https://en.wikipedia.org/wiki/Hilbert_curve
- **Tree fractal (branching / L-system style)** - Procedural branching tree commonly produced with iterative rules or L-systems. Related origins: Aristid Lindenmayer's L-systems (1968).
  - https://en.wikipedia.org/wiki/Fractal_tree
  - https://en.wikipedia.org/wiki/L-system

## Troubleshooting

- If CMake cannot find or build the thirdparty libraries, verify your generator (Ninja, Visual Studio, etc.) and that your environment contains a suitable C/C++ toolchain (MSVC, GCC, clang-cl, mingw, etc.).
- If you encounter shader compilation errors, the runtime prints shader link/compile logs to the console, so check the console output.
- If the application runs but shows a blank window, ensure sRGB and depth states are supported by your GPU/drivers. Updating GPU drivers on the machine can help.
