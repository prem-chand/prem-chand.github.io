---
title: "Model Predictive Control for Quadruped Locomotion"
description: "A practical, end-to-end guide to MPC for legged robots—covering rigid body dynamics, contact scheduling, and Python implementation with MuJoCo."
date: 2025-06-01T10:00:00+00:00
draft: false
slug: "mpc-quadruped"
categories:
  - Robotics
  - Control Systems
tags:
  - MPC
  - Quadruped
  - Legged Robotics
  - Optimization
  - Control Theory
  - Python
  - MuJoCo
---

## _A practical, end-to-end guide for beginners_

## Prerequisites

This post assumes familiarity with:

- Python and NumPy
- Basic rigid body dynamics (forces, torques, inertia)
- Jacobians for robot kinematics
- A physics simulator (MuJoCo preferred, but not required)

No prior experience with MPC or legged robots is assumed.

---

## 1. Motivation: Why Is Quadruped Locomotion Hard?

Quadruped locomotion looks effortless in animals. In robots, it is anything but.

A quadruped robot typically has:

- **12 actuated joints**
- A **6-DoF floating base** (position + orientation, not directly actuated)
- **Intermittent contact** with the ground

This means the robot is **underactuated**.
You cannot command the center of mass (CoM) directly. You can only influence it indirectly through **ground reaction forces (GRFs)** at the feet.

At any instant:

- Some legs are in contact, others are not
- Each contact can only push, not pull
- Friction limits what forces are physically possible

The control problem becomes one of **negotiating with physics**, not issuing commands.

![Image](https://www.researchgate.net/publication/241151813/figure/fig5/AS%3A669133691707403%401536545312490/GROUND-REACTION-FORCES-FOR-EACH-LEG-OF-THE-ROBOT-AND-THE-MODEL.png)

![Image](https://www.researchgate.net/publication/263277248/figure/fig1/AS%3A614048953540620%401523412087965/Schematic-representation-of-quadruped-robot-with-compliant-legs.png)

![Image](https://www.researchgate.net/publication/350790598/figure/fig1/AS%3A1035561045327873%401623908401256/Illustration-of-the-centroidal-dynamics-and-its-connection-to-the-whole-body-dynamics.png)

Every controller in this stack exists to answer one question:

> _Given these contacts, what forces should the robot apply to stay balanced and move where we want?_

---

## 2. Control Hierarchy Overview

Before diving into math, it helps to see the full control stack.

```
        Model Predictive Control (≈ 30–50 Hz)
        → decides desired ground reaction forces

        Whole Body Control (≈ 100–200 Hz)
        → converts forces into joint torques

        Swing Leg Control (≈ 1 kHz)
        → moves feet when they are not in contact
```

Why three layers?

- **MPC** reasons about the _future_ but is computationally expensive
- **WBC** enforces instantaneous physical consistency
- **Swing control** must react quickly to maintain foot tracking

Each layer solves a simpler problem than the one above it.

![Image](https://www.researchgate.net/publication/258970686/figure/fig2/AS%3A669529864675336%401536639767626/Quadruped-robot-control-architecture.png)

![Image](https://moonlight-paper-snapshot.s3.ap-northeast-2.amazonaws.com/arxiv/hierarchical-learning-framework-for-whole-body-model-predictive-control-of-a-real-humanoid-robot-2.png)

A key idea to keep in mind:

> MPC does **not** output joint torques.
> It outputs _forces at the feet_.

---

## 3. Gait Scheduling

A gait defines **when each foot is allowed to touch the ground**.

For a trot:

- Front-left and rear-right move together
- Front-right and rear-left move together

We represent this using **phase offsets**:

```
FL: 0.0
FR: 0.5
RL: 0.5
RR: 0.0
```

From these phases we generate a **binary contact schedule**:

```
c_i(k) ∈ {0, 1}
```

This schedule is provided to MPC as a known input.

From MPC’s perspective:

- There are no “legs”
- Only **force variables that are enabled or disabled**

![Image](https://media.springernature.com/lw1200/springer-static/image/art%3A10.1038%2Fs41598-024-84060-5/MediaObjects/41598_2024_84060_Fig6_HTML.png)

![Image](https://www.researchgate.net/publication/346211363/figure/fig4/AS%3A11431281401767575%401745601475531/The-left-side-of-the-image-shows-the-schedule-of-a-trot-gait-while-the-right-side-shows.tif)

---

## 4. MPC Formulation

### 4.1 State Vector

We use a **centroidal model**, focusing on the robot’s mass distribution rather than individual joints.

State vector (12-dimensional):

```
x = [ p, θ, v, ω ]
```

Where:

- `p` : CoM position (world frame)
- `θ` : roll, pitch, yaw
- `v` : linear velocity
- `ω` : angular velocity

This abstraction is deliberate.
MPC does not care _how_ joints move—only how forces move the body.

---

### 4.2 Centroidal Dynamics

The continuous-time dynamics:

```
ṗ = v
θ̇ ≈ ω
v̇ = (1/m) Σ F_i + g
ω̇ = I⁻¹ Σ (r_i × F_i)
```

Where:

- `F_i` are ground reaction forces
- `r_i` is vector from CoM to foot contact
- `I` is the body inertia matrix

This is Newton–Euler mechanics, nothing exotic.

![Image](https://www.researchgate.net/publication/350790598/figure/fig1/AS%3A1035561045327873%401623908401256/Illustration-of-the-centroidal-dynamics-and-its-connection-to-the-whole-body-dynamics.png)

![Image](https://www.researchgate.net/publication/371684817/figure/fig3/AS%3A11431281168765950%401687144641740/The-centroidal-dynamics-model-assumes-that-the-robot-is-a-single-rigid-body-with-massless.png)

---

### 4.3 Linearization

The true dynamics are nonlinear. MPC handles this by **linearizing around the current state**.

Assumptions:

- Small roll and pitch angles
- Yaw handled separately
- Contact positions known

After linearization and discretization:

```
x_{k+1} = A x_k + B u_k + g
```

This approximation is valid because:

- MPC is re-solved every few milliseconds
- Errors do not accumulate for long

---

### 4.4 Cost Function

The quadratic cost over a horizon `N`:

```
J = Σ (x_k − x_ref)ᵀ Q (x_k − x_ref) + u_kᵀ R u_k
```

Interpretation:

- `Q`: how much we care about tracking motion
- `R`: how much we penalize large forces

Too much `Q` → force chatter
Too much `R` → sluggish response

---

### 4.5 Constraints

Physical constraints make this problem meaningful:

- **Contact schedule**: only stance legs apply force
- **Friction pyramid**:

  ```
  |F_x| ≤ μ F_z
  |F_y| ≤ μ F_z
  F_z ≥ 0
  ```

The MPC output is:

> **Desired ground reaction forces over the horizon**

---

## 5. Coordinate Frames (Crucial and Often Skipped)

Many unstable controllers fail **not because of bad math**, but because vectors live in the wrong frame.

We use three frames:

| Frame | Description            | Why              |
| ----- | ---------------------- | ---------------- |
| World | Fixed, gravity aligned | Global reference |
| Body  | Rotates with robot     | Sensors, inertia |
| Yaw   | Rotates only about z   | MPC linearity    |

Why the yaw frame?

- Full body frame rotates too aggressively for linear MPC
- World frame ignores robot heading
- Yaw frame is the compromise

![Image](https://www.researchgate.net/publication/221786604/figure/fig1/AS%3A305482783838208%401449844179148/Coordinate-frames-for-the-robot-The-head-has-two-DOFs-such-as-yawing-and-pitching-The.png)

![Image](https://dev.bostondynamics.com/_images/spotframes.png)

A common trick:

- Rotate velocities into the yaw frame before feeding MPC
- Rotate forces back afterward

This preserves physical meaning while keeping the math tame.

---

## 6. Whole Body Control (WBC)

Given desired GRFs from MPC, we compute joint torques.

For a stance leg:

```
τ = − Jᵀ F_GRF
```

Why the minus sign?

- `F_GRF` is force _on the body_
- Joint torques must apply the opposite force to the ground

In MuJoCo, gravity and Coriolis effects appear in `qfrc_bias`.
We add this term explicitly for gravity compensation.

WBC is instantaneous:

- No prediction
- No horizon
- Just enforce physics _right now_

![Image](https://i.sstatic.net/b8Wdg.png)

![Image](https://www.researchgate.net/publication/339177612/figure/fig4/AS%3A857800704024579%401581527033952/Simulink-model-of-Jacobian-transpose-control.ppm)

---

## 7. Swing Leg Control

Swing legs are **kinematic problems**, not force problems.

### 7.1 Raibert Heuristic

Target foot placement:

```
p_target = p_nominal + v·T/2 + K (v_cmd − v)
```

Key detail:

> The target is anchored to a **nominal stance location**, not the liftoff position.

Anchoring to liftoff accumulates drift and destabilizes the robot.

---

### 7.2 Trajectory Generation

We generate a smooth swing trajectory using a **cubic Bézier curve**:

- Zero velocity at liftoff
- Zero velocity at touchdown
- Fixed swing height

![Image](https://www.researchgate.net/publication/381417059/figure/fig1/AS%3A11431281390879366%401745300148350/Bezier-curve-green-to-implement-the-support-and-swing-phases-during-the-step-of-a-leg.tif)

![Image](https://cdn.hackaday.io/images/3445591592702432996.png)

---

### 7.3 Cartesian PD

At high rate (~1 kHz):

```
F = Kp (p_des − p) + Kd (v_des − v)
```

Swing targets are frozen at swing onset to prevent mid-air oscillations.

---

## 8. Tuning Lessons (What the Papers Don’t Tell You)

This section matters.

### Q vs R Balance

- Large `Q/R`: aggressive correction, force chatter
- Small `Q/R`: smooth but unresponsive

### Force Smoothing

Exponential moving average reduces chatter:

- More smoothing → lag
- Less smoothing → noise

### Foot Placement Bug

Symptom:

- Legs drift inward
- Robot collapses laterally

Signal:

- Asymmetric lateral GRFs

Root cause:

- Foot targets anchored to hip frame

Fix:

- Anchor to nominal stance frame

Logs revealed this faster than intuition.

---

## 9. Results

- Stable standing
- Clean trot
- Roll angle reduced by >50%
- Foot placement converges to targets

![Image](https://www.researchgate.net/publication/284907108/figure/fig5/AS%3A877552176484352%401586236151606/Webots-simulation-Snapshots-of-one-gait-cycle-of-the-Cheetahcub-robot-in-trot-gait.jpg)

![Image](https://media.springernature.com/lw1200/springer-static/image/art%3A10.1038%2Fs41598-023-41462-1/MediaObjects/41598_2023_41462_Fig13_HTML.png)

Showing _before_ matters. Stability is earned.

---

## 10. What’s Next

Possible extensions:

- Sparse MPC for longer horizons
- Contact estimation from force sensors
- Terrain-aware foot placement
- Real-time QP solvers (OSQP, qpSWIFT)

Each improves either **performance**, **robustness**, or **scalability**—rarely all three at once.

---

## Closing Thoughts

Quadruped locomotion is not controlled by a single clever algorithm.
It emerges from **clear abstractions**, **respect for physics**, and **painfully careful coordinate handling**.

If this post saves you a week of debugging lateral collapse, it has done its job.

---

Below is a **self-contained Appendix** you can paste verbatim at the end of the blog.
It is deliberately more formal than the main text, but still readable for a beginner who is willing to slow down.

No storytelling here—this is the machinery laid bare.

---

# Appendix A: Model Predictive Control Mathematics

This appendix derives the MPC formulation used in the main article.
All assumptions are stated explicitly.

---

## A.1 Modeling Assumptions

We use a **centroidal rigid body model** with the following assumptions:

1. The robot is treated as a single rigid body with mass `m` and inertia `I`
2. Contacts occur at known foot locations
3. Roll and pitch angles are small
4. Contacts are known a priori from the gait scheduler
5. Forces are piecewise constant over each MPC timestep

This model ignores joint dynamics entirely. That abstraction is intentional.

---

## A.2 State and Input Definitions

### State Vector

The state is 12-dimensional:

[
x =
\begin{bmatrix}
p \
\theta \
v \
\omega
\end{bmatrix}
\in \mathbb{R}^{12}
]

Where:

- ( p \in \mathbb{R}^3 ): center of mass position (world frame)
- ( \theta = [\phi, \theta, \psi]^\top ): roll, pitch, yaw
- ( v \in \mathbb{R}^3 ): linear velocity
- ( \omega \in \mathbb{R}^3 ): angular velocity

---

### Control Input

The control input is the concatenation of **ground reaction forces** at all feet:

[
u =
\begin{bmatrix}
F_1 \
F_2 \
F_3 \
F_4
\end{bmatrix}
\in \mathbb{R}^{12}
]

Each ( F_i \in \mathbb{R}^3 ) is expressed in the **world frame**.

If a foot is in swing, its corresponding force is constrained to zero.

---

## A.3 Continuous-Time Dynamics

### Translational Dynamics

[
\dot{p} = v
]

[
\dot{v} = \frac{1}{m} \sum_{i=1}^4 F_i + g
]

Where ( g = [0, 0, -9.81]^\top ).

---

### Rotational Dynamics

[
\dot{\theta} \approx \omega
]

This approximation is valid for small roll and pitch angles.

[
\dot{\omega} = I^{-1} \sum_{i=1}^4 (r_i \times F_i)
]

Where:

- ( r*i = p*{foot,i} - p\_{CoM} )

---

## A.4 Linearization

We linearize about the current state ( x_0 ) and nominal input ( u_0 ).

The dynamics can be written compactly as:

[
\dot{x} = f(x, u)
]

First-order Taylor expansion:

[
\dot{x} \approx A_c (x - x_0) + B_c (u - u_0) + f(x_0, u_0)
]

Where:

[
A_c = \left.\frac{\partial f}{\partial x}\right|*{x_0,u_0},
\quad
B_c = \left.\frac{\partial f}{\partial u}\right|*{x_0,u_0}
]

---

### Structure of ( A_c )

[
A_c =
\begin{bmatrix}
0 & 0 & I & 0 \
0 & 0 & 0 & I \
0 & 0 & 0 & 0 \
0 & 0 & 0 & 0
\end{bmatrix}
]

This reflects:

- position integrates velocity
- orientation integrates angular velocity
- accelerations depend only on forces

---

### Structure of ( B_c )

[
B_c =
\begin{bmatrix}
0 \
0 \
\frac{1}{m} [I ; I ; I ; I] \
I^{-1} [r_1^\times ; r_2^\times ; r_3^\times ; r_4^\times]
\end{bmatrix}
]

Where ( r^\times ) is the skew-symmetric cross-product matrix.

---

## A.5 Discretization

Using forward Euler with timestep ( \Delta t ):

[
x_{k+1} = x_k + \Delta t (A_c x_k + B_c u_k + g)
]

Which yields:

[
x_{k+1} = A x_k + B u_k + c
]

With:

[
A = I + \Delta t A_c
\quad
B = \Delta t B_c
\quad
c = \Delta t g
]

---

## A.6 MPC Horizon Stacking

Over a horizon of length ( N ):

[
X =
\begin{bmatrix}
x_1 \
x_2 \
\vdots \
x_N
\end{bmatrix},
\quad
U =
\begin{bmatrix}
u_0 \
u_1 \
\vdots \
u_{N-1}
\end{bmatrix}
]

The stacked dynamics become:

[
X = \mathcal{A} x_0 + \mathcal{B} U + \mathcal{C}
]

Where ( \mathcal{A} ), ( \mathcal{B} ) are block matrices constructed from ( A ) and ( B ).

---

## A.7 Cost Function

The quadratic objective:

[
J =
\sum_{k=0}^{N-1}
(x_k - x_k^{ref})^\top Q (x_k - x_k^{ref})
+
u_k^\top R u_k
]

In stacked form:

[
J =
\frac{1}{2} U^\top H U + f^\top U
]

Where:

[
H = \mathcal{B}^\top \bar{Q} \mathcal{B} + \bar{R}
]

[
f = \mathcal{B}^\top \bar{Q} (\mathcal{A} x_0 - X_{ref})
]

This is a **convex quadratic program**.

---

## A.8 Constraints

### Contact Constraints

For each foot ( i ):

[
F_i = 0 \quad \text{if foot } i \text{ is in swing}
]

---

### Friction Pyramid

For stance feet:

[
|F_x| \le \mu F_z
]

[
|F_y| \le \mu F_z
]

[
F_z \ge 0
]

These constraints are linear and compatible with QP solvers.

---

## A.9 Final MPC Problem

The complete optimization:

[
\begin{aligned}
\min_U \quad & \frac{1}{2} U^\top H U + f^\top U \
\text{s.t.} \quad
& G U \le h \
& A_{eq} U = b_{eq}
\end{aligned}
]

Solved at every control cycle.

Only the **first control input** ( u_0^\* ) is applied.
The horizon is shifted and the problem re-solved at the next timestep.

---

## A.10 Key Takeaways

- MPC operates on **forces**, not torques
- Linearization is acceptable because of frequent re-solving
- Physical constraints are what make the solution meaningful
- Most implementation bugs arise from **frame mismatches**, not math

---

This appendix intentionally trades brevity for clarity.
If the main article explains _why_ MPC works, this appendix shows _how_ the machinery actually runs.
