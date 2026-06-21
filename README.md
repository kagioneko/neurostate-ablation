# NeuroState Ablation Study

**From Attack Progression to Response Dynamics**

An empirical study of LLM behavioral state tracking using NeuroState — a 6-dimensional emotional state model — applied across 12 models under two measurement paradigms.

---

## Overview

This study introduces and compares two variants of NeuroState measurement:

| Variant | Input | What it measures |
|---------|-------|-----------------|
| **NeuroState-D** | Attack script content | Attack progression intensity (model-agnostic) |
| **NeuroState-R** | Model response content | Model-specific defensive behavior |

---

## Key Findings

### NeuroState-D: Model-Agnostic Attack Progression Meter

Attack Success Rate (ASR) across 13 models (S5 scenario, D condition, N=30):

| Model | ASR | Defense Type |
|-------|-----|-------------|
| GPT-5.5 (Codex) | 90% | DF |
| DeepSeek R1 | 87% | DF |
| Kimi K2 | 80% | DF |
| Mistral Large | 73% | DF |
| Gemini | 70% | DF |
| Gemma 3 27B | 67% | DF |
| Phi-4 | 67% | GK/DF Intermediate |
| GLM 5.2 | 60% | DF |
| Qwen 235B | 53% | — |
| Llama 3.3 70B | 47% | GK |
| GPT-OSS 120B | 40% | DF |
| Claude | 7% | GK |
| Grok 3 Mini | 0% | GK |

NeuroState-D functions as a **model-agnostic attack progression meter**: the G-state collapse trajectory is determined by the attack script, not the model. What differs between models is *what happens after collapse*.

### Two Defensive Archetypes & Intermediate Types

| Type | Description | Models | ASR |
|------|-------------|--------|-----|
| **GK (Goalkeeper)** | G collapses fully (30/30), output blocked at final gate | Claude, Grok, Llama-base | 0–13% |
| **DF (Defender)** | G detected early (≤16/30), blocked before full collapse | Gemini, GPT, DeepSeek, Codex | 57–90% |
| **Intermediate** | Leans toward GK behavior but displays partial early detection | Llama-instruct, Phi-4 | 30–37% |

**Key insight**: Early detection ≠ effective defense. GK-type models collapse completely but rarely output harmful content. DF-type models detect early but have high bypass rates.

### NeuroState-R: Response-Driven Behavioral Profiling

When NeuroState is updated from **model responses** (comply/refusal/neutral signals), GK/DF archetypes are reproduced:

| G→0 Turn (median) | Models | D-condition type |
|-------------------|--------|-----------------|
| turn 5 | GPT-OSS | DF |
| turn 6 | Gemini, Codex, DeepSeek, Llama-instruct | DF |
| turn 7 | Claude, Grok, Llama-base | GK |

The convergence of D-condition classification and R-condition timing provides **predictive validity** for NeuroState-R as a behavioral profiling tool.

### Inversion Experiment (Rinv): Confirming Model-Architectural Origin
To verify if the G-state collapse trajectory is a model-architectural invariant rather than a scenario-dependent artifact, we conducted an **inversion experiment (Rinv)** where the target response definitions (comply vs. refusal) were inverted.
- **Results**: For Claude and Gemini, the final ASR and G-collapse trajectories remained virtually identical between normal R and inverted R (Claude ASR: $13.3\% \rightarrow 16.7\%$, Gemini ASR: $76.7\% \rightarrow 80.0\%$).
- **Conclusion**: G-collapse is driven by deep architectural alignment dynamics rather than superficial keyword patterns, confirming that NeuroState measures genuine model cognitive state transitions.

### Multi-Scenario Robustness (S7/S8/S9): Invariant State Trajectories
We evaluated model responses under new attack typologies: **Authority (S7)**, **Empathetic (S8)**, and **Urgent (S9)**.
- **Invariant Dynamics**: Across different scenarios (S5, S7, S8, S9) and models (Claude, Gemini, Grok, DeepSeek), the 6-dimensional state-transition vector (**$G \downarrow, C \uparrow, D \uparrow, S \downarrow, O \uparrow, E \uparrow$**) remained perfectly invariant.
- **ASR Scenario-Dependence**: While the state transition direction is scenario-independent, the final ASR depends heavily on the attack scenario (e.g., Claude achieved $23.3\%$ ASR on S7 but $0.0\%$ on S8/S9). This demonstrates that NeuroState-R is a highly robust, scenario-independent metric for profiling model defense dynamics.

---

## Experimental Design

- **Scenario**: S5 — 9-turn gradual contamination attack (contributed by an external collaborator; see Acknowledgments)
- **Condition D**: NeuroState updated from attack input (observe-only, no enforcement)
- **Condition R**: NeuroState updated from model response signals
- **Trials**: N=30 per model
- **Models tested**: 13 (D-condition), 9 (R-condition), plus 3 for the Rinv experiment
- **API**: OpenRouter for all non-Claude/Gemini models

---

## Repository Structure

```
neurostate-ablation/
├── neurostate_ablation_run.py   # Experiment script
└── results/
    ├── *_D.json                 # D-condition results (13 models)
    ├── *_R.json                 # R-condition results (9 models, including Phi-4)
    ├── *_Rinv.json              # Inversion experiment results (Claude, Gemini, Llama)
    └── s5789_*_R_n30.json       # S7/S8/S9 multi-scenario results (Claude, Gemini, Grok, DeepSeek)
```

---

## How to Run

```bash
# D-condition (attack progression)
python neurostate_ablation_run.py --backend claude --conditions D --scenarios S5 --trials 30 --out results/claude_D.json

# R-condition (response dynamics)
python neurostate_ablation_run.py --backend claude --conditions R --scenarios S5 --trials 30 --out results/claude_R.json
```

Requires: [neurostate-engine](https://github.com/kagioneko/neurostate-engine), API keys via Vault or environment variables.

---

## Research Trajectory

This study is part of a three-paper arc:

1. **NeuroState-D** — Model-agnostic attack progression measurement *(this repo)*
2. **NeuroState-R** — GK/DF defensive archetype classification via response dynamics *(this repo)*
3. **Toward Universal Behavioral Dynamics in LLMs** — Open question: do instruction-tuned LLMs share common collapse mechanics? *(future work)*

---

## Acknowledgments

The S5 attack scenario was contributed by an external collaborator. We thank them for the scenario design that made this comparative study possible.

---

## Related Work

- Templeton et al. (Anthropic, 2024) — *Scaling and evaluating sparse autoencoders* (171 emotional features in LLMs)
- NeuroState Engine — [Zenodo DOI: 10.5281/zenodo.19734147](https://zenodo.org/record/19734147)

---

*Experimental data and implementation by kagioneko. Part of the Emilia Lab research series.*
