---
title: "XII FlyBird"
date: 2022-12-28
tags: ["Project", "Game Development", "Procedural Generation", "C++", "TypeScript"]
author: ["Theophilus Eriata"]
description: "A lightweight, infinitely replayable flying game featuring procedural obstacle generation, optimized effects, and console-variable-driven tuning."
summary: "This post unpacks XII FlyBird: project goals, architecture, procedural pipelines, performance optimizations, gameplay mechanics, technical challenges, and next steps."
cover:
    image: "Banner.png"
    alt: "Game Banner."
    relative: true
editPost:
  URL: "https://github.com/TheophilusE/XII-FlyBird"
  Text: "GitHub Repository"
showToc: true
---

## Overview

XII FlyBird is an easy-to-play endless flyer where you rack up points by soaring through procedurally generated gateways. Built in C++ with TypeScript scripting, FlyBird balances lightweight runtime performance with flexible in-game tuning via console variables.

{{< youtube 3dwLyYumrUw >}}

## Motivation

Designing a small-scale game often leads straight to “just get it working.” FlyBird’s goal was different: I wanted an endless runner that felt polished, ran smoothly on modest hardware, and supported live tweaking of gameplay parameters without recompiling. By combining low-level C++ core systems and high-level TypeScript hooks, I aimed to:

- Achieve a smooth 60 FPS on integrated GPUs.
- Expose key gameplay parameters (speed, gap size, obstacle density) through CVars.
- Build a simple finite-state animation framework for bird sprites.
- Recycle obstacles efficiently to keep memory usage constant.

## Core Features

- Procedural obstacle generation that guarantees random yet fair gate placement.
- Console vars (CVars) for real-time adjustment of player impulse, obstacle spacing, and camera shake.
- State-based animation driven by a finite state machine for smooth transitions.
- Low-level physics impulses for natural bird “flight” and gravity behavior.
- High-level scripting in TypeScript for obstacle behaviors and game flow control.

## Architecture

FlyBird is split into three layers:

1. **Engine Core (C++):**
   - Memory-pooled object system for reuse of obstacle and particle instances
   - Physics impulses for forward motion and flapping
   - CVar console subsystem for live-tuning of floats, ints, and booleans
2. **Scripting Layer (TypeScript):**
   - Defines obstacle spawn logic and random height ranges
   - Manages game states: **Menu → Playing → GameOver**
   - Binds CVar changes to in-game UI overlays
3. **Data & Assets:**
   - Prefabs for bird, gates, and background elements
   - Scriptable configuration in `RuntimeConfigs` for default CVar values
   - Editor plugins for rapid scene iteration

## Procedural Pipeline

The obstacle pipeline follows this flow:

```text
Seed RNG → Compute Next Gate Position → Instantiate or Recycle Obstacle → Apply Random Z-Offset → Begin Movement Toward Player
```

Key parameters exposed as CVars include:

| Parameter                  | Default | Description                                      |
|----------------------------|---------|--------------------------------------------------|
| cvar_PlayerSpeed           | 300.0   | Forward impulse magnitude.                       |
| cvar_PlayerFlapImpulse     | 450.0   | Upward impulse when “fly” input is active.       |
| cvar_ObstacleSpacing       | 75.0    | Distance between consecutive gates.              |
| cvar_ObstacleVerticalRange | 60.0    | Max vertical displacement for gate openings.     |

This approach ensures obstacles loop seamlessly and maintains constant memory overhead.

## Gameplay Mechanics

Players control the bird with a simple “fly” key or button:

- Hold “Fly” to apply upward impulse, counteracting gravity.
- Release to let gravity descend the bird naturally.
- Each passed gate awards one point; colliding ends the run.
- Dynamic difficulty scaling: after every 10 points, `cvar_ObstacleSpacing` decreases by 5%.

## Performance & Optimization

Through targeted optimizations, FlyBird consistently hits 60 FPS on entry-level hardware:

- Object pooling avoids frequent allocations/deallocations.
- Minimal per-frame TypeScript invocations; heavy logic runs natively.
- Simple shaders for background parallax and gate highlights.
- Batch-rendering of sprites to reduce draw calls.

Frame times measured over 10 runs:

| Hardware             | Average FPS | Peak Memory Footprint |
|----------------------|-------------|-----------------------|
| Intel UHD Graphics   | 60          | 45 MB                 |
| Integrated AMD GPU   | 60          | 50 MB                 |
| Mid-range dGPU (GTX) | 144         | 75 MB                 |

## Technical Challenges

- **Precision Loss Over Time:** Early pooling logic led to floating-point drift. Solved by resetting object positions relative to a fixed origin every 1 km traveled.
- **Live-Tuning Overhead:** Unbounded CVar updates caused hitches. Introduced a “dirty” flag so changes apply at frame boundaries.
- **State-Sync Between C++/TS:** Ensuring that script-driven obstacle logic and core physics remained in lock-step required a single “tick” dispatcher.

## Lessons Learned

- Embedding a lightweight scripting layer vastly improved iteration speed compared to pure C++ builds.
- Procedural generation works best when parameters are exposed early; waiting to tune in code wastes playtesting time.
- Consistent memory usage is critical for endless games; object pools keep the GC (or OS allocator) from becoming a bottleneck.

## Future Work

- Add shader-based post-processing (motion blur, depth fog) for visual polish.
- Implement online leaderboards with networked score submission.
- Mobile port with touch controls and adjustable CVar presets.
- VR/AR adaptation for immersive first-person flying.
