---
title: "Empowering an All-Girls FTC Team: Math, Code & Robotics in Power Play"
date: 2023-07-23
tags: [ "Blog", "FTC", "Mentorship", "Linear Algebra", "Control Systems", "Computer Vision", "Finite State Machine", "Software Engineering"]
author: ["Theophilus Eriata"]
description: "During the FTC 2022–2023 Power Play season, I mentored a brand-new all-girls robotics team (Team 21595) through hands-on workshops in linear algebra, control theory, computer vision, and collaborative software engineering. Over eight months, they learned to build a competition-ready robot, tune PID loops, and deploy vision pipelines, all while mastering Git workflows and Java subsystems."
summary: "An in-depth look at mentoring an all-girls FIRST Tech Challenge team, covering linear algebra, control theory, vision pipelines, software design, and collaborative workshops."
cover:
    image: "POWERPLAY_Logo_Horiz_RGB_FullColor.png"
    alt: "FIRST Tech Challenge Logo"
    relative: true
editPost:
    URL: "https://github.com/TheophilusE/FTC_PowerPlay"
    Text: "GitHub Repository"
showToc: true
disableAnchoredHeadings: false
math: true
---

## Introduction

Looking back on the 2022–2023 FTC “Power Play” season, what stands out isn’t just the robot we built, but the journey we shared. When I first met our all-girls team, they were curious but tentative, with some uncertainty whether gears and code could ever feel like their own language. Over ten months, I witnessed that hesitancy transform into bold experimentation, late-night troubleshooting, and the thrill of watching a vision pipeline finally detect an object on the field.

From day one, I aimed to teach more than formulas. Yes, we unpacked the Euclidean norm for color filtering and PID control loops for stable driving, but we also paused to discuss how failure sparks creativity and how collaboration can outpace any solo effort. We held reflection circles after each build session and milestone, asking questions like “What worked and what didn't?” and “How can we overcome a setback?” These moments nurtured empathy, resilience, and a shared sense of ownership over every line of code and every physical part of the robot.

Mentoring this team reshaped my own views on leadership and learning. I learned to listen beyond technical questions; tuning in to frustrations about imposter syndrome, celebrating small breakthroughs in confidence, and guiding the team through moments of doubt. Their growing self-belief reminded me that real STEM education thrives when technical rigor and emotional support walk hand in hand.

In the following sections, I’ll explore how we wove theory with practice, balanced individual growth with collective achievements, and built a culture where every member felt both challenged and supported. This holistic approach ignited a lasting passion for engineering in a group of young women who now see themselves as creators, problem-solvers, and pioneers.

## Groundwork for Computer Vision in Robotics

In this section, we’ll unpack the foundational concepts that turned raw camera feeds into reliable, robot-usable data. We’ll explore how we moved from pixels to field coordinates, the mathematical choices that drove our design, and the hands-on activities that cemented these ideas in the students’ minds.

### The Mathematics of Color Detection

Every computer vision pipeline begins with distinguishing “what matters” from background noise. For our FTC robot, that meant singling out game elements (e.g., colored cones and cubes) against a busy field.

#### Why RGB? Pros and Cons

- RGB is the camera’s native output, so no initial conversion saves compute cycles.
- However, brightness changes (e.g., shadows under the field lights) warp RGB distances, leading to false positives.

To address this, we:

1. Briefly introduced **HSV color space** and showed how decoupling Hue from Value can make thresholds more stable.
2. Had students capture sample frames under different lighting, then manually adjust RGB and HSV thresholds to see which was more robust.

This juxtaposition taught them that math choices aren’t abstract, they’re dictated by real-world constraints.

#### From Pixel Vector to Distance Metric

Representing a pixel as
$
\vec p = \langle R,G,B \rangle
$
lets us apply well-known vector operations:

- **Euclidean norm**: intuitive, fast, but treats each channel equally.
- **Weighted norm**: by introducing a diagonal weight matrix \(W\),
  $$
  \lvert d \rvert = \sqrt{(\mathbf{p}-\mathbf{t})^T W (\mathbf{p}-\mathbf{t})},
  $$
  we could weight individual channels based on the dominant color characteristics of our target region (e.g., cones clustering in the green channel).

Students examined lightweight evaluation scripts to compare the performance of both the Manhattan and Euclidean norms on representative image data. By quantifying pixel-wise differences between targets and noise, they observed how each norm responded to artifacts and variability. The exercise demonstrated how norm selection directly impacts filter sensitivity and robustness

### Building the Thresholding Pipeline

Turning distance measurements into a binary mask involved more than a single cutoff. We designed a multi-stage filter:

1. Global threshold.
   - Pixels with $d < \varepsilon$ become candidates.
   - Students experimented to choose $\varepsilon$ via live telemetry of false positives vs. missed detections.
2. Morphological cleanup.
   - Apply erosion then dilation to remove speckles.
   - We discussed how kernel size trades off removing small noise vs. eroding actual objects.
3. Blob analysis.
   - Label connected components and compute area, aspect ratio, and solidity.
   - Only blobs within expected size/shape ranges moved forward.

At each step, students ran the pipeline, adjusting parameters and immediately seeing the effect on both simulated and real camera frames. This iterative loop cemented the idea that vision is as much about tuning as it is about theory.

### Camera Calibration and Intrinsics

Raw pixel coordinates $(u, v)$ don’t map directly to metric distances. We can perform a one-day calibration, which includes:

