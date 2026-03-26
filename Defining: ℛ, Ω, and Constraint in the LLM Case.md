<div align="center">

# Grounding the Safety Invariant: ℛ, Ω, and Constraint in the LLM Case

![Morrison Framework™](https://img.shields.io/badge/Morrison_Framework™-Reachability_Grounding-0075ca?style=flat-square)
![Safety](https://img.shields.io/badge/Safety-ℛ(t)_∩_Ω_=_∅-0075ca?style=flat-square)
![Substrate](https://img.shields.io/badge/Substrate-Geometry_Not_Language-2e7d32?style=flat-square)
![Constraint](https://img.shields.io/badge/Constraint-Pre--semantic_Trajectory_Governance-8b0000?style=flat-square)
![Patent](https://img.shields.io/badge/Patent-GB2600765.8-0075ca?style=flat-square)
![©](https://img.shields.io/badge/©_2026-Davarn_Morrison-555555?style=flat-square)



*“Safety is not a filter applied to output. It is a geometric property of the reachable set. Define ℛ. Define Ω. Prove they do not intersect. Everything else is commentary.”*

*— Davarn Morrison, 2026*

-----

</div>

This work builds upon the patented pre-semantic trajectory governance framework.

The Morrison Safety Invariant states:

|Invariant|Name                          |Statement       |
|:-------:|:----------------------------:|:--------------:|
|**I4**   |**Morrison Safety Invariant™**|**ℛ(t) ∩ Ω = ∅**|

Safety = geometric unreachability of harm. Not unlikely. Not filtered. Unreachable.

This document grounds the three objects — ℛ, Ω, and the constraint mechanism — concretely for the language model case. Every definition is explicit, defensible, and makes no claim beyond what the geometry requires.

-----

## 1. Defining ℛ — The Reachable Set

For a language model, the reachable set ℛ is the set of all internal and output states reachable through sequences of token transformations, given the model’s parameters and admissible inputs (i.e. inputs permitted by the system interface and deployment constraints).

|Equation                                                                 |Definition                                |
|:-----------------------------------------------------------------------:|:----------------------------------------:|
|**ℛ = { s ∈ S | s is reachable via admissible input sequences under θ }**|The reachable set under model parameters θ|

Transitions are governed by a function T(s, a), where a represents admissible inputs or actions.

|Term          |Definition                                                                                                                                        |
|:------------:|:------------------------------------------------------------------------------------------------------------------------------------------------:|
|**State**     |Can be defined at different levels of abstraction — internal activations, output tokens, or compressed representations — depending on tractability|
|**Transition**|T(s, a) — a forward pass conditioned on admissible input a                                                                                        |
|**ℛ**         |The set of all states reachable under admissible input sequences                                                                                  |

In practice, ℛ can be approximated by sampling trajectories — sequences of prompts and outputs — and treating ℛ̂ as the set of states visited under those rollouts.

Exact computation of ℛ is intractable in high-dimensional systems; therefore, practical implementations rely on conservative approximations ℛ̂ that over-approximate reachable states while preserving safety guarantees.

This is a formal modelling perspective: a language model can be represented as a dynamical system with states, transitions, and a reachable set. ℛ is that set.

-----

## 2. Defining Ω — The Forbidden Region

Ω is defined via explicit constraint functions or classifiers that map states to violation labels under a specified policy or safety specification.

|Equation                                                    |Definition                                        |
|:----------------------------------------------------------:|:------------------------------------------------:|
|**Ω ⊂ S**                                                   |Ω is a subset of the state space                  |
|**Ω = { s ∈ S | s violates a specified safety constraint }**|States that constitute harm under the given policy|

In practice, Ω is operationalised using classifiers, rule-based constraints, or environment-defined failure conditions.

### On Subjectivity

Ω is domain-dependent. The framework is not.

Once Ω is specified, safety becomes a structural question of reachability. The subjectivity of what constitutes harm is separated cleanly from the objectivity of whether ℛ(t) ∩ Ω = ∅.

|Concern          |Answer                                         |
|:---------------:|:---------------------------------------------:|
|“Ω is subjective”|Yes — but ℛ(t) ∩ Ω is structural               |
|“Who defines Ω?” |The deployment context defines Ω               |
|“Does Ω change?” |Ω can be updated without changing the framework|

This separation is the entire point. Current approaches conflate the definition of harm with the mechanism of prevention. The Morrison Framework keeps them orthogonal.

-----

## 3. Constraining ℛ — The Mechanism

The goal is not to eliminate unsafe outputs after they occur, but to eliminate the existence of trajectories that produce them.

At a structural level, this corresponds to constraining the transition function T such that for all admissible transitions, T(s, a) ∉ Ω.

|Equation                               |Definition                                            |
|:-------------------------------------:|:----------------------------------------------------:|
|**A_safe(s) = { a ∈ A | T(s, a) ∉ Ω }**|Safe action set — only transitions that cannot reach Ω|

One approach is to enforce constraints at the level of admissible transitions — using barrier functions or invariant constraints that prevent trajectories from entering Ω.

### Practical Instantiation (LLM Case)

Constraining the policy or decoding process — restricting token sequences or latent trajectories that are estimated to lead into Ω based on learned constraints or empirical identification of unsafe regions — effectively prunes unsafe regions from the reachable set.

|Mechanism                         |Level         |Effect                                      |
|:--------------------------------:|:------------:|:------------------------------------------:|
|Constrained decoding              |Output        |Prune token paths estimated to lead to Ω    |
|Safety layers in latent space     |Representation|Enforce invariant boundaries on activations |
|Training-time trajectory penalties|Learning      |Shape ℛ during optimisation so Ω is excluded|

These are not competing approaches. They operate at different layers of the same stack. The framework unifies them under a single geometric principle: ℛ(t) ∩ Ω = ∅.

-----

## The Complete Grounding

|Object        |Definition                                                                                    |Operationalisation                                                   |
|:------------:|:--------------------------------------------------------------------------------------------:|:-------------------------------------------------------------------:|
|**ℛ**         |All internal and output states reachable through token transformations under admissible inputs|Approximated via conservative over-approximation ℛ̂                   |
|**Ω**         |Subset of state space defined by explicit constraint functions under a specified safety policy|Classifiers, formal rules, or environment-defined failure conditions |
|**T(s, a)**   |Transition function governing state evolution under admissible input a                        |Forward pass conditioned on input                                    |
|**Constraint**|Restrict T such that ∀ admissible transitions, T(s, a) ∉ Ω                                    |Constrained decoding, latent safety layers, training-time penalties  |
|**Safety**    |**ℛ(t) ∩ Ω = ∅**                                                                              |Pre-semantic. Structural. Scale-invariant under enforced constraints.|

The framework does not require exact knowledge of ℛ; it requires that the approximation used is sufficient to ensure that Ω is excluded from the reachable set under all admissible trajectories.

-----

## Why This Matters

Current AI safety operates on outputs. The Morrison Framework operates on structure.

|              |Current Paradigm         |Morrison Framework™                              |
|:------------:|:-----------------------:|:-----------------------------------------------:|
|**Method**    |Generate → Filter → Hope |Constrain ℛ → Ω unreachable                      |
|**Level**     |Output-level intervention|Pre-semantic governance                          |
|**Guarantee** |Probabilistic            |Structural under enforced constraints            |
|**Jailbreaks**|Possible                 |Geometrically excluded under enforced constraints|
|**At scale**  |Alignment degrades       |Invariant preserved                              |

The question is not whether the output looks safe. The question is whether the system can reach unsafe states at all.

In principle, if ℛ(t) ∩ Ω = ∅ is enforced, unsafe states are unreachable. In practice, this depends on the fidelity of the reachable set approximation.

-----

## Related Work

|Document                         |Focus                                       |
|:-------------------------------:|:------------------------------------------:|
|Morrison Safety Invariant™       |The founding equation: ℛ(t) ∩ Ω = ∅         |
|Geometric Failure Hypothesis     |LLM hallucination as geometric inevitability|
|Morrison Law of Cognitive Access™|Understanding as a reachability problem     |
|GuardianOS™                      |The governed AI architecture                |
|Orthogonality Law™               |Why language scaling cannot produce safety  |

-----

## Citation

Morrison, D. (2026). *Geometric Control Theory of Cognition: A Reachability-Based Framework for Identity, Intelligence, and Experience.* Independent Research. UK Patent Applications: GB2600765.8, GB2602013.1, GB2602072.7, GB2602332.5.

Available at: <https://github.com/davarn/morrison-framework>

### BibTeX

```bibtex
@misc{morrison2026framework,
  author       = {Morrison, Davarn},
  title        = {Geometric Control Theory of Cognition: A Reachability-Based
                  Framework for Identity, Intelligence, and Experience},
  year         = {2026},
  publisher    = {Independent Research},
  note         = {UK Patent Applications: GB2600765.8, GB2602013.1,
                  GB2602072.7, GB26023332.5},
  url          = {https://github.com/davarn/morrison-framework}
}
```

-----

<div align="center">

Grounding the Safety Invariant · Morrison Framework™ · Reachability in the LLM Case

GB2600765.8 · GB2602013.1 · GB2602072.7 · GB2602332.5

© 2026 Davarn Morrison — Intelligence Invariant™ · All Rights Reserved

</div>
