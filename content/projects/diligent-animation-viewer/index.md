---
title: "VisionSource: A Diligent GLTF Loader"
date: 2021-08-17
tags: [ "Project", "C++", "Game Engine", "Graphics", "Open Source", "Real-time Rendering"]
author: ["Theophilus Eriata"]
description: "VisionSource is the foundational C++ module of VisionEngine, offering component-based real-time rendering and shader management."
summary: "Core C++ library powering VisionEngine’s rendering pipeline with modular ECS and editor integration."
cover:
    image: "diligent-animation-viewer.png"
    alt: "Vision GLTF Viewer"
    relative: true
editPost:
    URL: "https://github.com/TheophilusE/VisionEngine"
    Text: "GitHub Repository"
showToc: true
disableAnchoredHeadings: false
---

{{< youtube W-iMmyA4-0I >}}

---

VisionSource serves as the heart of VisionEngine, an open-source, real-time C++ game engine. It encapsulates a modular, component-based architecture that drives entity transformations, rendering pipelines, and asset management; all orchestrated through a robust CMake-driven build system.

The Engine submodule introduces a flexible ECS framework, with components like Transform for hierarchical object positioning and Bullet conversions for physics interoperability. In parallel, the Shaders directory houses GLSL programs loaded at runtime, enabling dynamic material and lighting effects.

An integrated Editor streamlines development by allowing live tweaks to component properties and scene hierarchies, accelerating iteration and debugging. Throughout VisionSource’s development, I focused on performance optimization, memory management, and clean code organization, laying a solid foundation for larger engine features.
