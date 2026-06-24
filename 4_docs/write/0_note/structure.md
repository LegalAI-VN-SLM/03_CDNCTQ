# Cấu trúc ĐỀ CƯƠNG — VN Legal Selective RAG (bản đã chỉnh)

> Cập nhật: 2026-06-23. Mô tả kiến trúc chương/mục **sau khi tái cấu trúc** skeleton `.tex`.
>
> **Đây là ĐỀ CƯƠNG, không phải luận văn đầy đủ.** Vì vậy:
> - Chương **Experiments** và **Conclusion** được **ẩn** (comment-out trong `main.tex`), để dành cho luận văn đầy đủ về sau.
> - Đề cương có **4 chương**, kết bằng chương "Expected Outcomes and Implementation Plan".
>
> Ba quyết định định hình:
> 1. **Hướng method = RAG-centric.** ReGrad chỉ còn là *related work / đối chiếu parametric*, **không** phải method ở Ch3.
> 2. **Ch2 theo phễu broad→narrow** (survey → paradigm CL → PEFT → các trụ method → datasets).
> 3. **Khối "đề xuất/quản trị" gom thành chương cuối** (Ch4): Kết quả mong đợi + Đối tượng thụ hưởng + Timeline + Kinh phí. Ch3 do đó thành **method thuần**.

---

## Định hướng tổng thể

- **Bài toán:** cập nhật kiến thức pháp lý tiếng Việt liên tục mà *không quên năng lực tổng quát*, đồng thời *"quên có chủ đích"* (luật hết hiệu lực, sealed records, right to be forgotten) — **không đụng weights của base LLM**.
- **Tiếp cận:** lấy **RAG làm trục chính**:
  - **Selective RAG Memory** — importance score + consolidation/decay cho điều luật/án lệ.
  - **RAG-based Machine Unlearning** — mô hình hoá yêu cầu "quên" như ràng buộc trên KB.
- **ReGrad (gradient-based weight editing):** giữ ở lược khảo (2.4) làm đại diện họ **parametric** để *đối chiếu*, làm bệ phóng lập luận "parametric tốn kém / dễ catastrophic forgetting ⇒ chọn hướng RAG". Không phải đóng góp của đề tài.

---

## Chương 1 — INTRODUCTION

| # | Mục | Ghi chú |
|---|-----|---------|
| 1.1 | Reason for Choosing the Topic | Bối cảnh, sự cần thiết, ý nghĩa |
| 1.2 | Research Objectives | Mục tiêu chung + cụ thể |
| 1.3 | Research Questions and Hypotheses | dời lên ngay sau objectives |
| 1.4 | Research Subjects and Scope | đối tượng + giới hạn nội dung/không gian/thời gian |
| 1.5 | Contributions of the Study | |
| 1.6 | Report Structure | |

> Lưu ý: **Expected Results** và **Beneficiaries** đã chuyển xuống chương cuối (Ch4).

## Chương 2 — BACKGROUND AND LITERATURE REVIEW (phễu broad→narrow)

| # | Mục | Vai trò trong phễu | Map → method |
|---|-----|--------------------|--------------|
| 2.1 | Surveys on Continual Learning for LLMs & Catastrophic Forgetting | mở đầu rộng, survey theo năm | — |
| 2.2 | Continual Learning Paradigms: CPT · CIT · CA | Continual Pre-Training / Instruction Tuning / Alignment | — |
| 2.3 | Parameter-Efficient Fine-Tuning (PEFT) | họ parametric | — |
| 2.4 | Retrievable Gradients & Dynamic Weight Editing (ReGrad) | parametric **đối chiếu** | — |
| 2.5 | Selective RAG & Long-term Memory | trụ chính 1 | → 3.2 |
| 2.6 | Machine Unlearning in RAG | trụ chính 2 | → 3.3 |
| 2.7 | Vietnamese Legal NLP Datasets & Benchmarks | nền dữ liệu | (luận văn đầy đủ) |

**Logic phễu:** rộng (survey CL) → các paradigm CL → giải pháp parametric (PEFT, ReGrad) + *giới hạn của chúng* → pivot sang hướng RAG (memory + unlearning) → nền dữ liệu tiếng Việt.

