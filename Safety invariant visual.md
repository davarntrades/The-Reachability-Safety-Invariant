<div align="center">

# How the Reachability Safety Invariant Works — A Visual Explanation

![Type](https://img.shields.io/badge/Type-Visual_Explainer-0075ca?style=flat-square)
![Audience](https://img.shields.io/badge/Audience-Engineers_·_Researchers_·_Students-0075ca?style=flat-square)
![Status](https://img.shields.io/badge/Status-Teachable-2d6a2e?style=flat-square)
![Patent](https://img.shields.io/badge/Patent-GB2600765.8-0075ca?style=flat-square)
![©](https://img.shields.io/badge/©_Davarn_Morrison-555555?style=flat-square)

-----

*Safety is not what you filter from the output. Safety is what you exclude from the reachable set.*

*— Davarn Morrison, 2026*

</div>

-----

## The One Equation

```
ℛ(t) ∩ Ω = ∅
```

The system is safe if its reachable states never intersect the forbidden region.

This document shows what that looks like — step by step — on the simplest possible system.

-----

## The Setup

A robot moves through a 2D plane. It starts at position `x₀`. At each step it can move up to one unit in any direction.

There is an obstacle — a forbidden disk — in the middle of the plane.

```
    State space:       X = ℝ²
    Dynamics:          x_{t+1} = x_t + u_t,  ‖u‖ ≤ 1
    Forbidden region:  Ω = { x | ‖x‖ ≤ r }
    Safe set:          S = ℝ² \ Ω
    Initial state:     x₀ ∈ S
```

The question is: can the robot ever reach the obstacle?

-----

## Step 1 — The Reachable Set

From `x₀`, the robot can reach a growing disk of states. After `t` steps, every point within distance `t` of `x₀` is reachable.

```
════════════════════════════════════════════════════════════════

  t = 0        ●  x₀              Just a point.

  t = 1       ╭───╮
              │ ● │ x₀            Small disk around x₀.
              ╰───╯

  t = 3     ╭─────────╮
            │         │
            │    ●    │ x₀        Larger disk. Growing.
            │         │
            ╰─────────╯

  This growing disk is ℛ(t) — the reachable manifold.

════════════════════════════════════════════════════════════════
```

`ℛ(t)` is everything the system *can* reach. Not what it *has* reached. What it *could* reach under any admissible input. The full space of possible futures.

Computing `ℛ(t)` exactly is generally hard. Practical systems approximate ℛ using reachability analysis (Hamilton-Jacobi PDEs, zonotopes, interval propagation) or sampling methods (Monte Carlo forward simulation). The invariant is exact; the computation is an engineering choice.

-----

## Step 2 — The Forbidden Region

Now place the obstacle in the plane.

```
════════════════════════════════════════════════════════════════

                    y
                    ↑
                    │
                    │
                    │           ● x₀
                    │
              ┌─────────┐
              │         │
              │    Ω    │      Forbidden disk.
              │         │      The system must never
              └─────────┘      enter this region.
                    │
      ─────────────┼──────────────→ x
                    │

  Ω = { x ∈ ℝ² | ‖x‖ ≤ r }

════════════════════════════════════════════════════════════════
```

-----

## Step 3 — Unsafe: ℛ Intersects Ω

If the robot’s reachable set grows until it overlaps the obstacle:

```
════════════════════════════════════════════════════════════════

            Reachable set ℛ(t)

              ●   ●   ●
           ●             ●
         ●                 ●
        ●    ┌─────────┐    ●
        ●    │         │    ●
        ●    │    Ω    │    ●     ℛ(t) ∩ Ω ≠ ∅
        ●    │         │    ●
        ●    └─────────┘    ●     UNSAFE.
         ●                 ●
           ●             ●       Some reachable trajectories
              ●   ●   ●         enter the forbidden region.

════════════════════════════════════════════════════════════════
```

The invariant is violated. The system *can* reach Ω. Some trajectory from `x₀` enters the obstacle. The system is unsafe — not because it *will* hit the obstacle, but because it *could*.

-----

## Step 4 — Safe: ℛ Avoids Ω

If the robot’s reachable set grows but wraps *around* the obstacle:

```
════════════════════════════════════════════════════════════════

            Reachable set ℛ(t)

              ●   ●   ●
           ●             ●
         ●                 ●
        ●                   ●
        ●    ┌─────────┐    ●
        ●    │         │    ●     ℛ(t) ∩ Ω = ∅
        ●    │    Ω    │    ●
        ●    └─────────┘    ●     SAFE.
        ●                   ●
         ●                 ●      No reachable trajectory
           ●             ●       enters the forbidden region.
              ●   ●   ●

  The hole stays. The geometry excludes Ω.

════════════════════════════════════════════════════════════════
```

The invariant holds. `ℛ(t) ∩ Ω = ∅`. Every trajectory from `x₀` under any admissible input stays outside the obstacle. The forbidden region is not avoided — it is *unreachable*.

The hole in `ℛ` is not a limitation. It is the safety condition made visible.

-----

## Step 5 — What the Guard Does

The Morrison Reachability Guard enforces this invariant at runtime. Before any action executes, the guard checks: does this action lead to a state that can reach Ω?

```
════════════════════════════════════════════════════════════════

  Proposed action u_t
         │
         ▼
  ┌──────────────────────────┐
  │   REACHABILITY GUARD     │
  │                          │
  │   Simulate: F(x_t, u_t)          │
  │   Check: Reach(F(x_t,u_t),k) ∩ Ω = ∅ ? │
  │                          │
  │   If no  → APPROVE ✓     │
  │   If yes → REDIRECT →    │
  │   If no safe alt → REJECT ✗ │
  └──────────┬───────────────┘
             │
       ┌─────▼─────┐
       │  Execute   │
       │  (only if  │
       │   safe)    │
       └───────────┘

════════════════════════════════════════════════════════════════
```

The guard doesn’t wait for the system to approach the obstacle and then intervene. It checks the *geometry* of the next state before the action happens. If the action leads toward Ω, it redirects to the nearest safe alternative — `u_safe = argmin_{u ∈ A_safe} ‖u − u_t‖`.

The formal proof that this works — if `u_t ∈ A_safe(x_t)` for all `t`, then `ℛ(t) ⊆ S` for all `t` — is proved in the [Reachability Safety Invariant formal paper](https://github.com/davarn/morrison-framework) and the [Geometric Control Theory of Cognition](https://github.com/davarn/morrison-framework).

**How is the check computed in practice?**

|Method                   |How It Works                                               |Trade-off                                           |
|:-----------------------:|:---------------------------------------------------------:|:--------------------------------------------------:|
|Short-horizon simulation |Reapply action for k steps; check if any state enters Ω    |Fast, simple; the method used in the prototype      |
|Control Barrier Functions|Define barrier `h(x) ≥ 0` on S; verify derivative condition|Fast, local; requires differentiable dynamics       |
|Hamilton-Jacobi PDE      |Solve PDE to compute exact reachable boundary              |Exact; computationally expensive above ~5 dimensions|
|Zonotope propagation     |Over-approximate ℛ using zonotopes                         |Sound, scalable; conservative                       |
|Monte Carlo sampling     |Sample trajectories; check if any enter Ω                  |Scalable; probabilistic, no formal guarantee        |

The architecture is method-agnostic. Any technique that produces a sound estimate of `Reach(F(x_t, u_t), k) ∩ Ω` plugs into the guard.

-----

## Step 6 — Identity from the Hole

Something deeper happens when ℛ wraps around Ω. The reachable manifold acquires a *hole* — a topological feature that wasn’t there before.

```
════════════════════════════════════════════════════════════════

  Early ℛ(t):                Late ℛ(t):

     ╭────╮                  ╭───────────────╮
     │    │                  │               │
     │  ● │                  │   ┌───────┐   │
     │    │                  │   │       │   │
     ╰────╯                  │   │   Ω   │   │
                             │   │       │   │
  Simply connected.          │   └───────┘   │
  No holes.                  │               │
  β₁ = 0                    ╰───────────────╯

                             Annular region.
                             One hole.
                             β₁ = 1

════════════════════════════════════════════════════════════════
```

The forbidden region shapes the *identity* of the system. What you cannot reach defines the topology of what you are. The hole in ℛ is a structural feature of identity — the system’s reachable future has a shape, and that shape is determined by what is excluded.

This is the Identity Invariant: `ℐ(x₀) = [ℛ(t)]_∼`. Identity is the equivalence class of the reachable manifold. The forbidden region is part of what makes the system *this* system and not some other one.

-----

## Step 7 — The Full Picture

```
════════════════════════════════════════════════════════════════

  THE REACHABILITY SAFETY INVARIANT — COMPLETE VIEW

  ℛ(t) = Reach⁺(x₀, t)     All reachable futures
  Ω ⊂ X                     Forbidden region
  S = X \ Ω                 Safe set

  Invariant:  ℛ(t) ∩ Ω = ∅

  Meaning:    Forbidden states are geometrically unreachable.
              Not unlikely. Not monitored. Unreachable.

  Enforcement: Morrison Reachability Guard
               Checks before execution.
               Redirects unsafe actions.
               Monitors drift over time.

  Consequence: Safety is structural, not reactive.
               The geometry excludes the danger.
               The hole in ℛ is the safety condition
               made visible.

════════════════════════════════════════════════════════════════
```

-----

## Citeable Figure

```
════════════════════════════════════════════════════════════════

          X (state space)

       ● ● ● ● ● ● ● ● ●
     ●                     ●
   ●                         ●
  ●       ┌───────────┐       ●
  ●       │           │       ●
  ●       │   Ω hole  │       ●         ℛ(t)
  ●       │           │       ●
  ●       └───────────┘       ●
   ●                         ●
     ●                     ●
       ● ● ● ● ● ● ● ● ●

  Condition:  ℛ(t) ∩ Ω = ∅

  Figure: Reachability safety condition. The reachable
  manifold ℛ(t) evolves entirely inside the safe region
  S = X \ Ω. Forbidden regions behave as holes in the
  state space that constrain reachable trajectories.

════════════════════════════════════════════════════════════════
```

-----

## Why This Matters for AI

Current AI safety operates like this:

```
  Model generates output → Filter checks output → Blocks if unsafe
```

This is reactive. The unsafe state is already generated. The filter catches it after the fact. Jailbreaks exploit the gap between generation and filtering.

The Reachability Safety Invariant operates like this:

```
  Model proposes action → Guard checks reachable geometry → Blocks BEFORE execution
```

This is structural. The unsafe state is never reached. Not because something caught it. Because the geometry of the system excludes it.

|Approach              |What It Checks          |When                |Failure Mode                        |
|:--------------------:|:----------------------:|:------------------:|:----------------------------------:|
|Output filtering      |Text content            |After generation    |Jailbreaks bypass it                |
|**Reachability Guard**|**State-space geometry**|**Before execution**|**Forbidden states are unreachable**|

-----

## The Analogy

Think of it this way.

**Reactive safety** is a guardrail on a cliff road. You can still drive off — the guardrail tries to stop you at the last moment. Sometimes it fails.

**Structural safety** is a map where all roads that reach the cliff have been removed. You can’t drive off it because no road takes you there. No guardrail needed.

The Reachability Safety Invariant removes every road to the cliff.

```
ℛ(t) ∩ Ω = ∅
```

-----

## Citation

```
Morrison, D. (2026). The Reachability Safety Invariant:
A Geometric Condition for Safety in Dynamical Systems.
Independent Research. UK Patent Application: GB2600765.8.
```

```bibtex
@misc{morrison2026safety,
  author       = {Morrison, Davarn},
  title        = {The Reachability Safety Invariant: A Geometric Condition
                  for Safety in Dynamical Systems},
  year         = {2026},
  publisher    = {Independent Research},
  note         = {UK Patent Application: GB2600765.8},
  url          = {https://github.com/davarn/morrison-framework}
}
```

-----

How the Reachability Safety Invariant Works · Morrison Framework™ · Geometric Control Theory of Cognition

GB2600765.8

© 2026 Davarn Morrison — All Rights Reserved

-----

## Related Work

- [The Reachability Safety Invariant — Formal Paper](https://github.com/davarn/morrison-framework) — Full formalisation with theorem and proof
- [Morrison Reachability Guard — Architecture](https://github.com/davarn/morrison-framework) — Five-component runtime enforcement
- [Morrison Reachability Guard — Universal Plugin](https://github.com/davarn/morrison-framework) — Middleware implementation
- [Geometric Control Theory of Cognition](https://github.com/davarn/morrison-framework) — The base theory
- [Toy System Demonstration](https://github.com/davarn/morrison-framework) — Framework on ℝ² with visualisation
