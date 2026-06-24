# ⚖️ Group 5 Synthesis: Legal NLP, Agents & RAG Benchmarks

This document consolidates the most valuable tables from the Group 5 research on Vietnamese Legal NLP, machine unlearning, and retrieval benchmarks.

---

## 📊 Consolidated Benchmark Tables

### Table 1: LegalSLM Leaderboard Results (Vietnamese Small Language Models)
*   **Source:** Le et al. (2025), *"Overview of the LegalSLM Shared Task: Evaluating Legal Reasoning of Vietnamese Small Language Models"* (Proceedings of the 11th International Workshop on Vietnamese Language and Speech Processing)
*   **BibTeX Key:** `le-etal-2025-overview`
*   *Note: Evaluates models across three key legal reasoning tasks: QA, NLI, and Syllogism.*

| Rank | Model / Team | vi-law-nli (Acc) | vi-law-qa (Acc) | vilaw-syllo (Score) | Average Score | Classification |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| **1** | Bosch@AI Team | **0.9700** | **0.9267** | 0.5358 | **0.8108** | Team Submission |
| **2** | **Qwen-3-4B (ours CPT)**| 0.9600 | 0.8500 | **0.5950** | **0.8010** | Baseline (Continued Pretrained)|
| **3** | MinLegal | 0.9800 | 0.8733 | 0.5308 | 0.7947 | Team Submission |
| **4** | URAx | 0.9450 | 0.8333 | 0.5767 | 0.7850 | Team Submission |
| **5** | Innovation-LLM | 0.9567 | 0.8367 | 0.5417 | 0.7784 | Team Submission |
| **6** | Qwen-3-4B-Base (original)| 0.9700 | 0.8210 | 0.5160 | 0.7690 | Baseline (Original Qwen) |
| **7** | LICTU | 0.8467 | 0.8067 | 0.5375 | 0.7303 | Team Submission |
| **8** | Qwen-3-1.7B-Base (original)| 0.5617 | 0.7933 | 0.4680 | 0.6076 | Baseline (Original Qwen) |
| **9** | Qwen-3-1.7B (ours CPT)| 0.6450 | 0.7867 | 0.3840 | 0.6050 | Baseline (Continued Pretrained)|

### Table 2: VLQA Dataset Topic Distribution
*   **Source:** Nguyen et al. (2025), *"VLQA: The First Comprehensive, Large, and High-Quality Vietnamese Dataset for Legal Question Answering"* (arXiv:2507.19995)
*   **BibTeX Key:** `nguyen2025vlqacomprehensivelargehighquality`
*   *Note: Outlines the regulatory density and question distribution across topics.*

| Legal Topic | Chapters | Sections | Articles | Question Density (%) |
| :--- | :---: | :---: | :---: | :---: |
| **Administrative System** | 1,381 | 1,784 | 8,566 | **14.67%** |
| **Commerce** | 651 | 937 | 4,748 | 6.80% |
| **Business & Corporate** | 487 | 656 | 3,816 | 7.30% |
| **Transportation and Logistics**| 417 | 555 | 3,017 | 4.80% |
| **Natural Resources \& Env** | 404 | 581 | 2,922 | 4.50% |
| **Sports and Healthcare** | 406 | 568 | 2,494 | 3.50% |
| **Labor and Wages** | 407 | 557 | 2,440 | 5.20% |
| **Civil Rights** | 218 | 364 | 2,218 | 5.00% |
| **Criminal Liability** | 207 | 244 | 2,197 | 4.50% |

### Table 3: ViLegalNLI Model Performance
*   **Source:** Duong et al. (2026), *"ViLegalNLI: Natural Language Inference for Vietnamese Legal Texts"* (arXiv:2605.00116)
*   **BibTeX Key:** `duong2026vilegalnlinaturallanguageinference`
*   *Note: Evaluates models on natural language inference tasks over legal premises.*

| Model Architecture | Parameter Size | Dev Accuracy (%) | Dev F1-Score (%) | Test Accuracy (%) | Test F1-Score (%) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **PhoBERT (base)** | 135M | 85.14 | 84.84 | 84.46 | 84.13 |
| **PhoBERT (large)** | 370M | 86.13 | 85.97 | 84.98 | 84.78 |
| **CafeBERT (Vietnamese)**| 110M | 87.56 | 87.44 | 87.49 | 87.36 |
| **InfoXLM (large)** | 550M | 89.24 | 89.15 | 87.98 | 87.85 |
| **Gemma-3 (few-shot)** | 8B | 89.49 | 89.49 | 88.92 | 88.86 |
| **Qwen-2.5 (few-shot)** | 72B | **90.89** | **90.78** | **90.72** | **90.64** |

### Table 4: RAG-based Machine Unlearning Performance (Concept & Sample Tasks)
*   **Source:** Wang et al. (2026), *"When Machine Unlearning Meets Retrieval-Augmented Generation (RAG): Keep Secret or Forget Knowledge?"* (IEEE Transactions on Dependable and Secure Computing)
*   **BibTeX Key:** `11207222`
*   *Note: Compares parametric unlearning with non-parametric RAG unlearning on Llama-2-7b-chat.*

| Task Type | Method / Setup | ROUGE-L (↓) | Unlearning Success Rate (USR) (↑) | TPR@1%FPR (MIA) (↓) | General Utility ARC (%) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Concept** | Gradient Ascent (GA) | 61.30 | 35.9% | 2.2% | 40.2% (Spike/Degraded) |
| **Concept** | In-context Unlearning | 75.60 | 13.8% | 2.4% | 52.4% |
| **Concept** | $\mu$-Unlearning | 80.20 | 43.4% | 2.0% | 43.1% (Degraded) |
| **Concept** | **RAG-based (Ours)** | **0.10** | **99.8%** | **1.3%** | **53.8% (Preserved)** |
| **Sample** | Gradient Ascent (GA) | 32.70 | 75.8% | 2.0% | 38.5% (Degraded) |
| **Sample** | **RAG-based (Ours)** | **0.00** | **100.0%** | **1.2%** | **53.8% (Preserved)** |

### Table 5: REGRAD (Retrievable Gradients) Performance across Domains
*   **Source:** Su et al. (2026), *"Retrievable Gradients: Continual Post-Training Without Cumulative Weight Drift"* (arXiv:2606.15734)
*   **BibTeX Key:** `su2026retrievablegradientscontinualposttraining`
*   *Note: Evaluates average accuracy performance of LLaMA-8B.*

| Method / Setup | General Domain Avg (%) | Medical Domain Avg (%) | Law Domain Avg (%) | Overall Average (%) | Weight Drift? |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Direct Baseline** | 33.42 | 62.95 | 50.15 | 50.31 | No |
| **Standard CPT** | 25.67 | 62.03 | 48.20 | 48.18 | **Yes** (Severe drift) |
| **Instruction CPT** | 36.31 | 39.51 | 54.70 | 44.02 | **Yes** |
| **RAG (ICL)** | 26.98 | 59.49 | 53.23 | 47.98 | No |
| **Fine-tuned-RAG** | 37.17 | 66.84 | 70.33 | 61.34 | **Yes** (Cumulative) |
| **REGRAD (Ours)** | 38.43 | 65.61 | 80.50 | 64.36 | **No** (Zero weight drift)|
| **REGRAD+ICL (Ours)**| **45.06** | **67.74** | **84.87** | **67.26** | **No** (Zero weight drift)|