## Chương 3 — PROPOSED METHODS AND MODELS (RAG-centric, method thuần)

| # | Mục | Ghi chú |
|---|-----|---------|
| 3.1 | Research Plan and Overall Framework | framing RAG-centric |
| 3.2 | Selective RAG Memory Module | Forgetting mechanics + Importance score |
| 3.3 | RAG Machine Unlearning | Constrained optimization + Privacy/compliance filtering |

> Đã bỏ khỏi Ch3: `ReGrad Gradient Bank` (→ về 2.4) và `Project Plan and Budget` (→ về Ch4).

## Chương 4 — EXPECTED OUTCOMES AND IMPLEMENTATION PLAN (chương cuối của đề cương)

| # | Mục | Tương ứng template VN |
|---|-----|------------------------|
| 4.1 | Expected Results | Kết quả mong đợi (tương ứng từng mục tiêu cụ thể) |
| 4.2 | Beneficiaries | Đối tượng thụ hưởng (Who) |
| 4.3 | Project Timeline and Implementation Plan | Kế hoạch & tiến độ |
| 4.4 | Estimated Budget | Dự trù kinh phí (How much) |

---

## Ẩn — để dành cho LUẬN VĂN ĐẦY ĐỦ (comment-out trong `main.tex`)

- `2_chapters/4_Experiments.tex` — 4.1 Experimental Design & Metrics · 4.2 Baselines & Ablations
- `2_chapters/5_Conclusion.tex` — 5.1 Conclusion · 5.2 Limitations · 5.3 Future Work

> Khi chuyển sang luận văn đầy đủ: bỏ comment hai dòng này trong `main.tex`, và cân nhắc thay chương "Expected Outcomes" (đề cương) bằng chương "Results" thực tế.

---

## Cây file `.tex` (đề cương đang build)

```
main.tex  ──> \input theo thứ tự:
2_chapters/
├── 1_Introduction.tex
│   └── 1_Introduction/
│       ├── 1_1_Reason_for_choosing_the_topic.tex
│       ├── 1_2_Research_objectives.tex
│       ├── 1_3_Research_questions_and_hypotheses.tex
│       ├── 1_4_Research_subjects_and_scope.tex
│       ├── 1_5_Contributions_of_the_study.tex
│       └── 1_6_Report_structure.tex
├── 2_Background.tex
│   └── 2_Background/
│       ├── 2_1_Surveys_CL.tex
│       ├── 2_2_CL_Paradigms.tex
│       ├── 2_3_PEFT.tex
│       ├── 2_4_Related_work_ReGrad.tex        (đối chiếu parametric)
│       ├── 2_5_Related_work_RAG_memory.tex
│       ├── 2_6_Related_work_RAG_unlearning.tex
│       └── 2_7_Related_work_Vietnamese_Legal_datasets.tex
├── 3_Methods_Models.tex
│   └── 3_Methods_Models/
│       ├── 3_1_Research_Plan_and_Overall_Framework.tex
│       ├── 3_2_Selective_RAG_Memory.tex
│       └── 3_3_RAG_Machine_Unlearning.tex
├── 4_Expected_Outcomes_and_Plan.tex            (chương cuối)
│   └── 4_Expected_Outcomes_and_Plan/
│       ├── 4_1_Expected_Results.tex
│       ├── 4_2_Beneficiaries.tex
│       ├── 4_3_Project_Timeline_and_Implementation_Plan.tex
│       └── 4_4_Estimated_Budget.tex
│
├── 4_Experiments.tex   (ẨN — luận văn đầy đủ)
└── 5_Conclusion.tex    (ẨN — luận văn đầy đủ)
```

## Việc còn lại (chưa làm)

- [ ] Viết nội dung cho từng section (hiện mới là skeleton header).
- [ ] Đối chiếu lại citation key trong `references.bib` cho từng trụ method + survey/PEFT/CL paradigm.
- [ ] (Khi sang luận văn đầy đủ) bỏ comment Experiments & Conclusion trong `main.tex`.
