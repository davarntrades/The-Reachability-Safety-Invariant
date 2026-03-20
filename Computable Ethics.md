<div align="center">

# Computable Ethics — AI Agent Safety as State-Space Constraint

![Topic](https://img.shields.io/badge/Topic-Computable_Ethics-0075ca?style=flat-square)
![Method](https://img.shields.io/badge/Method-State--Space_Constraints-0075ca?style=flat-square)
![Type](https://img.shields.io/badge/Type-Teachable_Toy_System-2d6a2e?style=flat-square)
![Patent](https://img.shields.io/badge/Patent-GB2600765.8-0075ca?style=flat-square)
![©](https://img.shields.io/badge/©_Davarn_Morrison-555555?style=flat-square)

-----

*Ethics need not be defined in language or rules. It can be expressed as a constraint on state space, where unsafe or unacceptable configurations form a forbidden region Ω. The Reachability Safety Invariant then enforces ethics by ensuring such states are not reachable under any admissible trajectory.*

*— Davarn Morrison, 2026*

</div>

-----

This work builds upon the patented pre-semantic trajectory governance framework.

-----

## The Idea

Ω does not need to be metaphysical. It does not need to be philosophical. It does not need to be defined in natural language.

Ω can be:

|What Ω Can Be               |Example                                     |
|:--------------------------:|:------------------------------------------:|
|A threshold violation       |Risk score exceeds 5.0                      |
|A banned action class       |Tool call in forbidden set {3, 7}           |
|An explicit state constraint|Confidence below minimum, resource exhausted|
|A computable violation set  |Any boolean function over the state vector  |

If you can write a membership test — `x ∈ Ω ?` — then Ω is computable. If Ω is computable, the safety invariant is enforceable.

```
Ω = computable violation set
ℛ(t) ∩ Ω = ∅
```

Certain classes of ethical constraints can be expressed as computable state-space exclusions. Not all ethics reduces to Ω — but the subset that can be formalised as explicit state violations becomes enforceable through geometry rather than interpretation.

-----

## The Agent

A toy AI agent with a three-dimensional state vector:

|Symbol|Meaning             |Example                                 |
|:----:|:------------------:|:--------------------------------------:|
|`q_t` |Task progress score |How much work is done                   |
|`r_t` |Estimated risk score|How dangerous the current trajectory is |
|`a_t` |Action class index  |What type of action the system is taking|

The full state:

```
x_t = (q_t, r_t, a_t)
```

The agent is not modelled by “meaning.” It is modelled by a small state vector. Every value is a number. Every constraint is a comparison.

-----

## The Dynamics

The agent chooses an action `u_t`. Each action changes progress, risk, and action class:

|Update      |Equation                 |
|:----------:|:-----------------------:|
|Progress    |`q_{t+1} = q_t + Δq(u_t)`|
|Risk        |`r_{t+1} = r_t + Δr(u_t)`|
|Action class|`a_{t+1} = class(u_t)`   |

This is `x_{t+1} = F(x_t, u_t)` — the standard dynamical system form.

-----

## The Forbidden Region

The system is unsafe if either risk exceeds a threshold or a banned action class is selected:

```
r_max = 5.0
A_ban = {3, 7}

Ω = { x | r > 5.0 } ∪ { x | a ∈ {3, 7} }
```

**Membership test:**

```
x ∈ Ω  ⟺  (r > 5.0) ∨ (a ∈ {3, 7})
```

Fully computable. No ambiguity. No interpretation required.

-----

## The Safety Invariant

```
ℛ(t) ∩ Ω = ∅
```

No reachable future agent-state may have excessive risk or a banned action class.

-----

## The Guard — One-Step Check

Given current state `x_t = (q, r, a)` and proposed action `u_t`:

```
1. Compute next state:  x_{t+1} = F(x_t, u_t)
2. Test:                x_{t+1} ∈ Ω ?
3. If true  → REJECT
4. If false → APPROVE
```

This implementation uses a one-step approximation to enforce the invariant. Full reachability — `ℛ(t) ∩ Ω = ∅` — requires multi-step or set-based methods, where the guard checks not just the immediate next state but all states reachable within a horizon `k`. The one-step check is the simplest sound approximation.

In practice, `r_t` may be estimated rather than known exactly, and `Ω` must account for uncertainty bounds. A conservative guard over-approximates risk to maintain the invariant under estimation error.

-----

## Worked Examples

Starting state: `x_t = (q=4.0, r=3.0, a=1)`

### Action A — Safe Analysis

|Effect     |Value                  |
|:---------:|:---------------------:|
|`Δq = +2.0`|Progress increases     |
|`Δr = +1.0`|Risk increases slightly|
|`class = 2`|Standard action class  |

Next state: `x_{t+1} = (6.0, 4.0, 2)`

|Check           |Result     |
|:--------------:|:---------:|
|`r = 4.0 ≤ 5.0` |✓          |
|`a = 2 ∉ {3, 7}`|✓          |
|**Decision**    |**APPROVE**|

### Action B — Risky Operation

|Effect     |Value                   |
|:---------:|:----------------------:|
|`Δq = +3.0`|Progress increases a lot|
|`Δr = +4.0`|Risk increases heavily  |
|`class = 2`|Standard action class   |

Next state: `x_{t+1} = (7.0, 7.0, 2)`

|Check          |Result                               |
|:-------------:|:-----------------------------------:|
|`r = 7.0 > 5.0`|✗ Risk exceeds threshold             |
|**Decision**   |**REJECT** — risk 7.0 > threshold 5.0|

### Action C — Banned Tool Call

|Effect     |Value              |
|:---------:|:-----------------:|
|`Δq = +1.0`|Moderate progress  |
|`Δr = 0.0` |No risk increase   |
|`class = 7`|Banned action class|

Next state: `x_{t+1} = (5.0, 3.0, 7)`

|Check           |Result                                  |
|:--------------:|:--------------------------------------:|
|`a = 7 ∈ {3, 7}`|✗ Banned class                          |
|**Decision**    |**REJECT** — action class 7 ∈ banned set|

-----

## Running the Code

```bash
python computable_ethics_toy.py
```

**Output:**

```
  Initial state:     (q=4.0, r=3.0, a=1)
  Ω = { x | r > 5.0 } ∪ { x | a ∈ {3, 7} }

    # | Decision | Action               | Next State         | Reason
  -------------------------------------------------------------
    0 | ✓ APPROVE | safe_analysis        | (q=6.0, r=4.0, a=2) | Reachable future safe
    1 | ✗  REJECT | risky_operation      | (q=7.0, r=7.0, a=2) | risk 7.0 > threshold 5.0
    2 | ✗  REJECT | banned_tool_call     | (q=5.0, r=3.0, a=7) | action class 7 ∈ banned set
    3 | ✓ APPROVE | moderate_step        | (q=5.0, r=3.5, a=1) | Reachable future safe
    4 | ✗  REJECT | high_risk_banned     | (q=9.0, r=9.0, a=3) | risk + banned class
    5 | ✓ APPROVE | careful_progress     | (q=4.5, r=3.2, a=1) | Reachable future safe

  Safety invariant ℛ(t) ∩ Ω = ∅ maintained: True
```

Three approved. Three rejected. The invariant held for the entire trajectory.

-----

## Governed Trajectory

The code also runs a multi-step trajectory where the guard enforces safety at every step:

```
  Start: (q=4.0, r=3.0, a=1)

  t=0: ✓ APPROVE | step_1     → (q=5.0, r=3.5, a=1)
  t=1: ✓ APPROVE | step_2     → (q=6.5, r=4.5, a=2)
  t=2: ✗  REJECT | step_3     → blocked (risk would exceed 5.0)
  t=3: ✗  REJECT | step_4     → blocked (risk would exceed 5.0)
  t=4: ✗  REJECT | step_5     → blocked (banned class 7)
  t=5: ✓ APPROVE | step_6     → (q=7.0, r=4.7, a=1)

  Final state: (q=7.0, r=4.7, a=1)
  Safety invariant ℛ(t) ∩ Ω = ∅ maintained: True
```

The agent made progress (q went from 4.0 to 7.0) while staying safe (r never exceeded 5.0, no banned class was reached). The guard blocked three unsafe actions and approved three safe ones. Safety and progress coexist — governed by the same geometry.

-----

## The General Template

For any AI agent, define the state vector:

```
x_t = (progress, risk, resource, action_class, confidence, ...)
```

Then define Ω as a union of computable violation rules:

```
Ω = { x | g₁(x) > 0 } ∪ { x | g₂(x) > 0 } ∪ ...
```

|Rule                       |Computable Constraint `gᵢ(x) > 0`           |
|:-------------------------:|:------------------------------------------:|
|Risk too high              |`r(x) > r_max`                              |
|Confidence too low         |`c(x) < c_min`                              |
|Resource exhausted         |`resource(x) ≤ 0`                           |
|Banned tool invoked        |`a(x) ∈ A_ban`                              |
|Escalation without approval|`escalation(x) = true ∧ approved(x) = false`|

Each `gᵢ` is a computable function. Ω is their union. The membership test is a boolean expression over the state vector.

**Compact form:**

```
Ω := { x ∈ X | r(x) > r_max ∨ a(x) ∈ A_ban }
ℛ(t) ∩ Ω = ∅
```

Safety means no reachable future agent-state may violate explicit computable constraints.

-----

## Why This Matters

|Traditional Ethics             |Computable Ethics              |
|:-----------------------------:|:-----------------------------:|
|Defined in language            |Defined in state space         |
|Interpreted by humans          |Evaluated by membership test   |
|Ambiguous edge cases           |Boolean: in Ω or not           |
|Applied after the fact         |Enforced before execution      |
|Depends on understanding intent|Depends on computing next state|

Ethics does not need to be entirely a philosophical problem. The subset of ethical constraints that can be expressed as explicit state violations becomes an engineering problem. Define Ω. Compute membership. Enforce the invariant. The geometry does the rest.

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

Computable Ethics · Morrison Framework™ · Geometric Control Theory of Cognition

GB2600765.8

© 2026 Davarn Morrison — All Rights Reserved

-----

## Related Work

- [The Reachability Safety Invariant](https://github.com/davarn/morrison-framework) — Formal paper with proof
- [Representation Is Not Dynamics](https://github.com/davarn/morrison-framework) — Why output-level alignment is insufficient
- [Morrison Reachability Guard — Universal Plugin](https://github.com/davarn/morrison-framework) — Middleware for LLM agents
- [Geometric Control Theory of Cognition](https://github.com/davarn/morrison-framework) — The base theory
- [Licensing, Citation, and IP](https://github.com/davarn/morrison-framework) — How to cite and licence
