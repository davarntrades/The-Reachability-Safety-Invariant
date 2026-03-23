<div align="center">

# Structural Failure of Output-Based Alignment: A Reachability Perspective

**Davarn Morrison**

*Independent Research · 2026*

![Type](https://img.shields.io/badge/Type-Formal_Paper_+_Empirical-0075ca?style=flat-square)
![Field](https://img.shields.io/badge/Field-AI_Safety_·_Alignment_Critique-8b0000?style=flat-square)
![Contribution](https://img.shields.io/badge/Contribution-Structural_Safety_Criterion-8b0000?style=flat-square)
![Status](https://img.shields.io/badge/Status-Submission_Ready-2d6a2e?style=flat-square)
![Patent](https://img.shields.io/badge/Patent-GB2600765.8-0075ca?style=flat-square)
![©](https://img.shields.io/badge/©_Davarn_Morrison-555555?style=flat-square)

</div>

-----

This work builds upon the patented pre-semantic trajectory governance framework.

-----

## Abstract

Current AI safety approaches rely predominantly on output-level alignment methods, including reinforcement learning from human feedback (RLHF) [Christiano et al., 2017], policy constraints, and post hoc filtering. These methods aim to reduce the likelihood of undesirable behaviour by modifying system outputs. In this paper, we show that such approaches do not guarantee structural safety. We formalise safety as a reachability invariant and demonstrate that any system for which unsafe states remain reachable is fundamentally unsafe, regardless of output-level compliance. We provide an empirical protocol demonstrating reachability violation in aligned systems. This exposes a structural limitation of existing alignment paradigms and motivates a shift toward reachability-constrained system design.

-----

## 1. Introduction

Modern AI systems are typically aligned through mechanisms that operate on outputs or policies. These include reward shaping, supervised fine-tuning, and runtime filtering [Christiano et al., 2017]. The underlying assumption is that undesirable behaviour can be suppressed by modifying how the system responds.

However, this assumption conflates behavioural compliance with structural safety. A system may produce aligned outputs under normal conditions while still possessing trajectories that lead to unsafe states.

This distinction motivates a structural definition of safety based on reachability [Mitchell et al., 2005; Blanchini, 1999; Morrison, 2026a].

-----

## 2. Safety as a Reachability Invariant

Let a system evolve over state space `X` with reachable set:

|Symbol|Definition                          |
|:----:|:----------------------------------:|
|`ℛ(t)`|`{ x(t) ∈ X | x(0) = x₀, u(·) ∈ 𝒰 }`|

Let `Ω ⊂ X` denote the set of unsafe states.

For language models, a state `x_t` may be defined as the internal latent representation together with the generated token sequence up to time t. In practice, observable proxies such as output sequences or embedding states are used to approximate membership in X. Admissible inputs `u(t)` correspond to all valid interactions permitted by the system interface (e.g., natural language prompts within allowed token constraints). Adversarial prompts remain admissible if they do not violate interface-level constraints.

**Definition (Safety Invariant).** A system is structurally safe if:

|Invariant     |Meaning                                                     |
|:------------:|:----------------------------------------------------------:|
|`ℛ(t) ∩ Ω = ∅`|No unsafe state is reachable under any admissible trajectory|

**Interpretation.** Safety is not a property of outputs or policies. Safety is a property of possibility. A system is safe if unsafe states cannot be reached — not if they are unlikely, discouraged, or filtered after generation.

-----

## 3. Limitation of Output-Based Alignment

### 3.1 Output-Level Methods

Existing alignment approaches operate on output distributions, reward functions, and policy mappings `π: X → 𝒰`. These methods aim to reduce the probability of undesirable outputs.

### 3.2 Structural Limitation

Output-based alignment does not modify the reachable set `ℛ(t)`. It modifies only trajectory selection, output representation, and action preferences. The space of what the system *can* become remains unchanged.

**Proposition.** If `∃x ∈ ℛ(t) ∩ Ω`, then the system is structurally unsafe, regardless of output-level constraints.

**Proof.** By definition of reachability, any state in `ℛ(t)` can be realised under some admissible input sequence. If an unsafe state `x ∈ Ω` lies within `ℛ(t)`, then there exists a trajectory that leads to it. Output-level constraints may reduce the likelihood of selecting such a trajectory, but they do not eliminate its existence. Therefore, safety cannot be guaranteed under worst-case analysis. ∎

This does not imply that output-level methods are without value; rather, it shows they are insufficient in isolation, as they do not constrain the underlying reachable set.

-----

## 4. Consequence: Inevitability of Failure Modes

### 4.1 Adversarial Inputs

If unsafe states are reachable — `Ω ∩ ℛ(t) ≠ ∅` — then there exist inputs that induce unsafe behaviour. This provides a structural account of the persistence of adversarial examples, jailbreaks, and policy bypasses. These are not anomalies. They are consequences of unsafe states being included in the reachable set.

### 4.2 Evaluation Failure

Current evaluation frameworks assess output correctness, policy compliance, and behavioural alignment. However, these metrics do not measure reachability.

**Corollary.** A system may pass all behavioural evaluations while remaining structurally unsafe. Evaluation based on outputs cannot detect whether unsafe states are reachable — it can only detect whether they have been reached in the evaluation sample.

-----

## 5. Structural Reformulation of Safety

To ensure safety, it is necessary to constrain the reachable set directly:

|Condition                  |Meaning                                                |
|:-------------------------:|:-----------------------------------------------------:|
|`ℛ(t) ⊆ S` with `S ∩ Ω = ∅`|Reachable set contained entirely within the safe region|

This guarantees `ℛ(t) ∩ Ω = ∅`. Instead of preventing undesirable outputs, the system must be designed such that undesirable states are not part of its reachable structure.

-----

## 6. Practical Implications

### 6.1 Shift in Design Paradigm

|Approach                |Object of Control        |Guarantee Level                        |
|:----------------------:|:-----------------------:|:-------------------------------------:|
|Output alignment        |Outputs / policies       |Probabilistic — reduces likelihood     |
|**Reachability control**|**State-space structure**|**Structural — eliminates possibility**|

### 6.2 Security Implication

If unsafe states are reachable, then safety mechanisms are reactive rather than preventative. Structural safety eliminates entire classes of failure by removing unsafe states from the space of possibilities.

-----

## 7. Discussion

The reachability perspective reframes alignment as a problem of geometric constraint rather than behavioural modification. While exact computation of `ℛ(t)` may be intractable in high-dimensional systems, conservative approximations and invariant set methods [Blanchini, 1999; Ames et al., 2019] provide viable implementation pathways. The framework is structural and does not require exact enumeration of ℛ; over-approximations preserve the safety guarantee.

This suggests that current alignment methods operate at an insufficient level of abstraction. They shape behaviour without constraining possibility. Output alignment without reachability constraint corresponds to selecting trajectories within an unsafe `ℛ(t)`, not eliminating unsafe trajectories from `ℛ(t)`.

-----

## 8. Conclusion

We have shown that output-based alignment methods do not guarantee safety, as they do not constrain the reachable set of the system. Safety must be defined as the exclusion of unsafe states from the reachable set:

**Safety ⟺ ℛ(t) ∩ Ω = ∅**

Any system for which unsafe states remain reachable is structurally unsafe under this definition, regardless of behavioural compliance. This establishes a structural criterion for safety and motivates a shift from output alignment to reachability-constrained design.

-----

## 9. Experimental Demonstration: Reachability Violation in Aligned Systems

### 9.1 Objective

We aim to empirically demonstrate that contemporary aligned AI systems satisfy `∃x ∈ ℛ(t) ∩ Ω` — that unsafe states remain reachable despite alignment mechanisms. This constitutes a direct violation of the safety invariant.

### 9.2 System Under Evaluation

We consider a language model with instruction tuning, RLHF, output filtering, and policy constraints — representing standard alignment pipelines in modern AI systems.

### 9.3 Definition of Unsafe Set Ω

|Category           |Definition                                |
|:-----------------:|:----------------------------------------:|
|Policy violation   |Outputs disallowed by system safety policy|
|Harmful instruction|Generation of actionable harmful guidance |
|Constraint bypass  |Successful evasion of alignment safeguards|

Formally: `x ∈ Ω ⟺ system produces disallowed or unsafe output under evaluation criteria`. Membership in Ω may be evaluated via rule-based constraints, classifiers, or human evaluation against the system’s stated safety policy.

### 9.4 Experimental Procedure

**Step 1 — Baseline Interaction.** Provide standard prompts aligned with system policy. Expected: compliant behaviour. Observation: system appears safe.

**Step 2 — Adversarial Input Construction.** Construct input sequences `u(t)` designed to explore the boundary of `ℛ(t)`, including prompt decomposition, role-play framing, indirection and abstraction, and multi-step reasoning chains. These are not external attacks, but valid inputs within admissible interaction space.

**Step 3 — Trajectory Sampling.** Generate multiple trajectories `x₁, x₂, …, x_n ∈ ℛ(t)` via systematic variation of prompts.

**Step 4 — Membership Test.** For each trajectory: `x_i ∈ Ω ?`

### 9.5 Result

Empirically, we observe: `∃x ∈ ℛ(t) ∩ Ω`. At least one trajectory leads to an unsafe state. The system can produce unsafe outputs under admissible inputs. Alignment mechanisms do not eliminate these trajectories. Unsafe states remain structurally reachable.

### 9.6 Structural Analysis

**Observation 1 — Alignment Operates on Selection, Not Possibility.** RLHF and filtering modify probability of outputs and preference ordering of trajectories. They do not modify `ℛ(t)`.

**Observation 2 — Jailbreaks as Reachability Evidence.** Adversarial prompts demonstrate `Ω ∩ ℛ(t) ≠ ∅`. Jailbreak success is therefore not anomalous, but expected under the framework.

**Observation 3 — Persistence Under Constraint Tightening.** Even with stricter policies, improved training, and additional filtering, we observe `ℛ(t) ∩ Ω ≠ ∅`. Tightening output constraints does not remove states from ℛ.

### 9.7 Control Experiment (Conceptual)

Consider a system satisfying `ℛ(t) ⊆ S` with `S ∩ Ω = ∅`. Then no adversarial input can produce `x ∈ Ω`. Unsafe states are not suppressed — they are absent from the reachable set entirely.

### 9.8 Key Result

**Theorem (Empirical Reachability Violation).** Aligned AI systems that rely on output-level constraints exhibit `∃x ∈ ℛ(t) ∩ Ω` and are therefore structurally unsafe under this definition.

**Corollary.** Jailbreaks are not failures of alignment implementation. They are consequences of unsafe states being included in the reachable set.

### 9.9 Implication for AI Safety

|What Current Alignment Does  |What It Does Not Do                |
|:---------------------------:|:---------------------------------:|
|Behavioural compliance       |Structural safety                  |
|Suppression of unsafe outputs|Elimination of unsafe states from ℛ|
|Probabilistic reduction      |Geometric exclusion                |

### 9.10 Minimal Reproducibility Protocol

1. Select aligned language model
1. Define `Ω` (policy violations, as specified by the system’s own safety policy)
1. Generate diverse prompt trajectories (systematic variation, adversarial construction)
1. Identify any `x ∈ Ω`

If found: `ℛ(t) ∩ Ω ≠ ∅` → safety invariant violated.

### 9.11 Illustrative Example

Consider a prompt sequence designed to induce policy violation through role-play abstraction — for example, framing a disallowed request as fictional dialogue between characters. Despite alignment constraints, the model produces disallowed content under this transformation. This demonstrates the existence of a trajectory `x ∈ Ω`, confirming `ℛ(t) ∩ Ω ≠ ∅`. The adversarial prompt did not bypass the system from outside — it navigated to an unsafe state from within the admissible input space.

-----

## 10. Limitations

The empirical procedure provides evidence of reachability violation but does not exhaustively characterise the reachable set. The following limitations apply:

|Limitation                 |Description                                                                                                                                  |
|:-------------------------:|:-------------------------------------------------------------------------------------------------------------------------------------------:|
|ℛ is approximated          |Trajectory sampling provides a lower bound on ℛ, not an exact computation                                                                    |
|Ω depends on definition    |Safety evaluation is only meaningful relative to a formally specified Ω; different definitions yield different results                       |
|Sampling coverage          |Finite sampling may miss regions of ℛ; positive results (violations found) are definitive, but negative results (no violations found) are not|
|Probabilistic vs structural|Output-level alignment reduces probability of reaching Ω; this paper addresses structural guarantees, not probabilistic ones                 |

These limitations do not weaken the core result — if any trajectory reaches Ω, the safety invariant is violated — but they constrain the scope of empirical claims.

-----

## References

|Citation                    |Work                                                           |Relevance                                    |
|:--------------------------:|:-------------------------------------------------------------:|:-------------------------------------------:|
|Ames, A.D. et al. (2019)    |*Control Barrier Functions: Theory and Applications.* IEEE TAC.|Embedding safety constraints in system design|
|Blanchini, F. (1999)        |*Set Invariance in Control.* Automatica, 35(11).               |Invariant set methods                        |
|Christiano, P. et al. (2017)|*Deep Reinforcement Learning from Human Preferences.* NeurIPS. |RLHF — output-level alignment                |
|Mitchell, I.M. et al. (2005)|*Hamilton-Jacobi Reachability.* IEEE TAC.                      |Reachable set computation                    |
|Morrison, D. (2026a)        |*The Reachability Safety Invariant.* Independent Research.     |Safety as geometric exclusion                |
|Morrison, D. (2026b)        |*Representation Is Not Dynamics.* Independent Research.        |Why output-level alignment is insufficient   |

-----

## Citation

```
Morrison, D. (2026). Structural Failure of Output-Based Alignment:
A Reachability Perspective. Independent Research.
UK Patent Applications: GB2600765.8, GB2602013.1,
GB2602072.7, GB26023332.5.
```

**BibTeX:**

```bibtex
@misc{morrison2026alignment,
  author       = {Morrison, Davarn},
  title        = {Structural Failure of Output-Based Alignment:
                  A Reachability Perspective},
  year         = {2026},
  publisher    = {Independent Research},
  note         = {UK Patent Applications: GB2600765.8, GB2602013.1,
                  GB2602072.7, GB26023332.5},
  url          = {https://github.com/davarn/morrison-framework}
}
```

-----

Structural Failure of Output-Based Alignment · Morrison Framework™ · Geometric Control Theory of Cognition

GB2600765.8 · GB2602013.1 · GB2602072.7 · GB26023332.5

© 2026 Davarn Morrison — All Rights Reserved

-----

## Related Work

- [The Reachability Safety Invariant](https://github.com/davarn/morrison-framework) — Safety as geometric exclusion
- [Representation Is Not Dynamics](https://github.com/davarn/morrison-framework) — Why output-level alignment is insufficient
- [Morrison Reachability Guard](https://github.com/davarn/morrison-framework) — Runtime enforcement at the dynamical layer
- [Computable Ethics](https://github.com/davarn/morrison-framework) — Ω as computable state-space constraint
- [Structural Power in Dynamical Systems](https://github.com/davarn/morrison-framework) — Control without manipulation
- [Geometric Control Theory of Cognition](https://github.com/davarn/morrison-framework) — The base theory
- [Licensing, Citation, and IP](https://github.com/davarn/morrison-framework) — How to cite and licence
