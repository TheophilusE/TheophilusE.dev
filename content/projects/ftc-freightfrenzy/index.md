---
title: "FTC: Freight Frenzy"
date: 2021-09-01
tags: [ "Project", "Robotics", "FTC", "Java", "Path Planning" ]
author: ["Theophilus Eriata"]
description: "A complete Java‐based control system for a FIRST Tech Challenge Freight Frenzy robot, featuring path planning, sensor integration, and performance tuning."
summary: "This post walks through our Freight Frenzy season codebase, from build setup and drive‐train calibration to MeepMeep path visualization and autonomous strategy; highlighting architecture, key features, test results, and future directions."
editPost:
    URL: "https://github.com/TheophilusE/FTC_FreightFrenzy"
    Text: "GitHub Repository"
showToc: true
disableAnchoredHeadings: false
---

## Overview

We built a full-stack Android/Java control system for our Freight Frenzy FTC season. The code lives in the standard FTC Robot Controller SDK, extended with:

- A custom `TeamCode` module for our hardware mappings and behaviors
- MeepMeep path visualization to simulate and fine-tune autonomous routes
- Tunable force coefficients for drivetrain motors
- State-machine control for ring/puck intake and shipping hub scoring

This repo powers both teleop driver controls and a multi-stage autonomous routine optimized for shipping hub cycles.

## Motivation

Freight Frenzy challenges teams to pick up blocks and freight, navigate warehouse zones, and score on a shipping hub under 30 seconds of autonomy. We needed:

- Precise path‐following to avoid obstacles
- Tunable power models for battery aging/drivetrain friction
- A fast-recovery state machine in case of mis-alignment
- Easy simulation of trajectories before hitting the field

This drove us to integrate MeepMeep and develop a robust on-robot state architecture.

## Development Setup

1. Clone the FTC SDK and our fork:
   ```bash
   git clone https://github.com/TheophilusE/FTC_FreightFrenzy.git
   cd FTC_FreightFrenzy
   ```
2. Open in Android Studio (“Import Project (Gradle)”).
3. Plug in the REV Expansion Hub over USB-OTG.
4. Tune motor force coefficients in `TeamCode/src/main/java/constants/DriveConstants.java`.
5. Deploy via “Run > Deploy” to your Control Hub or Driver Hub.

## Software Architecture

### Module Breakdown

- **FtcRobotController** (root SDK)
- **TeamCode**
  - Hardware mappings: IMU, DC motors, servos, distance sensors
  - `AutoStateMachine`: sequences marker drop, freight intake, hub scoring
  - `DriveSubsystem`: kinematic wrapper with feedforward + PID
  - `TeleOpOpMode`: field-centric joystick drive + intake controls
- **MeepMeepPathVisualizer**
  - JSON-exported trajectories from on-robot code
  - Real-time visualization in desktop window

## Key Features

- Unpowered path planner with JSON export for simulation
- Force coefficient adjustments to match real motor torque curves
- Recovery states if element detection via optical distance sensor fails
- Field-centric mecanum drive for intuitive TeleOp handling

## Autonomous Routine

1. **Start** at pre-load shipping hub level (determined by AprilTag scan).
2. **Deliver** the duck marker.
3. **Cycle**:
   - Navigate to warehouse
   - Intake freight (3 blocks)
   - Return to shipping hub
   - Deposit on designated level
4. **Park** in warehouse to maximize endgame points.

```ascii
Top-down view (meters)
  ┌─────────────────────────────┐
  │ [Shipping Hub]   [Carousel]│
  │     ↑         →            │
  │   Start → → →   → ← ← ← ←  │
  │   ↓                      ↓ │
  │ [Warehouse Entrance]       │
  └─────────────────────────────┘
```

## Path Visualization

We integrated the MeepMeep SDK to simulate robot paths:

| Phase         | Waypoints                          | Simulated Time |
|---------------|------------------------------------|----------------|
| Pre-load drop | (0,0) → (1.2,0.8) → (1.0,1.6)      | 4.1s           |
| Warehouse run | (1.0,1.6) → (3.0,2.5)              | 3.8s           |
| Return score  | (3.0,2.5) → (0.8,1.4)              | 3.2s           |

This allowed on-field tuning of power curves and obstacle avoidance without repeated hardware trials.

## TeleOp Controls

- Left stick: field-centric translation
- Right stick X: rotation
- `A`/`B`: intake in/out
- `X`/`Y`: dumper up/down
- Bumpers: speed scaling (0.5×, 1×, 1.5×) for delicate / fast maneuvers

## Testing & Results

- 20+ hardware runs to tune force coefficients
- 95% success on first-cycle hub deposition
- Average 28s complete cycle time (target: <30s)
- Robust recovery from dropped freight in 2.3s on average

## Future Work

- Machine-vision alignment to shipping hub using OpenCV
- Dynamic trajectory replanning if blocked by opponent robot
- Integration with Road Runner for spline-based paths
- Expansion of MeepMeep plugin to visualize sensor error envelopes
