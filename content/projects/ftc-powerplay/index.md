---
title: "FTC: PowerPlay"
date: 2022-09-01
tags: ["Project", "FTC", "Robotics", "Java", "Road Runner", "OpenCV"]
author: ["Theophilus Eriata"]
description: "A Java-based control system for the FIRST Tech Challenge PowerPlay season, featuring mecanum drive with Road Runner path planning, AprilTag-powered signal detection, and a resilient autonomous state machine."
summary: "This post covers our PowerPlay project end-to-end: mechanical design, software architecture, autonomous & TeleOp strategies, performance metrics, lessons learned, and the roadmap ahead."
editPost:
  URL: "https://github.com/TheophilusE/FTC_PowerPlay"
  Text: "GitHub Repository"
showToc: true
disableAnchoredHeadings: false
---

## Overview

PowerPlay (FTC 2022-2023) tasks robots with stacking cones onto grid junctions of four heights, creating circuits, and parking in randomized signal zones. We built on the official FTC SDK, adding:

- A custom `TeamCode` module for hardware mappings, vision, and game logic.
- Road Runner powered mecanum trajectories and MeepMeep simulation.
- AprilTag, contour, and shipping element detection via EasyOpenCV for autonomous signal recognition.
- A state-machine framework to handle stack cycles, recoveries, and parking.

This system drives both our multi-stage autonomous routine and a high-throughput TeleOp control scheme.

{{< youtube HsitvZ0JaDc >}}

## Motivation

Key PowerPlay challenges:

- Rapid, repeatable cone pickup and stacking at ground, low, medium, and high junctions.
- Autonomous parking based on a hidden signal cone for bonus points.
- Precise navigation through tight field corridors without collisions.
- A flexible TeleOp interface enabling 8+ cones per match.

Manual trajectory tuning and ad-hoc state logic proved brittle. By integrating Road Runner and a modular state machine, we achieved both precision and robustness.

## Hardware Setup

- Four-wheel mecanum drivetrain with NeveRest Orbital 20 motors.
- REV Control Hub (v1.15.6) + two REV Expansion Hubs.
- BNO055 IMU for field-centric orientation.
- 3D-printed claw with rubber-band tension.
- Web camera on a swivel mount for colour detection and April Tag reads.
- REV Distance Sensor for close-range cone alignment.

All ports and constants live in `TeamCode/src/main/java/constants/HardwareMap.java` for easy remapping.

## Software Architecture

- **FtcRobotController (SDK):** Core Android/Java framework
- **TeamCode:**
  - `DriveSubsystem` for field-centric motion
  - `AutoStateMachine` enumerating preload delivery, cycle loops, recoveries, and parking
  - `TeleOpOpMode` mapping joysticks, bumpers, and buttons to drive & slide actions
- **MeepMeepVisualizer:** JSON importer to preview and refine trajectories on desktop

This separation keeps vision, driving, stacking, and game logic in testable, interchangeable modules.

## Path Planning & Simulation

Road Runner lets us define spline sequences:

```java
TrajectorySequence autoPath = drive.trajectorySequenceBuilder(startPose)
    .addDisplacementMarker(() -> stacking.intakeCone())
    .splineTo(new Vector2d(1.2, 2.8), Math.toRadians(90))
    .addDisplacementMarker(() -> stacking.raiseTo(MEDIUM))
    .splineTo(new Vector2d(0.5, 1.0), Math.toRadians(180))
    .build();
drive.followTrajectorySequenceAsync(autoPath);
```

We export these trajectories to JSON for MeepMeep, revealing velocity curves and heading changes. On-field tuning runs dropped by over 50%.

## Autonomous Routine

1. **Signal Scan**
   - Shipping element pipeline detection identifies parking zone (Left/Center/Right).
2. **Preload Delivery**
   - Drive to medium junction & deposit preloaded cone.
3. **Cone Cycles (2×)**
   - Navigate to substation, intake a cone, return to high junction, stack.
   - Distance sensor confirms pickup; timeouts trigger recovery.
4. **Parking**
   - Follow spline to the target zone for a 10-point bonus.

The `AutoStateMachine` class orchestrates these phases, handling errors and timeouts gracefully.

## TeleOp Controls

- Left stick: field-centric translation.
- Right stick X: rotation.
- D-pad Up/Down: select slide level (ground/low/medium/high).
- Right bumper: claw grab/release.
- Left bumper: slide home.
- X/B: adjust slide ±10 ticks.
- A: quick 180° pivot.

Drive power, slide speed, and PID gains are exposed in `DriveConstants.java` and `SlideConstants.java`.

## Testing & Results

| Metric                          | Result                | Target            |
|---------------------------------|-----------------------|-------------------|
| Preload placement accuracy      | 96%                   | ≥95%              |
| Cycle time (pickup → stack)     | 10.2 s (avg)          | ≤12 s             |
| Full autonomous completion time | 28.5 s (avg)          | ≤30 s             |
| Parking success rate            | 100%                  | 100%              |

During TeleOp, our drivers averaged eight stacked cones and maintained precise slide positioning under match pressure.

## Lessons Learned

- Vision systems need per-match exposure tuning to handle gym lighting.
- Explicit error states in the state machine prevent mechanism jams.
- Dual odometry wheels drastically improves path-following repeatability.
- Continuous MeepMeep simulation saved hardware wear and accelerated strategy iteration.

## Future Work

- Add TensorFlow-based object detection for signal redundancy.
- Dynamic cycle-count adjustment based on remaining match time.
- Live trajectory replanning to avoid opposing-alliance congestion.
- Port vision & drive code to Kotlin for enhanced concurrency.
- Publish a post-season video dissecting match footage with overlayed trajectories.