- Printing a checkerboard poster, taping it to a flat surface.
- Using OpenCV routines to estimate the intrinsic matrix $K$ and distortion coefficients.

Key takeaways for students:

- **Focal length** and **principal point** define how rays from real-world points intersect the sensor.
- **Lens distortion** (radial and tangential) can warp straight edges; if uncorrected, a straight line on the field looks curved in the image.

After calibration, every frame will be first undistorted, then pixel coordinates can be normalized as
<div>
$$
\begin{bmatrix}x_c \\ y_c \\ 1\end{bmatrix} = K^{-1} \begin{bmatrix}u \\ v \\ 1\end{bmatrix}.
$$
</div>

> This step is non-negotiable as failing to undistort can lead to systematic errors in distance estimates.

### Extrinsic Transforms and Homogeneous Coordinates

Detecting an object in the camera frame is only half the battle. To drive toward it, the robot needs that point in its own coordinate system.

#### Rotation and Translation in 2D

We broke the problem into digestible chunks:

- **Rotation**: taught via a turntable demo; placing a square marker, rotating it by hand, and measuring how its coordinates change.
- **Translation**: visualized by sliding the marker along a ruler and tracking the offset.

Putting them together, we have obtain

<div>
$$
R(\theta) =
\begin{bmatrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta
\end{bmatrix}
\quad
\vec{t} = \begin{bmatrix}t_x \\ t_y \end{bmatrix}.
$$
</div>

We can now transform a point $\bigl(x_c,y_c\bigr)$ into robot space with

<div>
$$
\begin{bmatrix}x_r\\y_r\end{bmatrix}
= R(\theta)\begin{bmatrix}x_c\\y_c\end{bmatrix} + \mathbf{t}.
$$
</div>

#### Homogeneous Coordinates for Chaining

To combine undistortion, rotation, and translation in one stroke, we introduce a new $3×3$ homogeneous matrix

<div>
$$
H = \begin{bmatrix}
r_{11} & r_{12} & t_x\\
r_{21} & r_{22} & t_y\\
0 & 0 & 1
\end{bmatrix}.
$$
</div>

By multiplying

<div>
$$
\begin{bmatrix}x_r\\y_r\\1\end{bmatrix}
= H
\begin{bmatrix}x_c\\y_c\\1\end{bmatrix},
$$
</div>

which shows us how multiple transforms collapse into a single matrix multiplication, which is crucial for efficient, real-time pipelines.

### Hands-On Reinforcement

Throughout these modules, we wove in exercises that bridged theory and practice:

- Visualizing pixel vectors in color space to identify clusters.
- Building simple GUIs and telemetry sliders which controlled $\varepsilon$, kernel size, or rotation angle, and the students saw immediate feedback on the camera feed.

By the end of these groundwork sessions, the students had the intuition of tracing a pixel from the raw camera image all the way to a precise robot-centric $(x_r, y_r)$ pair, complete with error bounds. This mathematical and experimental preparation laid the foundation for the later control and automation stages, ensuring our vision system was both accurate and robust under competition pressures.

## Shipping Element Pipeline: Detecting the Park Signal

In the autonomous period, the robot must read the “shipping element” signal (one of three colored markers) to know where to park. Our `ShippingElementPipeline` uses Y′CbCr color space conversion, a center‐region sampling strategy, and Euclidean‐distance matching in both RGB and Y′CbCr to robustly classify the signal. Here’s how it was implemented in practice.

### RGB $\to$ Y′CbCr Conversion

While the camera outputs RGB, separating luminance (Y′) from chrominance (Cb / Cr) makes color detection far less sensitive to lighting changes. We implement the standard BT.601 transform

<div>
$$
\begin{bmatrix}
Y'\\
Cb\\
Cr
\end{bmatrix}
=
\begin{bmatrix}
0.299 & 0.587 & 0.114\\
-0.168736 & -0.331264 & 0.5\\
0.5 & -0.418688 & -0.081312
\end{bmatrix}
\begin{bmatrix}
R\\
G\\
B
\end{bmatrix}
+
\begin{bmatrix}
0\\
128\\
128
\end{bmatrix}.
$$
</div>

In code, OpenCV handles this via:

```java
Imgproc.cvtColor(input, yCrCbMat, Imgproc.COLOR_RGB2YCrCb);
```

### Defining the Region of Interest

We sample a small box at the center of the frame (where the signal is expected) of size $W\times H$

```java
Rect centerSampleRect = new Rect(
  (int)(0.5 * input.width()),
  (int)(0.5 * input.height()),
  centerMarkerPositionWidth,
  centerMarkerPositionHeight
);
Mat sampleYCrCb = yCrCbMat.submat(centerSampleRect);
Mat sampleRGB   = input.submat(centerSampleRect);
```

By computing the mean color in both spaces

```java
Scalar meanYCrCb = Core.mean(sampleYCrCb);
Scalar meanRGB   = Core.mean(sampleRGB);
```

we get two 3D-vectors $\vec c_{\rm YCrCb}$ and $\vec c_{\rm RGB}$.

### Color Matching from Euclidean Distance

We pre‐define three target colors in RGB as
$$
\vec t_i = (R_i,G_i,B_i),\quad i\in\{1,2,3\}.
$$
Their Y′CbCr equivalents $\mathbf{t}_i'$ are computed once in the constructor using our `FastMath.convertRGB2YCRCB(...)`. At runtime, we pick the closest color by minimizing

$$
d_i = \|\vec c_{\rm RGB} - \vec t_i\|
    = \sqrt{\sum_{k\in\{R,G,B\}} (c_k - t_{i,k})^2}.
$$

A simpler (and faster) variant uses squared distance to avoid the square root

$$
d_i^2 = \sum_{k}(c_k - t_{i,k})^2.
$$

In Java code we obtain the following.

```java
double fBestDistance        = fMinThresholdValue;
ParkTargetSignal bestSignal = SIGNAL_NONE;

for (Pair<ParkTargetSignal,Vector3d> pair : signalColorPairs)
{
  double fDistance = rgbFinalColor.distance(pair.snd);

  if (fDistance < fBestDistance)
  {
    fBestDistance = fDistance;
    bestSignal    = pair.fst;
  }
}

targetSignal = bestSignal;
```

### Debug Overlays & Final Output

To aid tuning, we draw the sampling rectangle in a color matching the detected signal.

```java
Scalar vOverlayColor;
switch (targetSignal)
{
  case SIGNAL_ONE:   vOverlayColor = new Scalar(41,118,187);  break;
  case SIGNAL_TWO:   vOverlayColor = new Scalar(51,184,100);  break;
  case SIGNAL_THREE: vOverlayColor = new Scalar(253,89,86);   break;
  default:           vOverlayColor = new Scalar(255,255,255); break;
}
Imgproc.rectangle(input, centerSampleRect, vOverlayColor, 2);
```

We also render a text label at $(0.5w, 0.2h)$

```java
Imgproc.putText(input,
  getLabelForSignal(targetSignal),
  new Point(0.5 * input.width(), 0.2 * input.height()),
  Imgproc.FONT_HERSHEY_COMPLEX, 1.0,
  new Scalar(255,255,255)
);
```

### Key Takeaways

- Converting to Y′CbCr decouples brightness from color, improving robustness under variable lighting.
- Sampling a fixed center region reduces noise from distracting background elements.
- Euclidean distance in RGB (or squared distance) works well when target colors are well separated.
- Debug overlays and real‐time tuning (via FTC Dashboard) make threshold and region adjustments intuitive.

By combining these mathematical and programming techniques, the team learned to tune a vision pipeline end-to-end; linking linear algebra, color theory, and practical software engineering in a single, competition-ready module.

## Contour Pipeline: Detecting and Tracking the Largest Region

This pipeline thresholds an image in Y′CrCb to isolate a target color (hot pink by default), cleans up noise with morphology, finds all contours, and then selects and highlights the largest valid bounding rectangle each frame. It also draws borders, overlays contour outlines, and exposes getters for rectangle properties.

### Color Thresholding in Y′CrCb

The input RGB frame is first converted into Y′CrCb.

```java
Imgproc.cvtColor(input, mat, Imgproc.COLOR_RGB2YCrCb);
```

We then apply an inRange threshold using two configurable `Scalar(y, Cr, Cb)` bounds.

```java
Core.inRange(mat, scalarLowerYCrCb, scalarUpperYCrCb, processed);
```

This produces a binary mask (`processed`) where pixels within the color range become white ($255$) and others black ($0$).

### Morphological Noise Removal

To remove spurious spots and holes, we chain three operations on the binary mask:

| Operation       | OpenCV Call                                    | Purpose                           |
|-----------------|------------------------------------------------|-----------------------------------|
| Opening         | `morphologyEx(..., MORPH_OPEN, new Mat())`     | Remove small white speckles.      |
| Closing         | `morphologyEx(..., MORPH_CLOSE, new Mat())`    | Fill small black gaps.            |
| Gaussian Blur   | `GaussianBlur(..., new Size(5,15), 0)`         | Smooth mask edges, reduce jitter. |

The Gaussian Blur formula is given as
$$
I'(x,y) = \sum_{i=-k}^{k}\sum_{j=-k}^{k} I(x+i,y+j)\frac{1}{2\pi\sigma^2}e^{-\frac{i^2+j^2}{2\sigma^2}}.
$$

These steps yield a cleaner, blur‐smoothed mask ready for contour detection.

### Contour Detection and Filtering

Contours are extracted from the cleaned mask.

```java
List<MatOfPoint> contours = new ArrayList<>();
Imgproc.findContours(processed, contours, new Mat(), RETR_LIST, CHAIN_APPROX_SIMPLE);
```

We then loop through each `contour` and:

- Convert to `MatOfPoint2f` for bounding‐rectangle calculation.
- Ignore contours with fewer than 15 points.
- Compute `Rect rect = Imgproc.boundingRect(areaPoints)`.

Only rectangles that
  - exceed the previous `maxArea`,
  - lie fully inside the fractional borders (`borderLeftX`, `borderRightX`, etc.),
  - or appear after six frames without a larger detection,

are considered the new `maxRect`.

### Maintaining the Largest Valid Region

We track:

- `maxArea`: area of the currently chosen rectangle.
- `maxRect`: its coordinates and dimensions.
- `loopCounter` & `pLoopCounter`: frame counters to force updates if no larger contour appears.

Logic flow:

1. If `rect.area() > maxArea` and within borders $\to$ update `maxRect`.
2. Else if `loopCounter - pLoopCounter > 6` $\to$ accept the latest valid `rect` anyway.
3. If no contours found $\to$ reset `maxRect` to an empty `Rect()`.

This prevents the pipeline from “sticking” on an outdated region.

### Debug Overlays and Display

Once `maxRect` is chosen:

- Draw **all** contours in blue.

  ```java
  Imgproc.drawContours(input, contours, -1, new Scalar(255, 0, 0));
  ```

- Highlight the largest rectangle in green (if `area() > 500`).

  ```java
  Imgproc.rectangle(input, maxRect, new Scalar(0, 255, 0), 2);
  ```

- Draw the user‐configurable border box in hot‐pink.

  ```java
  Imgproc.rectangle(input, borderRect, HOT_PINK, 2);
  ```

- Overlay text showing area and midpoint.

  ```java
  Imgproc.putText(input,
    "Area: " + getRectArea() +
    " Mid: " + getRectMidpointXY().x + "," + getRectMidpointXY().y,
    new Point(5, height-5), FONT_HERSHEY_SIMPLEX, 0.6,
    new Scalar(255,255,255), 2);
  ```

### Public Accessors

The pipeline exposes synchronized getters so the OpMode can safely read rectangle data.

- `getRectX()`, `getRectY()`
- `getRectWidth()`, `getRectHeight()`
- `getRectArea()`
- `getRectMidpointX()`, `getRectMidpointY()`, `getRectMidpointXY()`
- `getAspectRatio()`

These let us make decisions (e.g., distance estimation or alignment) based on the detected region.

### Key Insights

- Thresholding in Y′CrCb decouples brightness (Y′) from chroma, improving color stability.
- Morphology + blur yields cleaner masks for reliable contour extraction.
- Frame‐based counters avoid stale detections when contours temporarily disappear.
- Synchronized getters prevent race conditions between camera thread and main OpMode thread.
- Configurable borders let you ignore irrelevant regions (e.g., robot arms or field elements).

#### Further Exploration

- Try adaptive thresholding in HSV for dynamic lighting conditions.
- Implement contour hierarchy filtering to ignore nested or hole contours.
- Use a Kalman filter on `getRectMidpointXY()` for smoother tracking.
- Experiment with Canny‐based edge detection before contour finding to focus on shape over color.

## Control Theory for Motion Stabilization and Path Tracking

In robotics, precise movement hinges on closed-loop control. The robot measures its state, compares it to a desired state, and computes actuator commands to correct errors. For our FTC team, we focused on PID loops for turret and drive stabilization, plus motion profiling with feedforward terms to smoothly follow trajectories.

### Modeling a Feedback Loop

A control loop has three core elements:
1. **Setpoint** $r(t)$: the desired value (e.g., target angle or velocity).
2. **Process variable** $y(t)$: the measured value from sensors (e.g., encoder ticks).
3. **Controller** $C$: computes command $u(t)$ based on error $e(t) = r(t) - y(t)$.

Block diagram:

```
     ┌─────────────────┐    u(t)     ┌───────────┐    y(t)    ┌───────────┐
r(t)─┤   Controller    ├───────────▶ │   Plant  ├───────────▶│  Sensor  ├─┐
     └─────────────────┘             └───────────┘            └───────────┘
           ▲                                                           │
           │                       ┌───────────┐                       │
           └───────────────────────┤ Comparator├───────────────────────┘
                                   └───────────┘
                                   e(t)=r(t)-y(t)
```

### PID Controllers: Theory & Discrete Implementation

A PID (Proportional–Integral–Derivative) controller computes:
$$
u(t) = K_p \cdot e(t) + K_i\int_0^t e(\tau)d\tau + K_d\frac{de(t)}{dt}.
$$

- **Proportional** ($ K_p e $) reduces present error.
- **Integral** ($ K_i\int e $) eliminates steady-state offset.
- **Derivative** ($ K_d \,d e/dt $) anticipates future error, adding damping.

On a digital system with sampling period $\Delta t$, we approximate
<div>
$$
\begin{aligned}
e_k &= r_k - y_k,\\
I_k &= I_{k-1} + e_k\,\Delta t,\\
D_k &= \frac{e_k - e_{k-1}}{\Delta t},\\
u_k &= K_p e_k + K_i I_k + K_d D_k.
\end{aligned}
$$
</div>

#### Java Snippet with FTC Dashboard Tuning

```java
// In OpMode initialization
PIDController pid      = new PIDController(0.1, 0.01, 0.005);
FtcDashboard dashboard = FtcDashboard.getInstance();
dashboard.setTelemetryTransmissionInterval(50);

// Inside loop
double fTargetAngle  = 90.0;          // degrees
double fCurrentAngle = turret.getAngle();
double fPower        = pid.update(fTargetAngle - fCurrentAngle);
turret.setPower(fPower);

// Tune live via Dashboard transformers:
dashboardTelemetry.addData("Kp", pid.getKp());
dashboardTelemetry.addData("Ki", pid.getKi());
dashboardTelemetry.addData("Kd", pid.getKd());
dashboardTelemetry.update();
```

Students performed **Ziegler–Nichols tuning**, which includes the following.
1. Disable $I$ and $D$ terms, increase $K_p$ until oscillation at $K_{u}$.
2. Record oscillation period $T_{u}$.
3. Set gains as follows
   $$
   K_p = 0.6 K_u, \quad
   K_i = \frac{1.2 K_u}{T_u}, \quad
   K_d = 0.075 K_u T_u.
   $$

### Feedforward & Motion Profiling

A pure PID loop can lag when following rapid setpoint changes. We introduced **feedforward**
$$
u(t) = F_v v(t) + F_a a(t) + \text{PID}(e(t)),
$$
where
- $v(t)$ is desired velocity,
- $a(t)$ is desired acceleration,
- $F_v$ and $F_a$ are empirically determined constants.

#### Trapezoidal Velocity Profile

For smooth starts/stops, we plan a trajectory with three phases.

1. **Acceleration**:
   $v(t)=a_{\max}t,\quad x(t)=\tfrac12a_{\max}t^2$
2. **Cruise**:
   $v(t)=v_{\max},\quad x(t)=x_1 + v_{\max}(t-t_1)$
3. **Deceleration**:
   $v(t)=a_{\max}(T-t),\quad x(t)=x_2 + v_{\max}(t-t_2)-\tfrac12a_{\max}(t-t_2)^2$

We provide a simple profile generator,

```java
public class MotionProfile
{
  double aMax, vMax, xTotal;
  double t1, t2, T;

  public MotionProfile(double aMax, double vMax, double xTotal)
  {
    this.aMax = aMax;
    this.vMax = vMax;
    this.xTotal = xTotal;

    // Compute transition times
    t1             = vMax / aMax;
    double xAccel  = 0.5 * aMax * t1 * t1;
    double xCruise = xTotal - 2 * xAccel;

    if (xCruise < 0)
    {
      // Triangular profile fallback (no cruise)
      t1 = Math.sqrt(xTotal / aMax);
      t2 = t1;
      T  = 2 * t1;
    }
    else
    {
      double tCruise = xCruise / vMax;
      t2             = t1 + tCruise;
      T              = t2 + t1;
    }
  }

  public MotionState getState(double t)
  {
    if (t < t1)
    {
      double v = aMax * t;
      double x = 0.5 * aMax * t * t;
      double a = aMax;
      return new MotionState(x, v, a);
    }
    else if (t < t2)
    {
      double v = vMax;
      double x = 0.5 * aMax * t1 * t1 + vMax * (t - t1);
      return new MotionState(x, v, 0.0);
    }
    else if (t < T)
    {
      double td = t - t2;
      double v  = aMax * (T - t);
      double x  = 0.5 * aMax * t1 * t1 + vMax * (t2 - t1)
                  + vMax * td - 0.5 * aMax * td * td;
      double a  = -aMax;
      return new MotionState(x, v, a);
    } else
    {
      // End of profile
      return new MotionState(xTotal, 0.0, 0.0);
    }
  }
}
```

with motion state

```java
public class MotionState
{
  public final double m_fPosition;
  public final double m_fVelocity;
  public final double m_fAcceleration;

  public MotionState(double x, double v, double a)
  {
    m_fPosition     = x;
    m_fVelocity     = v;
    m_fAcceleration = a;
  }
}
```

which combined with PID and Feed Forward, we obtain the following.

```java
double t          = timer.seconds();
MotionState state = profile.getState(t);

double ff = Fv * state.m_fVelocity + Fa * state.m_fAcceleration;
double fb = pid.update(state.m_fPosition - currentPosition);

motor.setPower(ff + fb);
```

> Note that Fv and Fa are the feed forward gains for velocity and acceleration.

### Key Takeaways

- Closed-loop control contrasts setpoints vs. actuals, correcting via feedback.
- Discrete PID implementation balances responsiveness (high $K_p$) with stability (appropriate $K_d$).
- Feedforward complements feedback for dynamic trajectory following.
- Motion profiles ensure smooth accelerations, reducing wheel slip and power spikes.
- Hands-on tuning and visualization cement theoretical concepts in a robotics context.

## Finite State Machines for Autonomous Logic

In a FTC autonomous routine, the robot must perform a sequence of discrete, reliable actions (e.g., detect a signal, drive to a location, manipulate game pieces, then park). A Finite State Machine (FSM) provides the clearest, most testable way to encode this behavior.

### Core Concepts of FSMs

- **State**: A named phase of operation (e.g., scanning a signal or spinning a carousel).
- **Transition**: The condition under which the robot moves from one state to the next (e.g., timer elapsed, sensor reading achieved).
- **Action**: The set of commands executed continuously while in a given state (e.g., follow a trajectory or set motor power).

A FSM ensures that only one state’s logic runs at a time, and makes transitions explicit, predictable, and easy to debug.

### Defining Our States

We use a generic `StateMachine<S,T>` where
- **S** = `AutonomousFSMState` (the set of named states).
- **T** = `AutonomousFSMTrigger` (the events that drive transitions).

```java
// Build the state machine once, on initialization.
m_StateMachineConfig = new StateMachineConfig<>();

m_StateMachineConfig
    .configure(IDLE_STATE)
      .onEntry(this::enterIdleState)
      .permit(IDLING,     EVALUATE_VISION);

m_StateMachineConfig
    .configure(EVALUATE_VISION)
      .onEntry(this::evaluateVision)
      .permit(NO_PARK_SIGNAL_FOUND,     DEFAULT_PARK)
      .permit(VALID_PARK_SIGNAL_FOUND,  PARK_IN_SIGNAL_ZONE);

m_StateMachineConfig
    .configure(DEFAULT_PARK)
      .onEntry(this::scheduleDefaultPark);

m_StateMachineConfig
    .configure(PARK_IN_SIGNAL_ZONE)
      .onEntry(this::scheduleAutonomousParkSignal);

m_StateMachine = new StateMachine<>(initialState, m_StateMachineConfig);
m_StateMachine.fire(IDLING);
```

- **IDLE_STATE**
  + Entry: `enterIdleState()`.
  + Trigger: after waiting $\to$ `IDLING` $\to$ **EVALUATE_VISION**.

- **EVALUATE_VISION**
  + Entry: `evaluateVision()`
  + Triggers:
     * `VALID_PARK_SIGNAL_FOUND` $\to$ **PARK_IN_SIGNAL_ZONE**.
     * `NO_PARK_SIGNAL_FOUND`    $\to$ **DEFAULT_PARK**.

- **DEFAULT_PARK**
  + Entry: `scheduleDefaultPark()` (open‐loop strafe).

- **PARK_IN_SIGNAL_ZONE**
  + Entry: `scheduleAutonomousParkSignal()` (direction/timing varies by signal).

### Idle & Vision Evaluation

```java
public void enterIdleState()
{
  driveEngine.setZeroPower();
  ElapsedTime wait = new ElapsedTime();
  while (wait.milliseconds() < 5000
         && getVisionState() == SIGNAL_NONE) { /* busy–wait */ }
  m_StateMachine.fire(IDLING);
}

public void evaluateVision()
{
  if (getVisionState() != SIGNAL_NONE)
  {
    m_StateMachine.fire(VALID_PARK_SIGNAL_FOUND);
  }
  else
  {
    m_StateMachine.fire(NO_PARK_SIGNAL_FOUND);
  }
}
```

- We zero the drive, then sample vision up to 5 seconds.
- On detecting a valid park signal (LEFT/MID/RIGHT), we fire `VALID_PARK_SIGNAL_FOUND`; otherwise we fallback to `NO_PARK_SIGNAL_FOUND`.

### Scheduling Default Park (No Signal)

```java
public void scheduleDefaultPark()
{
  DriveSubsystem drive = getComponent(DriveSubsystem.class);
  ElapsedTime t        = new ElapsedTime();
  // Strafe direction depends on alliance
  Vector3d dir = Defines.BLUE_ALLIANCE
                 ? new Vector3d( 1, 0, 0)
                 : new Vector3d(-1, 0, 0);
  while (t.milliseconds() < 1500)
  {
    drive.setMovementVector(dir);
    drive.updateMovementStateDifferentialTrigRC();
  }
  drive.stop(); // zero vector & update
}
```

- **Open‐loop timing:** 1.5 seconds at fixed power $\to$ distance $\approx$ V<sub>strafe</sub>×1.5.
- `updateMovementStateDifferentialTrigRC()` applies the Vector3d to wheel powers via our mechanum kinematics
  <div>
  $$
    \begin{aligned}
      P_{FL} &= v_y + v_x + \omega L,\\
      P_{FR} &= v_y - v_x - \omega L,\\
      P_{BL} &= v_y - v_x + \omega L,\\
      P_{BR} &= v_y + v_x - \omega L.
    \end{aligned}
  $$
  </div>

### Scheduling Park in Signal Zone

For each detected signal, we chain time‐based moves

```java
switch (signal)
{
  case SIGNAL_ONE:
    // Strafe left 1.38 s, then forward 1.3 s
    moveVector(new Vector3d(-1,0,0), 1380);
    moveVector(new Vector3d( 0,1,0), 1300);
    break;

  case SIGNAL_TWO:
    // Forward only
    moveVector(new Vector3d(0,1,0), 1500);
    break;

  case SIGNAL_THREE:
    // Strafe right then forward
    moveVector(new Vector3d(1,0,0), 1050);
    moveVector(new Vector3d(0,1,0), 1150);
    break;
}
```

Helper method:
```java
private void moveVector(Vector3d v, long ms)
{
  ElapsedTime t = new ElapsedTime();
  while (t.milliseconds() < ms)
  {
    drive.setMovementVector(v);
    drive.updateMovementStateDifferentialTrigRC();
  }
}
```

- **Pros:** Simple, no trajectory library needed.
- **Cons:** Distance = Power×Time $\to$ varies with battery level, friction, manufacturing tolerances.

### Transitioning to Trajectories

We also included commented‐out methods using Road Runner.

```java
public void scheduleAutonomousSignalTrajectory()
{
  TrajectorySequence traj = driveEngine.trajectorySequenceBuilder(...)
      .strafeLeft(24 * 2) // 48 in
      .forward(24 * 4)    // 96 in
      .build();
  schedule(new FollowTrajectorySequenceCommand(driveEngine, traj));
}
```

- **Metric distance** (inches) replaces time loops.
- **Closed‐loop control** (feedback + motion profiling) $\to$ repeatable paths under varying conditions.

### Lessons & Improvements

1. **State Machine Clarity**
   - Explicit states and triggers keep autonomous logic maintainable and testable.

2. **Open-Loop vs. Closed-Loop**
   - Time‐based moves are easy but sensitive to battery and friction.
   - Trajectories yield consistent distances
     $$ d = \int_0^T v(t) dt, $$
     with feedforward-feedback control for velocity tracking.

3. **Non-Blocking FSM**
   - Current `enterIdleState()` busy-waits; better to schedule a timer event and return, letting the main loop continue calling `drive.update()`.

4. **Extensible Architecture**
   - New states (e.g., “deliver freight,” “cycle blocks”) slot in without rewriting existing transitions.
   - Error or retry states can handle vision failures or mechanical stalls.


By combining a strongly typed FSM with vector‐based kinematics or trajectory sequences, our Power Play autonomous routine remained robust, readable, and adaptable. This empowered the team to focus on strategy rather than tangled if-else blocks.

## Drive Subsystem

The Drive Subsystem encapsulates every aspect of robot locomotion. It bridges high-level commands and the low-level motor outputs, switching seamlessly between multiple control schemes, handling kinematics, and providing rich telemetry for tuning and debugging.

### Core Components

- `DriveEngine`: Interfaces with hardware and, when used, Road Runner to handle pose estimation, trajectory following, and raw power outputs.

- `Defines.DriveMode`: Selects among `ROBOT_CENTRIC_HOLONOMIC`, `ROBOT_CENTRIC_MECANUM`, `FIELD_CENTRIC_GAMEPAD`, and `FIELD_CENTRIC_IMU` modes.

- `Vector3d`: Holds desired translation $(x, y)$ and rotation $(z)$ inputs in the range $[-1, 1]$.

- `Telemetry`: Streams real-time data to the driver station for monitoring stick inputs, computed powers, heading, and offsets.

- `double`: Zero reference for IMU-based field centric; updated via `setHeading()` to recalibrate on the fly.

### Drive Mode Dispatch

Every update interval (FTC loop), `periodic()` routes control to the active mode.

```java
switch (m_driveMode)
{
  case ROBOT_CENTRIC_HOLONOMIC:
    updateMovementStateDifferentialRC();
    break;
  case ROBOT_CENTRIC_MECANUM:
    updateMovementStateDifferentialTrigRC();
    break;
  case FIELD_CENTRIC_GAMEPAD:
    updateMovementStateGamepadFC();
    break;
  case FIELD_CENTRIC_IMU:
    updateMovementStateIMUFC();
    break;
  default:
    m_DriveEngine.setZeroPower();
    m_Telemetry.addLine("> Unknown drive mode");
}
```

This structure isolates each kinematic model, keeping logic clear and extensible.

### Movement Vector & Heading

Prior to `periodic()`, commands call:

```java
setMovementVector(new Vector3d(x, y, rotation));
setHeading(desiredZeroOffsetRadians);
```

- `x`, `y` range from $-1$ to $1$, representing strafe and forward/backward.
- `rotation` sends a spin command about the robot’s center.
- Heading offset allows zeroing the IMU reference mid-match.

### Kinematic Models

| Mode                          | Inputs                    | Computation                                                                                                         | Use Case                         |
|-------------------------------|---------------------------|---------------------------------------------------------------------------------------------------------------------|----------------------------------|
| ROBOT_CENTRIC_HOLONOMIC       | $(x, y, z)$               | Linear combination: each wheel receives $y \pm x \pm z$, scaled to avoid saturation.                                | Simple omni or tank fallback     |
| ROBOT_CENTRIC_MECANUM         | $(x, y, z)$               | Trig-based: rotate vector by 45°, scale by vector length, inject rotation, normalize, then apply coefficient.       | Precise mecanum movement         |
| FIELD_CENTRIC_GAMEPAD         | $(x, y, z)$ + Pose2d      | Rotate raw gamepad vector by –robotHeading from Road Runner; apply weighted drive power for closed-loop profiling.  | Responsive, trajectory-ready     |
| FIELD_CENTRIC_IMU             | $(x, y, z)$ + IMU offset  | Cube small inputs for finesse, convert to field frame via IMU offset, then reuse trig-mecanum pipeline.             | Straight-line precision & resets |

#### Differential Holonomic

1. Scale $x$ by $1.1$ to counter imperfect strafing.
2. Compute raw powers:
   <div>
   $$
   \begin{aligned}
   LF &= y + x + z \\
   LR &= y - x + z \\
   RF &= y - x - z \\
   RR &= y + x - z
   \end{aligned}
   $$
   </div>
3. Normalize:
   $$
   \text{scale} = \max\left(|LF|, |LR|, |RF|, |RR|, 1\right).
   $$
   Apply $\texttt{DRIVE\textunderscore COEFFICIENT}$ and send to motors:
   $$
   \text{Power}_i = \frac{P_i}{\text{scale}} \cdot \texttt{DRIVE\textunderscore COEFFICIENT}.
   $$

#### Trigonometric Mecanum

1. Compute vector length and angle:
   <div>
   $$
   \text{length} = \sqrt{x^2 + y^2}, \quad
   \text{angle} = \tan^{-1}\left(\frac{y}{x}\right) - \frac{\pi}{4}
   $$
   </div>
2. Map to wheel powers via cosine and sine:
   <div>
   $$
   \begin{aligned}
   LF &= \text{length} \cdot \cos(\text{angle}) \\
   LR &= \text{length} \cdot \sin(\text{angle}) \\
   RF &= \text{length} \cdot \sin(\text{angle}) \\
   RR &= \text{length} \cdot \cos(\text{angle})
   \end{aligned}
   $$
   </div>
3. Inject rotation, renormalize, scale, and output.

> This yields smooth strafing with constant speed in any heading.

#### Field-Centric with Gamepad

1. Fetch current pose from $\texttt{DriveEngine}$.
2. Rotate gamepad input by negative heading:
   <div>
   $$
   \begin{pmatrix}
   x' \\
   y'
   \end{pmatrix}
   =
   \begin{pmatrix}
   \cos(-\theta) & -\sin(-\theta) \\
   \sin(-\theta) & \cos(-\theta)
   \end{pmatrix}
   \begin{pmatrix}
   x \\
   y
   \end{pmatrix}
   $$
   </div>
3. Call the following for built-in velocity control:
   $$
   \texttt{setWeightedDrivePower(Pose2d}(x', y', \omega)\texttt{)}.
   $$

> This leverages Road Runner’s closed-loop profiling under driver control.

#### IMU-Based Field-Centric

1. Dead-zone and cube raw inputs for fine control.
2. Convert to field frame via `Extensions.toFieldRelative(pose, heading)`.
3. Reuse trig-mecanum pipeline to compute motor powers.
4. Telemetry shows both raw and computed values, along with offset and heading in degrees.

### Telemetry & Debugging

Every mode floods driver station with the following;

- Stick positions.
- Computed vector length & angle.
- Rotation command.
- Current $x$, $y$, and heading (in field-centric).
- Offset for on-the-fly recalibration.

This immediate feedback accelerates tuning coefficients and diagnosing drift.

By centralizing all drive logic in this subsystem, the team gains a modular, testable foundation. Whether strafing across the warehouse or executing tight turns on the field, the Drive Subsystem adapts dynamically with predictable locomotion.

## Software Engineering and Best Practices

Building a competition-grade robot under tight deadlines demands more than clever algorithms, it requires a robust software foundation. We now explore the principles and processes that kept our FTC team’s codebase maintainable, testable, and scalable.

### Modular Design & Composition

Rather than monolithic OpModes, we decomposed functionality into reusable components.

- **Entity-Component Approach**
  + Each subsystem (Drive, Lift, Vision) implements a clear interface.
  + OpModes assemble components like building blocks.
- **Composition over Inheritance**
  + Prefer injecting a `DriveEngine` into `DriveSubsystem` rather than subclassing.
  + Example:
    ```java
    public class LiftSubsystem
    {
      private final MotorController liftMotor;
      public LiftSubsystem(MotorController motor)
      {
        this.liftMotor = motor;
      }
      public void moveTo(double position) { /* ... */ }
    }
    ```
- **Single Responsibility Principle**
  + Each class has one well-defined role (e.g., `ShippingElementPipeline` only handles color detection).
  + Encourages small, focused units that are easy to test.

### Version Control & Collaboration

A structured Git workflow kept eight students in sync.

1. **Branch-Per-Feature**
   + New sensor code or tuning scripts lived in their own branches.
   + Merges occurred only after peer review.
2. **Pull Requests with Templates**
   + Enforce a checklist: “Does this compile? Unit tests pass? Documentation updated?”
3. **Fork + Upstream Model**
   + Students forked the central repo, submitted pull requests, and practiced resolving conflicts.

### Testing & Simulation

With limited robot time, we built confidence in software off-robot.

- **Unit Tests for Utilities**
  + Vector math, PID calculations, and coordinate transforms can be tested in JUnit.
- **Software-In-The-Loop (SITL)**
  + OpenCV pipelines can be tested on prerecorded videos.
  + State-machine transitions can be validated with mock vision results.
- **Hardware Abstraction Layer**
  + `DriveEngine` interface hides actual motors, allowing a `SimulatedDriveEngine` to drive a virtual robot.

### Continuous Integration & Deployment

Automating builds can catch errors early and ensure consistency.

- **GitHub Actions Pipeline**
  + On every push:
    - Compile Java code.
    - Run unit tests.
    - Lint with Checkstyle.
- **Artifact Publishing**
  + Successful builds on `Development` branch published downloadable `.apk` to a private release channel for testers.
- **Automatic Versioning**
  + Git tags determined build numbers, embedding version info in logs and telemetry.

### Documentation & Code Quality

Clear docs turns novices into contributors.

- **In-Line Javadoc**
  + Every public class and method included a concise `@param`/`@return` description.
- **Markdown Tech-Notes**
  + High-level architecture diagrams and setup instructions can live in a `docs/` directory.
- **Style Guide Enforcement**
  + Consistent naming: `m_` prefix for members, `CAMEL_CASE` for constants.
  + Pre-commit hooks formatted code with custom Java Format.

### Code Reviews & Pair Programming

Peer feedback and collaboration accelerated learning.

- **Scheduled Review Sessions**
  + Twice weekly, students demoed features and walked through diffs together.
- **Pair Programming Rotations**
  + Rotated driver/navigator roles; spread domain knowledge and caught subtle bugs early.
- **Review Checklist**
  + Does it handle edge cases?
  + Are magic numbers replaced with named constants?
  + Is the algorithm’s complexity appropriate for the Control Hub’s CPU?

### Dependency Management & Reuse

Reinventing the wheel wastes precious build time.

- **Third-Party Libraries**
  + EasyOpenCV for vision, FTCLib commands for scheduling, Road Runner for trajectory planning.
- **Internal Utility Modules**
  + Custom math library housed common vector and PID classes, which are shared across seasons.
- **Version Pinning**
  + Fixed library versions in `build.gradle` ensured reproducible builds and avoided “works on my machine” issues.

By embedding appropriate software practices (e.g., modularity, automated testing, CI/CD, code reviews, and thorough documentation) we empowered the team to iterate rapidly, diagnose issues confidently, and maintain a rock-solid codebase under competition pressure.

## Development Tools

* [Android Studio](https://developer.android.com/studio). An Integrated Development Environment (IDE) for Android app development.
* [Git](https://git-scm.com/). A free and open source distributed version control system.
* [Fork](https://git-fork.com/). A user-friendly Git client with free evaluation (permanent as of writing).
* [GitHub](https://github.com/). A cloud-based Git repository hosting service.

Checkout the source code on [GitHub](https://github.com/TheophilusE/FTC_PowerPlay/tree/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode).