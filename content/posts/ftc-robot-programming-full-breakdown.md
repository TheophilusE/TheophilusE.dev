---
title: "FTC PowerPlay Robot Programming - Full Breakdown"
date: 2023-07-23
weight: 998
draft: false
summary: "Details the full code breakdown of systems and processes implemented in the robot used for the FTC 2022-2023 (Power Play) season."
authors: = ["Theophilus Eriata"]
tags: ["Blog", "FTC"]
categories: ["Blog","FTC"]
math: true
#aliases: [/whatalias]
featuredImage: "/images/FIRST_Horz_RGB.jpg"
#images: ["/images/hello-world.gif"]
---

### Contents

- [Introduction](#introduction)
- [Overview](#overview)

# Introduction

This post documents the programming process of an advanced robot for the FTC 2022-2023 (Power Play) season.

This year (2022-2023), I mentored an all-girls robotics team where I got to share the foundations of a strong robotics team based on my 3-year experience as part of a robotics team, and 2-year experience as a mentor.

# Overview

The topics I covered included Mathematics, Robotics, Computer Science and the art of Programming.

#### Mathematics

1. This included an introduction to Linear Algebra, specifically to do with two and three dimensional vectors and transformations.

    * It is used to deduce which colour the camera detects by assuming each color to be a point in three-dimensional space. This means that we can have multiple points within that space as our target points, and just calculate which target point is closest to the read colour value.

    * It is used in autonomous programs to plan the robot’s path during the autonomous periods, perform robot localization, cruise control, and optimize velocity and acceleration control.

    * It is used in the driver controlled programs (TeleOp) to perform Field Relative control. This is where the robot's rotation is decoupled from the robot's translation.

2. Trigonometry In Differential Drives.

    * This is used in our differential drive control where the four wheels are categorized into 2 groups which balances the movement vector. This means that with Sine and Cosine, we are able to calculate the optimal force from the x and y input from the gamepad on the robot being mapped to the Unit Circle. This works out given that sine and cosine are complementary to each other.

    * This is also used in the optimal heading calculation for field-centric drive modes, where the movement of the robot is decoupled from the rotation of the robot to allow independent control axis of control of the robot.

3. Colour Space Theory.

    * This touches on the various ways color can be represented. We touched on the YCrCb, RGB, and HSV color spaces. This can be googled to see what the differences are. Currently only RGB and YCrCb models exist in the pipeline.

    * Links:
        - [YCbCr - Wikipedia](https://en.wikipedia.org/wiki/YCbCr)
        - [Why use HSV Colour Space In Vision and Image Processing - Stack Exchange](https://dsp.stackexchange.com/questions/2687/why-do-we-use-the-hsv-colour-space-so-often-in-vision-and-image-processing)
        - [What is YCBCr? - RW Designer](http://www.rw-designer.com/color-space)

#### Software Development

1. Opmode by Composition.

    * This encourages the programmer to think abstractly and separate their logic into components. It means that a programmer writes specialized functionality in components and easily adds that component functionality to any opmode without code duplication. For instance the Manual Drive has no specialized code except supplying components with input data or telling it to do something. The lift, drive, claw are all subsystems or components. See [ManualDrive.java](https://github.com/TheophilusE/FTC_PowerPlay/blob/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode/opmode/manual/ManualDrive.java) for details, including subsystems in the hardware directory.

    <br />

2. Finite State Machines.

    * This is used in the autonomous programs to build out the various states that the robot can be in at any given moment. This means that the robot has exactly only one state amongst a variety of states which are defined. For instance, if the robot has the following states $\lbrace Run, Walk, Jump, Idle\dots\rbrace$ the robot can only be in one of the aforementioned states. See [AutonomousDrive.java](https://github.com/TheophilusE/FTC_PowerPlay/blob/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode/opmode/autonomous/AutonomousDrive.java) to see this in action.

    <br />

3. Computer Vision and Object Detection.

    * The girls learnt how robots can use a physical camera to detect colored objects using OpenCV. What we do is that we sample a small region of the camera's image per frame (one frame has only 1 image), then find the average color values which are both in YCrCb (converted) and RGB (read) and determine which color it is closest to. YCrCb enables us to decouple the luminance value of the color from the color values themselves. See [ShippingElementPipeline.java](https://github.com/TheophilusE/FTC_PowerPlay/blob/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode/vision/pipeline/ShippingElementPipeline.java) for more details.

    <br />

4. Principles of Software Development and Programming.

    * They have an overview on how the build process operates and what must be done in order to get the program onto the robot.

    * They learnt how to write code in java, using both Object Oriented and Procedural Programming.

    * They learnt code collaboration with git where they're more able to individually make code changes and merge them together seamlessly.

    * They learnt version control using Git and GitHub as a way of backing up code as a series of revisions. This method is industry standard, compared to making multiple archives (.zip, .tar.gz, etc) as those do not scale well.

    * They learnt a tool, Fork, that they use to push, pull and commit code to our cloud-based repository.

    * They learnt how to use Android Studio, which is a powerful industry tool for building android applications.

    * And more...


#### Development Tools

* [Android Studio](https://developer.android.com/studio). An Integrated Development Environment (IDE) for Android app development.
* [Git](https://git-scm.com/). A free and open source distributed version control system.
* [Fork](https://git-fork.com/). A user-friendly Git client with free evaluation (permanent as of writing).
* [GitHub](https://github.com/). A cloud-based Git repository hosting service.

Checkout the source code on [GitHub](https://github.com/TheophilusE/FTC_PowerPlay/tree/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode).