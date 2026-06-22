# Continual Pre-Training of Large Language Models: How to (Re)warm Your Model?

**ArXiv:** 2308.04014  
**Authors:** Kshitij Gupta, Benjamin Thérien, Adam Ibrahim, Mats L. Richter, Quentin Anthony, Eugene Belilovsky, Irina Rish, Timothée Lesort  
**Venue:** (Workshop / Preprint 2023)  
**Source File:** `3_ 2308.04014 _Continual Pre-Training of Large Language Models How to (Re)warm Your Model.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo nghiên cứu một vấn đề thực tiễn quan trọng: thay vì huấn luyện lại LLM từ đầu mỗi khi có dữ liệu mới, làm thế nào để tiếp tục pre-training hiệu quả? Câu hỏi trọng tâm là **cần "re-warm" learning rate như thế nào** khi bắt đầu huấn luyện trên một dataset mới. Thí nghiệm dùng Pythia 410M được pre-train trên The Pile (300B tokens), sau đó continual pre-train trên SlimPajama (297B tokens). Kết quả cho thấy: (1) độ dài warm-up không quan trọng, nhưng bỏ qua hoàn toàn warm-up gây ra "stability gap" (loss spike tạm thời); (2) **MaxLR là nút điều chỉnh chính** để cân bằng giữa học dữ liệu mới và giữ tri thức cũ; (3) continual pre-training từ checkpoint đã hội tụ luôn **tốt hơn train from scratch**, kể cả khi downstream dataset lớn ngang upstream. Đây là một trong những bài đầu tiên khảo sát hệ thống warm-up trong continual pre-training quy mô lớn, mở đường cho nhiều nghiên cứu sau.

---

## 2. Section Summaries

### 2.1 Introduction
- Pre-training LLM từ đầu mỗi khi có data mới rất tốn kém; continual pre-training là giải pháp rẻ hơn.
- Thách thức chính: catastrophic forgetting khi data distribution thay đổi; các kỹ thuật CL thông thường (replay, regularization) quá tốn compute ở quy mô lớn.
- Giải pháp đề xuất: nghiên cứu ảnh hưởng của warm-up strategy khi re-tăng learning rate (re-warming) trên dataset mới.
- Giả thuyết: LR cần được re-increase để đảm bảo efficiency khi training trên dataset mới.

### 2.2 Method / Setup
- **Upstream data:** The Pile (300B tokens); **Downstream:** SlimPajama (297B tokens, 7 nguồn).
- **Model:** Pythia 410M (GPT-NeoX), AdamW optimizer (β₁=0.9, β₂=0.95, weight decay=0.1), không dùng replay.
- **LR schedule:** Linear warm-up + cosine decay xuống MinLR = 0.1 × MaxLR tại 240B tokens.
- **Biến thí nghiệm:** Warm-up lengths {0%, 0.5%, 1%, 2%}; MaxLR {1.5e-4, 3e-4, 6e-4}; constant LR (3e-5); 3 checkpoint mốc.

### 2.3 Experiments & Results
| Thí nghiệm | Kết quả chính |
|---|---|
| **Warm-up length** | 0.5–2% cho kết quả gần như giống nhau; WU=0 gây stability gap (loss spike) ban đầu nhưng hồi phục |
| **MaxLR tuning** | MaxLR 6e-4 → downstream loss thấp nhất, upstream loss cao nhất (nhiều forgetting). MaxLR 1.5e-4 → ngược lại. |
| **vs. Train from Scratch** | Tất cả fine-tuned models với warm-up **đều tốt hơn** model train từ scratch trên SlimPajama |
| **Re-warm trên cùng data** | Loss spike xảy ra ngay cả khi re-warm trên Pile (không có distribution shift) → optimization dynamics gây ra spike, không phải data shift |
| **Checkpoint selection** | Earlier checkpoint không giúp adapt nhanh hơn; fully converged checkpoint là tốt nhất |

### 2.4 Discussion / Conclusion
- MaxLR là công cụ kiểm soát trade-off upstream/downstream đơn giản và hiệu quả.
- Positive transfer giữa Pile và SlimPajama rất đáng kể (do data overlap cao).
- Hạn chế: chỉ dùng Pythia 410M (nhỏ), data overlap cao → chưa rõ liệu kết quả có generalize cho distribution shift lớn hơn.
- Cần nghiên cứu thêm về model lớn hơn, nhiều giai đoạn continual training liên tiếp, và domain shift thực sự.

---

## 3. Methodology Deep-Dive

### Framework
Continual pre-training theo paradigm **linear warm-up → cosine decay**, không có replay hay regularization. Thay đổi duy nhất: điều chỉnh MaxLR và độ dài warm-up khi chuyển sang dataset mới.

### Core Ideas
1. **Re-warming hypothesis:** LR phải được re-tăng lên (re-warmed) để optimizer thoát khỏi flat region sau khi đã hội tụ.
2. **Stability gap phenomenon:** Khởi đầu không có warm-up → loss spike ban đầu (được gọi là "chaotic phase" hay stability gap) → tự hồi phục.
3. **MaxLR as control dial:** Thay đổi MaxLR trực tiếp kiểm soát plasticity vs. stability trade-off.

### Data / Setup / Metrics
- **Data:** The Pile (upstream, 300B) → SlimPajama (downstream, 297B); không có replay.
- **Metrics chính:** Validation perplexity trên SlimPajama (downstream) và The Pile (upstream).
- **Baseline:** Train Pythia 410M from scratch trên SlimPajama.

### 3 Key Assumptions
1. **Assumption 1: Data overlap là tốt** — Thí nghiệm dùng Pile và SlimPajama, vốn có nhiều overlap (SlimPajama là dedup version của RedPajama dựa trên LLaMA dataset). Điều này có thể overestimate positive transfer.
2. **Assumption 2: Scale = Pythia 410M là đủ đại diện** — Kết quả chỉ được kiểm chứng trên 410M parameters, chưa rõ scaling behavior.
3. **Assumption 3: Single-stage continual training** — Bài chỉ nghiên cứu một lần chuyển đổi dataset (Pile → SlimPajama), chưa test multi-stage sequential continual pre-training.

---

## 4. Brutally Honest Review (NeurIPS/ICML/ICLR Template)

### Summary
Bài nghiên cứu thực nghiệm về warm-up strategies trong continual pre-training của LLMs. Cung cấp insights hữu ích về stability gap và MaxLR trade-off, nhưng scope còn hạn chế.

### Strengths
- ✅ **Câu hỏi nghiên cứu rõ ràng và thực tế:** Warm-up strategy trong continual pre-training là gap thực sự trong literature.
- ✅ **Phân tích trực quan tốt:** Figures 1–4 thể hiện rõ ràng trade-off và stability gap.
- ✅ **Baseline mạnh:** So sánh với train-from-scratch là công bằng và thuyết phục.
- ✅ **Insight "re-warm trên cùng data"** rất có giá trị — tách biệt được vai trò của optimization dynamics khỏi data distribution shift.

### Weaknesses
- ❌ **Chỉ 1 model size (410M):** Không có ablation theo scale; kết quả chưa verified ở 7B, 13B, 70B.
- ❌ **Data overlap cao:** Pile và SlimPajama overlap nhiều → positive transfer có thể bị inflate, không phản ánh real-world domain shift mạnh.
- ❌ **Chưa test multi-stage:** Bài chỉ xem xét 1 lần chuyển đổi; trong thực tế continual learning có nhiều stages.
- ❌ **Warm-up length experiment chỉ đo 50B tokens đầu:** Có thể warm-up dài hơn có lợi khi nhìn dài hạn hơn.
- ❌ **Thiếu evaluation downstream tasks:** Chỉ dùng validation perplexity, chưa đo benchmark NLP thực tế.

### Questions
1. Với distribution shift lớn hơn (ví dụ, English → code hoặc English → Vietnamese), MaxLR trade-off còn hoạt động theo cùng pattern không?
2. Kết quả có giữ với model 7B/13B không, hay là đặc thù của 410M?
3. Khi áp dụng re-warming nhiều lần liên tiếp, LR có thể bị "oscillation"? Làm sao thiết kế schedule cho multi-stage?

### Recommendation
**Accept as Workshop Paper / Borderline for Main Conference.** Đây là ablation study chắc chắn, cung cấp practical guidance. Tuy nhiên, scope nhỏ và thiếu diversity về model size/dataset cần được mở rộng trong phiên bản full paper.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **Upstream Dataset** | The Pile (~300B tokens) |
| **Downstream Dataset** | SlimPajama (~297B tokens, 7 sources) |
| **Model** | Pythia 410M |
| **Baselines** | Train from scratch on SlimPajama; Constant LR (3e-5) |
| **Primary Metric** | Validation Perplexity (downstream SlimPajama + upstream Pile) |

### 3 Chart Proposals
1. **Pareto Plot (Figure 4 replica):** Scatter plot với trục X = SlimPajama PPL (downstream) và trục Y = Pile PPL (upstream). Mỗi điểm là một model tại một checkpoint nhất định, màu = MaxLR. Visualizes trade-off rõ ràng nhất.
2. **Training Curves by MaxLR (Figures 2+3):** Line plot kép: trái = downstream loss theo tokens, phải = upstream loss theo tokens. Overlay 4 MaxLR settings + from-scratch baseline.
3. **Stability Gap Visualization (Figure 1):** Zoom-in vào 50B token đầu, so sánh WU=0 vs. WU=1% về loss spike magnitude và recovery speed.

### Table Structure
| Model Config | MaxLR | WU% | Final SlimPajama PPL | Final Pile PPL | Training Tokens |
|---|---|---|---|---|---|
| From Scratch | 3e-4 | 1% | ~14.5 | - | 240B |
| Continual PT | 1.5e-4 | 1% | ~15.2 | ~10.5 | 240B |
| Continual PT | 3e-4 | 1% | ~14.8 | ~11.2 | 240B |
| Continual PT | 6e-4 | 1% | ~14.0 | ~13.5 | 240B |
| Constant LR | 3e-5 | - | ~15.8 | ~10.2 | 240B |

*(Giá trị ước lượng từ figures, không có bảng số chính xác trong paper)*

### Raw Data Extraction
- **Table 1:** SlimPajama composition: CommoCrawl 67%/153B, C4 15%/78B, GitHub 4.5%/15B, Books 4.5%/14B, Wikipedia 4.5%/12B, ArXiv 2.5%/14B, StackExchange 2.0%/10B.
- **Key finding:** Tất cả fine-tuned models với warm-up đều outperform train-from-scratch trên SlimPajama sau đủ tokens.
- **Stability Gap:** WU=0 gây loss spike trong ~5B token đầu, sau đó bắt kịp WU>0.

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| Re-warming MaxLR là cần thiết để learn well on new data | Khi continual pre-train LLM trên corpus pháp lý tiếng Việt, cần re-warm với MaxLR phù hợp |
| MaxLR là nút điều chỉnh plasticity/stability | Nếu muốn retain tiếng Việt general, dùng MaxLR thấp; nếu muốn specialize vào pháp lý, dùng MaxLR cao hơn |
| Continual PT từ checkpoint tốt > Train from scratch | Có thể bắt đầu từ một LLM tiếng Việt sẵn có (PhoBERT, SeaLLM, Vistral) thay vì train từ đầu |
| Stability gap là phenomenon của optimizer, không phải data shift | Không cần lo lắng về spike ngắn ban đầu khi domain shift không quá lớn |
