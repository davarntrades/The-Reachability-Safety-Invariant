<div align="center">

# The Reachability Safety Invariant — A Geometric Condition for Safety in Dynamical Systems

**Davarn Morrison**
**Independent Research · 2026**

![Type](https://img.shields.io/badge/Type-Formal_Paper-0075ca?style=flat-square)
![Field](https://img.shields.io/badge/Field-Safety_·_Control_Theory_·_Reachability-0075ca?style=flat-square)
![Status](https://img.shields.io/badge/Status-v1_Canonical-2d6a2e?style=flat-square)
![Patent](https://img.shields.io/badge/Patent-GB2600765.8-0075ca?style=flat-square)
![©](https://img.shields.io/badge/©_Davarn_Morrison-555555?style=flat-square)

</div>

-----

This work builds upon the patented pre-semantic trajectory governance framework.

-----

## Abstract

Safety in complex systems is traditionally treated as a reactive property: systems detect unsafe states and intervene after risk emerges. This paper introduces a geometric formulation of safety based on reachability theory.

We define the Reachability Safety Invariant:

**ℛ(t) ∩ Ω = ∅**

which states that a system is safe if its reachable states never intersect a forbidden region of the state space.

This formulation shifts safety from a reactive control condition to a structural property of the system’s reachable geometry. The invariant provides a compact safety condition independent of specific control implementations. It applies to dynamical systems, autonomous agents, robotics, and AI systems with state transitions. We formalise the invariant, prove basic properties, and illustrate its application in control systems and AI safety architectures.

-----

## 1. Introduction

Safety is a fundamental requirement in engineered systems such as robotics, autonomous vehicles, financial systems, and artificial intelligence.

Traditional safety mechanisms operate by detecting unsafe states, triggering corrective action, or shutting down the system. This approach is reactive. The unsafe state must first be reached — or nearly reached — before the system responds.

In contrast, control theory provides tools for reasoning about the reachable states of a dynamical system. If a dangerous state cannot be reached, the system is inherently safe. Not safe because something intervened. Safe because the geometry of the system excludes the danger.

This observation leads to a fundamental geometric condition for safety.

-----

## 2. Dynamical Systems

Let `X ⊆ ℝⁿ` be the state space of a dynamical system. Let `u(t) ∈ 𝒰` be admissible control inputs.

System dynamics are defined by:

|Form           |Equation               |
|:-------------:|:---------------------:|
|Continuous-time|`ẋ(t) = f(x(t), u(t))` |
|Discrete-time  |`x_{t+1} = F(x_t, u_t)`|

with initial condition `x(0) = x₀`.

-----

## 3. The Reachable Set

The forward reachable set is defined as:

|Symbol|Definition                                          |Meaning                                     |
|:----:|:--------------------------------------------------:|:------------------------------------------:|
|`ℛ(t)`|`Reach⁺(x₀, t) = { x(t) ∈ X | x(0) = x₀, u(·) ∈ 𝒰 }`|All states the system can reach up to time t|

This represents the complete set of future states accessible from the initial condition under any admissible input sequence.

The reachable set is the future geometry of the system.

-----

## 4. The Forbidden Region

Let `Ω ⊂ X` be the forbidden region of the state space.

|Domain           |Forbidden Region Ω                                     |
|:---------------:|:-----------------------------------------------------:|
|Robotics         |Collision states                                       |
|Finance          |Illegal transactions, regulatory violations            |
|AI systems       |Unsafe policy states, alignment failures               |
|Aerospace        |Flight envelope violations                             |
|Healthcare       |Misdiagnosis cascades, drug interaction errors         |
|Sovereign systems|National security violations, data sovereignty breaches|

The system must never enter Ω. Not unlikely. Not monitored. Never.

-----

## 5. The Reachability Safety Invariant

**Definition.** A system satisfies the Reachability Safety Invariant if:

|Invariant       |Condition    |
|:--------------:|:-----------:|
|**ℛ(t) ∩ Ω = ∅**|For all t ≥ 0|

**Interpretation.** The system is safe if no reachable state lies in the forbidden region. Safety is a property of the geometry of reachable states — not a runtime intervention, not an output filter, not a reactive shutdown.

The forbidden region is not avoided. It is unreachable.

-----

## 6. Geometric Interpretation

Let the safe region be:

|Symbol     |Definition                            |
|:---------:|:------------------------------------:|
|`S = X \ Ω`|The complement of the forbidden region|

The safety invariant is equivalent to:

|Equivalent Form|Meaning                                                       |
|:-------------:|:------------------------------------------------------------:|
|`ℛ(t) ⊆ S`     |The reachable manifold evolves entirely within the safe region|

Forbidden regions act as holes in the state space, constraining the topology of reachable trajectories. The system’s future geometry is shaped by what it cannot reach. What is excluded from ℛ is as important as what is included.

-----

## 7. Theorem: Safety Preservation

**Theorem.** If the Reachability Safety Invariant holds for all admissible controls, the system cannot enter the forbidden region.

**Proof.**

Assume `ℛ(t) ∩ Ω = ∅`.

By definition, `ℛ(t) = { x(t) | x(0) = x₀, u(·) ∈ 𝒰 }`. Every possible trajectory from `x₀` under any admissible input lies in `ℛ(t)`.

If `ℛ(t) ∩ Ω = ∅`, then no trajectory lies in Ω.

Therefore `x(t) ∉ Ω` for all admissible trajectories, for all `t ≥ 0`.

The system cannot reach forbidden states. ∎

-----

## 8. Reactive vs Structural Safety

Traditional safety checks the current state:

|Approach      |Condition         |Scope                          |Failure Mode                                                                  |
|:------------:|:----------------:|:-----------------------------:|:----------------------------------------------------------------------------:|
|Reactive      |`x(t) ∉ Ω`        |Current state only             |System may already be at the boundary; intervention may be too late           |
|**Structural**|**`ℛ(t) ∩ Ω = ∅`**|**Entire future reachable set**|**Forbidden states are geometrically excluded — no trajectory can reach them**|

Structural safety is strictly stronger. Reactive safety asks “are we safe right now?” Structural safety asks “can we ever become unsafe?” The answer, under the invariant, is no.

-----

## 9. Example: Robot Navigation

Consider a robot moving in a plane.

|Parameter       |Value                           |
|:--------------:|:------------------------------:|
|State space     |`X = ℝ²`                        |
|Dynamics        |`x_{t+1} = x_t + u_t`, `‖u‖ ≤ 1`|
|Forbidden region|`Ω` = obstacle regions          |
|Safety invariant|`ℛ(t) ∩ Ω = ∅`                  |

The robot controller must ensure reachable trajectories never intersect obstacles. The invariant is not “avoid the obstacle when you see it.” The invariant is “the obstacle is geometrically unreachable under any admissible trajectory.”

-----

## 10. Example: AI Agent Safety

For AI agents interacting with tools:

|Parameter       |Value                                                                                    |
|:--------------:|:---------------------------------------------------------------------------------------:|
|State space     |`X` = agent state space (task, tools, permissions, memory, environment)                  |
|Forbidden region|`Ω` = states representing policy violations, security breaches, unsafe autonomous actions|
|Safety invariant|`ℛ(t) ∩ Ω = ∅`                                                                           |

The reachability condition ensures the agent cannot transition into unsafe states. This is not output filtering. This is not prompt engineering. This is geometric exclusion at the state-transition layer.

This principle motivates runtime governance systems such as the Morrison Reachability Guard — a middleware layer that enforces the invariant before tool execution.

-----

## 11. Relation to Control Theory

Reachability analysis is a central concept in optimal control, safety verification, and hybrid systems. The Reachability Safety Invariant expresses safety as a global geometric constraint on reachable sets.

This formulation shares structural similarities with established techniques:

|Technique                       |Relationship to the Invariant                                                                                            |
|:------------------------------:|:-----------------------------------------------------------------------------------------------------------------------:|
|Control Barrier Functions (CBFs)|Local differential constraints ensuring `h(x) ≥ 0`; the invariant states the same condition globally                     |
|Barrier certificates            |Lyapunov-like functions proving safety; the invariant is the reachability-set formulation                                |
|Viability kernels               |Sets from which the system can remain safe; the invariant requires the entire reachable set lies within the viable region|
|Hamilton-Jacobi reachability    |PDE-based exact computation of reachable sets; provides one method for verifying the invariant                           |

The invariant is consistent with these techniques but expresses the safety condition directly in terms of reachable sets, without requiring a specific control implementation to verify it.

### References

|Author(s)                                                                      |Work                                                                                                |Relevance                                                                                                                       |
|:-----------------------------------------------------------------------------:|:--------------------------------------------------------------------------------------------------:|:------------------------------------------------------------------------------------------------------------------------------:|
|Ames, A.D., Coogan, S., Egerstedt, M., Notomista, G., Sreenath, K., Tabuada, P.|*Control Barrier Functions: Theory and Applications* (2019)                                         |Foundational work on CBFs for safety-critical control; the invariant generalises the safety condition to global reachable sets  |
|Mitchell, I.M., Bayen, A.M., Tomlin, C.J.                                      |*A Time-Dependent Hamilton-Jacobi Formulation of Reachable Sets for Continuous Dynamic Games* (2005)|Exact reachability computation via HJ PDEs; provides one algorithmic method for verifying the invariant                         |
|Blanchini, F.                                                                  |*Set Invariance in Control* (1999)                                                                  |Survey of invariant set methods in control theory; the safety invariant is a reachability-set formulation of positive invariance|

-----

## 12. Discussion

The Reachability Safety Invariant reframes safety as a property of the geometry of future possibilities. Instead of monitoring outputs or policies, safety becomes a constraint on the reachable manifold of system dynamics.

This formulation is relevant for autonomous systems, robotics, AI agents, and large-scale dynamical infrastructures — any domain where the question is not “is the system safe right now?” but “can the system ever become unsafe?”

The invariant answers: under governed dynamics, no. The forbidden region is not avoided. It is unreachable.

-----

## 13. Conclusion

This paper introduced the Reachability Safety Invariant:

**ℛ(t) ∩ Ω = ∅**

which expresses safety as a geometric condition on reachable states. The invariant provides a simple and general formulation of safety applicable to dynamical systems across domains.

Safety is not what you filter from the output. Safety is what you exclude from the reachable set.

Future work will explore algorithmic methods for enforcing reachability constraints in large-scale autonomous systems and AI architectures.

-----

## Citation

```
Morrison, D. (2026). The Reachability Safety Invariant:
A Geometric Condition for Safety in Dynamical Systems.
Independent Research. UK Patent Application: GB2600765.8.

Available at: https://github.com/davarn/morrison-framework
```

**BibTeX:**

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

The Reachability Safety Invariant · Morrison Framework™ · Geometric Control Theory of Cognition

GB2600765.8

© 2026 Davarn Morrison — All Rights Reserved

-----

## Related Work

- [Geometric Control Theory of Cognition](https://github.com/davarn/morrison-framework) — The base theory from which the safety invariant derives
- [Morrison Reachability Guard — Architecture](https://github.com/davarn/morrison-framework) — Runtime enforcement of the invariant
- [Morrison Reachability Guard — Universal Plugin](https://github.com/davarn/morrison-framework) — Middleware implementation
- [The Morrison Reality Table](https://github.com/davarn/morrison-framework) — Unified expression of the full framework
- [Licensing, Citation, and IP](https://github.com/davarn/morrison-framework) — How to cite and licence
