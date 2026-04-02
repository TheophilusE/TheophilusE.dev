---
title: "Neural Networks and Reinforcement Learning: Practical Workshop Materials"
date: 2025-11-19
tags: ["Project", "Machine Learning", "Neural Networks", "Reinforcement Learning", "Control Systems", "Python"]
author: ["Theophilus Eriata"]
description: "A collection of practical demos, notebooks, and utilities for teaching neural networks and reinforcement learning through classical control comparisons and real-world examples."
summary: "Workshop materials covering PD control baselines, MLP policy imitation, double pendulum stabilization, gait generation, reward shaping, and lightweight environment wrappers."
cover:
    image: "images/frame-view.png"
    alt: "Balancing a single pendulum."
    relative: true
editPost:
  URL: "https://github.com/TheophilusE/NeuralNetworksAndReinforcementLearning"
  Text: "GitHub Repository"
showToc: true
disableAnchoredHeadings: false
---

## Overview

This project contains the full set of materials for my workshop on *practical approaches to neural networks and reinforcement learning*.
The goal is to teach these topics through hands-on, reproducible demos that compare classical control methods with learned policies.
The repository includes PD baselines, MLP policy implementations, environment wrappers, plotting utilities, and notebooks that illustrate the strengths and limitations of each approach.

{{< youtube XLf5uel90c8 >}}


## Motivation

Many introductions to reinforcement learning focus on theory or large frameworks.  
This workshop takes the opposite approach: start with simple, interpretable systems (pendulums, walkers, hoppers), build classical controllers, then show how neural networks can imitate or extend them.

We usually want to examine:
- when classical control is sufficient,
- when learned policies provide advantages,
- how reward shaping affects behavior,
- and how to evaluate stability, robustness, and trajectory quality.

## Key Learning Themes

### Classical Control vs. Neural Policies
Each demo begins with a PD controller to establish a baseline.  
Neural networks are then trained to imitate or improve upon these controllers, making the comparison intuitive.

### Stability and Chaos
The double pendulum notebook highlights how small changes in initial conditions affect classical controllers and how learned policies respond differently.

### Reward Shaping
The walker/hopper demos show how reward design directly influences gait quality, stability, and exploration.

## Technical Stack

- Python, for all demos, models, and utilities.
- PyBullet, for physics simulation.
- NumPy / PyTorch, for neural network policies.
- Jupyter Notebooks, for comparative analysis.
- Matplotlib, for plotting trajectories and reward curves.
- React/Vite, for frontend modelling.

## Intended Audience

This workshop is designed for:

- Students learning RL for the first time.
- Engineers who want practical intuition before diving into large frameworks.
- Researchers who want simple baselines for experimentation.
- Anyone interested in the relationship between classical control and learned policies.

## License

This project is licensed under the MIT License. See the [LICENSE](https://github.com/TheophilusE/NeuralNetworksAndReinforcementLearning/blob/Development/LICENSE) file for details.
