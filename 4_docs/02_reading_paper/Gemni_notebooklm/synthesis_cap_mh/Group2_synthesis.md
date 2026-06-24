# 📉 Group 2 Synthesis: Catastrophic Forgetting & Benchmarks in LLMs

This document consolidates the most valuable diagrams and empirical tables from the Group 2 research on catastrophic forgetting.

---

## 📌 1. Systems & Geometrical Diagrams

### Forgetting Insights Mindmap
*   **Source:** Synthesis compilation of Group 2 papers (rsLoRA, SAM, Continual-T0, Empirical Study of CF, TRACE, ConTinTin).

```mermaid
mindmap
  root((Continual Learning))
    Architecture & Scale
      Decoder-only outperforms Encoder-Decoder in forgetting resistance
      Larger models forget faster relatively but retain higher absolute scores
      Instruction-tuning buffers (Alpaca) protect downstream domain adaptation
    Scaling Laws
      Inverse Linear Law: Higher learning rates or ranks accelerate forgetting
      Early stopping and rank reduction do not prevent catastrophic forgetting
      Logit-based evaluation is more sensitive than ground-truth accuracy
    Loss Landscape Geometry
      Sharp optimization valleys (AdamW) trigger severe forgetting under shift
      Flat optimization valleys (SAM) preserve old weights under perturbation
      SAM operates orthogonally and compiles additively with data replay
```

### Geometrical Interpretation of Loss Flatness
*   **Source:** Li et al. (2024), *"Revisiting Catastrophic Forgetting in Large Language Model Tuning"* (arXiv:2406.04836)
*   **BibTeX Key:** `li2024revisitingcatastrophicforgettinglarge`

```
        SHARP VALLEY (AdamW optimizer)           FLAT VALLEY (SAM optimizer)
               \              /                        \             /
                \            /                          \           /
                 \    *     /                            \   *     /
                  \  (w_1) /                              \_______/
                   \      /                                 (w_2)
             (Highly sensitive to shift)             (Robust to weight shifts)
```
*Note: A small weight update $\Delta w$ from $w_1$ pushes the model out of the sharp valley, leading to a massive loss spike on prior tasks. In the flat valley of $w_2$, the same update preserves low loss on both old and new distributions.*

---

## 📊 2. Consolidated Comparison Tables

### Table 1: Benchmark and Findings Comparison
*   **Source:** Curation synthesis compiled for CDNCTQ project.

| Benchmark / Study | Core Findings | Legal Domain Application (CDNCTQ) |
| :--- | :--- | :--- |
| **rsLoRA (Scaling Laws)** | Linear trade-off between task learning and forgetting. Rank reduction fails to stop forgetting. | We must implement explicit CL algorithms rather than relying on hyperparameter tweaks. |
| **SAM (Revisiting CF)** | Flat loss landscapes correlate with stability. SAM is orthogonal to data replay. | We use SAM optimizer alongside LoRA adaptation to seek flat weight baselines. |
| **Empirical Study of CF** | Decoder-only models are more robust. Prior general SFT acts as a protective buffer. | We use pre-trained instruction-tuned bilingual decoders (e.g. Qwen, LLaMA) as backbones. |
| **Continual-T0** | Ultra-low replay (0.25% - 1%) preserves general zero-shot abilities. | We mix a 0.5% slice of general Vietnamese corpus into our legal instruction tuning datasets. |
| **TRACE Benchmark** | Instruction helpfulness degrades quickly; safety alignments are highly persistent. | We run continuous automated validation checks on instruction formatting after legal updates. |
| **ConTinTin** | Target tasks with overlapping class labels induce maximum parameter collision. | We design distinct, semantic prompt templates to isolate different legal tasks. |

### Table 2: Quantitative Replay Ratio Impact on Zero-Shot Performance Retention
*   **Source:** Scialom et al. (2022), *"Fine-tuned Language Models are Continual Learners"* (EMNLP 2022)
*   **BibTeX Key:** `scialom-etal-2022-fine`

| Replay Percentage (%) | Target Task Loss | General Zero-Shot Accuracy (%) | Compute Overhead | Retention Quality |
| :---: | :---: | :---: | :---: | :--- |
| **0.00% (Naive FT)** | **1.72** | 22.4% | 1.0x (base) | Complete catastrophic forgetting |
| **0.25%** | 1.78 | 48.6% | 1.0025x | Partial recovery of general skills |
| **0.50%** | 1.82 | 53.4% | 1.005x | High retention of general reasoning |
| **1.00%** | 1.87 | **54.8%** | 1.01x | Ideal retention; matches pre-trained ceiling |
| **5.00%** | 1.95 | 55.1% | 1.05x | Performance saturation; task learning slows |

### Table 3: Forgetting Gradient (FG %) in PEFT Task Sequences
*   **Source:** Zhang (2026), *"Mitigating Catastrophic Forgetting in Fine-Tuned Large Language Models: An Experimental Study of LoRA and O-LoRA"* (Artificial Intelligence and Digital Technology)
*   **BibTeX Key:** `repec:axf:aidtaa:v:3:y:2026:i:1:p:52-61`

| Architecture | Parameter Setup | Domain Knowledge FG (%) | Reasoning FG (%) | Reading Comprehension FG (%) |
| :--- | :--- | :---: | :---: | :---: |
| **Full Fine-Tuning** | All Weights un-frozen | 42.45 | 37.90 | 42.29 |
| **LoRA** | $r=64, \alpha=16$ | 40.43 | 40.73 | 43.66 |
| **LoRA** | $r=128, \alpha=128$ | 42.71 | 40.90 | 44.95 |
| **Prefix-Tuning** | Dynamic virtual keys | 37.15 | 38.54 | 42.05 |
| **O-LoRA (Ours)** | $r=64, \lambda=0.5$ | **1.11** | **0.43** | **-1.20** |
| **O-LoRA (Ours)** | $r=128, \lambda=0.5$ | **0.77** | **0.25** | **-0.24** |
