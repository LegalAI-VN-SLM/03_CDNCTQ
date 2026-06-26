# 3. Research Questions, Hypotheses & Thiết kế kiểm chứng (sắc, falsifiable)

> Mục đích: mỗi giả thuyết phải **kiểm chứng được** và **bác bỏ được**. Hội đồng đánh giá cao giả thuyết nêu rõ *điều kiện bác bỏ* và *biến kiểm soát*. Đây là khung "đúng kiểu nghiên cứu".
>
> **Hướng chốt (pivot):** trục lõi là **chống quên thảm khốc khi NẠP TRI THỨC LIÊN TỤC** bằng RAG phi-tham số, đo trên **3 task QA/NLI/Syllogism** (khuôn Bảng B của ReGrad). Unlearning (P+Q) **hạ xuống phụ** = temporal gating, đo rò rỉ như kiểm tra phụ — không còn là một giả thuyết tiêu đề.

---

## RQ0 (bao trùm)
Khi tri thức luật được **nạp liên tục**, cách tiếp cận nào tránh quên thảm khốc trên QA/NLI/Syllogism, và kho **chọn lọc** có giữ retrieval sắc ở chỗ kho **phẳng** bị nhiễu không?

## Khung chung cho mỗi giả thuyết
Mỗi H ghi: **IV** (biến độc lập – cái ta thay đổi) · **DV** (biến phụ thuộc – cái ta đo) · **Control** (giữ cố định) · **Hướng kỳ vọng** · **Điều kiện BÁC BỎ** (falsification) · **Confound cần khử**.

---

## RQ1 → H1: Quên thảm khốc khi nạp liên tục (parametric CPT/CIT)

**RQ1:** Khi nạp tri thức luật liên tục, parametric continual learning (CPT/CIT) tụt năng lực đến mức nào trên QA/NLI/Syllogism?

**H1:** Khi lượng tri thức nạp tăng dần (snapshot 25→50→75→100%), CPT/CIT **tụt mạnh** kèm **BWT âm mạnh** — quên thảm khốc, như Bảng B của ReGrad (CPT 10K sụp 27.80).

| Thành phần | Cụ thể |
|---|---|
| IV | **lượng tri thức nạp** (các mức snapshot) × phương pháp parametric {CPT, CIT} |
| DV | perf theo mức nạp (QA/NLI/Syll); **Backward Transfer (BWT)**; FGT năng lực chung |
| Control | cùng base LLM, cùng corpus, cùng tập đánh giá, **cùng cỡ model nhỏ** (1–4B) |
| Hướng kỳ vọng | perf **↓ rõ** khi nạp nhiều; BWT **âm mạnh** ở mức nạp cao nhất |
| **Bác bỏ nếu** | CPT/CIT **không** quên đáng kể khi nạp nhiều (perf phẳng, BWT ≈ 0) |
| Confound | model scale (cố định nhỏ), lượng data (matched), learning-rate/schedule (báo cáo) |

---

## RQ2 → H2: Frozen-RAG né quên (đối chiếu parametric cùng cỡ)

**RQ2:** Frozen-base RAG có né weight-level forgetting và **giữ/tăng** perf theo lượng nạp, so với parametric baseline **cùng cỡ** không?

**H2:** Dưới **cùng lịch nạp liên tục**, frozen-RAG cho **BWT ≈ 0** và perf **không tụt** theo lượng tri thức, **trong khi parametric baseline cùng cỡ tụt rõ rệt**.

| Thành phần | Cụ thể |
|---|---|
| IV | phương pháp đưa tri thức: {frozen-RAG} vs {CPT/CIT} vs {ReGrad} × **mức nạp** |
| DV | **BWT**, perf theo lượng nạp, legal-acc (VLQA/ViLegalNLI/LegalSLM) |
| Control | cùng base khởi điểm, corpus luật, tập đánh giá, cỡ model |
| Hướng kỳ vọng | BWT(RAG) ≈ 0 ≪ \|BWT(parametric)\|; **nuance:** legal-acc có thể kém ở syllogism do "learns less" |
| **Bác bỏ nếu** | RAG **cũng** tụt theo lượng nạp (retrieval interference khi kho phình), **hoặc** parametric **không** tụt → **không có contrast** |
| Confound | "quên ít vs học ít" — đo legal-acc song song để không nhầm (`Biderman2024_LoRALearnsLess`) |

> **Sửa lỗi tautology (then chốt):** H2 **không** phát biểu "base frozen ⇒ zero drift" (hiển nhiên, không bác bỏ được). Nó là **so sánh đo được/bác bỏ được** với parametric baseline cùng cỡ — bị bác nếu RAG cũng tụt hoặc parametric không tụt. Đây cũng là điểm trung thực: RAG **đổi quên-chung lấy chiều-sâu-suy-luận** (nối sang H4).

---

## RQ3 → H3: Selective memory cứu recall khi kho phình

**RQ3:** Khi kho tri thức phình to, selective memory có **giữ recall** ở chỗ kho **phẳng** tụt vì nhiễu index không?

**H3:** Vanilla **flat RAG tụt recall** khi kho lớn dần (nhiễu tích luỹ); **selective memory** (importance + decay + compliance hard-gate) **giữ recall** ở **cùng kích thước active index**, không vi phạm coverage luật còn hiệu lực.

| Thành phần | Cụ thể |
|---|---|
| IV | chính sách memory: {flat/static RAG} vs {selective + decay + compliance-gate} × **kích thước kho** |
| DV | Recall@K, MRR@10; **kích thước active index**; **compliance rate** |
| Control | cùng base LLM, cùng KB nguồn, cùng retriever backbone, cùng prompt |
| Hướng kỳ vọng | recall(selective) **giữ** khi kho lớn; recall(flat) **tụt**; index(selective) < index(flat); compliance không giảm |
| **Bác bỏ nếu** | selective làm **giảm** recall, **hoặc** tụt coverage luật còn hiệu lực (compliance ↓) |
| Confound | sức mạnh retriever (cố định backbone); rò rỉ test→train (decontaminate) |

Ablation: bỏ từng feature importance (citation / hiệu lực / rủi ro); quét λ (decay); bật/tắt compliance-gate.

---

## RQ4 → H4: Trần lập luận theo độ khó task

**RQ4:** Lợi ích của retrieval thay đổi thế nào theo độ khó suy luận trên trục QA → NLI → Syllogism?

**H4:** Cải thiện nhờ retrieval **phụ thuộc task**: mạnh ở **QA**, vừa ở **NLI**, **yếu ở Syllogism** — retrieval cấp *đại tiền đề* nhưng **không thay được** năng lực suy luận của base nhỏ/frozen; Δ cải thiện **giảm dần** theo trục.

| Thành phần | Cụ thể |
|---|---|
| IV | loại task {QA, NLI, Syllogism} × {base không-RAG} vs {base + RAG} |
| DV | **Δ perf = perf(RAG) − perf(base)** theo từng task |
| Control | cùng base LLM, cùng cấu hình RAG/selective, cùng prompt template |
| Hướng kỳ vọng | Δ(QA) > Δ(NLI) > Δ(Syll); Δ(Syll) **nhỏ** (chạm trần) |
| **Bác bỏ nếu** | retrieval cải thiện **syllogism ngang QA** (không có trần) — kết quả tốt ngoài kỳ vọng, giả thuyết "trần" sai |
| Confound | chất lượng retrieval ở mỗi task — đo Recall@K riêng để **tách** "trần lập luận" khỏi "retrieval kém" |

---

## Phần phụ (không phải giả thuyết tiêu đề): Unlearning P+Q = temporal gating

Unlearning được mô hình hoá thành ràng buộc **P+Q** trên KB (base đóng băng), dùng cho **temporal gating** khi luật mới thay luật cũ (supersession) trong lúc nạp liên tục. Đây là **cơ chế phụ trợ**, đo như kiểm tra phụ chứ không phải trục giả thuyết:
- **effectiveness** (USR, ROUGE-L↓ trên forget set); **harmlessness** (retain set không đổi); **robustness** (TPR@1%FPR);
- ở mức **behavioral** — đảm bảo *không truy xuất/trích dẫn*, **không** claim xoá khỏi weight; phần rò rỉ parametric **đo và báo cáo như cận trên**.

---

## Bảng tổng: RQ × H × Metric chính

| RQ | H | Nội dung | Metric quyết định |
|---|---|---|---|
| RQ1 | **H1** | CPT/CIT quên thảm khốc khi nạp liên tục | perf theo mức nạp, **BWT**, FGT |
| RQ2 | **H2** | Frozen-RAG né quên (vs parametric cùng cỡ) | **BWT ≈ 0** vs parametric; legal-acc |
| RQ3 | **H3** | Selective cứu recall khi kho phình | Recall@K, MRR, index size, compliance |
| RQ4 | **H4** | Trần lập luận QA→NLI→Syllogism | **Δ perf** theo task; Recall@K tách biệt |
| (phụ) | — | Temporal gating / unlearning P+Q | USR/ROUGE-L↓, harmlessness, TPR@1%FPR |

## Diễn giải kết quả NULL (sự trưởng thành nghiên cứu — nên chuẩn bị)
- **H1 null** (CPT *không* quên thảm khốc ở quy mô nhỏ) → vẫn giá trị: ranh giới forgetting phụ thuộc quy mô model/lượng nạp; parametric vẫn dùng được ở low-resource.
- **H2 null** (frozen-RAG cũng tụt khi kho phình, hoặc kém legal-acc tuyệt đối do base frozen) → lộ ranh giới RAG + đánh đổi "learns less" → biện luận cho hybrid future work.
- **H3 null** (selective *không* cứu được recall) → importance/decay chưa đủ tín hiệu → hướng feature mới (embedding-cluster, query-log mining). Báo cáo trung thực.
- **H4** (nếu retrieval cứu được cả syllogism) → tốt ngoài kỳ vọng; nếu không → khẳng định **trần lập luận** của base nhỏ/frozen (đóng góp cảnh báo).

> Thông điệp bảo vệ: "Mỗi giả thuyết đều **bác bỏ được**; kể cả kết quả null cũng cho kết luận khoa học." — đây là dấu hiệu nghiên cứu nghiêm túc.
