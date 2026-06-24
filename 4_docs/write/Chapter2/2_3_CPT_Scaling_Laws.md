# 2.3 Continual Pre-Training and Scaling Laws — Planning & Reading Notes
↔ `2_chapters/2_Background/2_3_*.tex` (Group 3)

> **Restructure:** 1 group = 1 section. Đây là **Group 3** — cơ chế tối ưu hoá CPT (stability gap, learning-rate schedule, replay) và **scaling laws đặc thù miền** (D-CPT). Nối thẳng tới quyết định chiến lược dữ liệu cho luật tiếng Việt.
> Paper lõi: `gupta2023continualpretraininglargelanguage` (re-warm), `ibrahim2024simplescalablestrategiescontinually` (compute-equiv replay), `que2024dcptlawdomainspecificcontinual` (D-CPT ⭐), `li2025ticlmwebscalebenchmarktimecontinual` (TiC-LM), `zheng-etal-2024-breaking` (cross-lingual). Phụ: `yildiz2025investigatingcontinualpretraininglarge`, `du2024unlockingcontinuallearningabilities` (MIGU), `peng2024scalablelanguagemodelgeneralized` (SLM), + Guo2024 stability-gap & Bethune2025 injection *(bib key cần xác nhận)*.

---

## 0. Vai trò & mạch viết
- **Vai trò:** trả lời "làm sao nạp tri thức miền (luật) vào LLM ở pha pre-training **hiệu quả + không quên**", và "dùng **scaling law** để **tính trước** tỷ lệ trộn tối ưu thay vì mò".
- **Mạch 4 đoạn:** (1) thách thức Stability Gap + giải pháp learning-rate (re-warm / infinite LR) → (2) chiến lược replay theo mức độ shift (5% weak → 15–25% strong/đa ngôn ngữ) → (3) **D-CPT Law** dự đoán tỷ lệ trộn tối ưu (Law domain) → (4) benchmark thời gian (TiC-LM) + chốt chiến lược cho luật VN.
- **Lưu ý lập trường:** đề tài **RAG-centric (frozen base)** nên CPT chủ yếu là *nền tham chiếu/đối chiếu*; nếu có nhánh DAP cho luật thì áp dụng đúng các kết luận này. Nêu rõ để nhất quán với 2.1.

---

## 1. Chiến lược Figure & Table

| ID | Loại | Nội dung | Nguồn | Trạng thái |
|----|------|----------|-------|-----------|
| **F2.3-a** | TikZ | **Stability Gap**: đường validation loss có/không re-warming | Gupta2023 (Group3 synth) | TikZ §4 |
| **F2.3-b** | TikZ | **Re-warming scheduler** (flow: no re-warm → loss spike; re-warm → smooth) | Gupta2023 | TikZ §4 |
| **T2.3-a** | Bảng | **CPT optimization strategies** (re-warm / infinite LR / compute-equiv replay) | Group3 synth T1 | data §5 |
| **T2.3-b** | Bảng | **D-CPT Law fitting accuracy** (R² theo domain & generalization) | Que2024 (synth T2) | data §5 |
| **T2.3-c** | Bảng | **D-CPT optimal mixture ratio** (Law r_d ≈ 0.64 ⭐) | Que2024 (synth T3) | data §5 |
| **T2.3-d** | Bảng | **Replay ratio theo mức shift** (weak 5% → strong 15–25%) | Ibrahim/Zheng | data §5 |

> Tối thiểu: **F (stability gap) + T (D-CPT optimal ratio)** — D-CPT cho con số trộn cụ thể cho luật là điểm "đắt" nhất của section.

---

## 2. Đọc sâu từng paper (template 7 câu)

### 2.A — Gupta et al. 2023, *How to (Re)warm Your Model?* `gupta2023continualpretraininglargelanguage`
1. **Câu hỏi:** khi tiếp tục pre-train từ checkpoint cũ, phải xử lý learning rate thế nào để không vỡ?
2. **Chia nhóm:** so các lịch LR (warm-up khác nhau, WU=0 vs WU>0).
3. **Method chính/phụ:** *chính* = **Re-warming + Re-decaying** + chẩn đoán **Stability Gap**. *Phụ* = infinite LR schedule.
4. **Benchmark:** Pythia (410M–2.8B), Pile, SlimPajama. Metric: validation loss, downstream acc.
5. **Kết luận lớn nhất:** **bắt buộc re-warm LR**; bỏ warm-up (WU=0) gây **loss spike** ~5–10B token; Stability Gap là do **động lực optimizer (AdamW momentum)**, không phải data shift.
6. **Gap:** quy mô nhỏ; cần tune MaxLR thủ công.
7. **Liên quan đề tài:** nếu CPT/DAP corpus luật VN → **dùng linear warm-up (0.5–1% token)** tránh stability gap. Cho **F2.3-a/b**.

### 2.B — Ibrahim et al. 2024, *Simple and Scalable Strategies to Continually Pre-train LLMs* `ibrahim2024simplescalablestrategiescontinually`
1. **Câu hỏi:** chiến lược CPT đơn giản nào đạt hiệu năng cao mà tiết kiệm compute?
2. **Chia nhóm:** theo mức shift (weak/strong) × tỷ lệ replay.
3. **Method chính/phụ:** *chính* = **Compute-equivalent Replay** + lịch LR. *Phụ* = thực nghiệm 10B model.
4. **Benchmark:** LLaMA, SlimPajama, RedPajama. Metric: validation loss, downstream.
5. **Kết luận lớn nhất:** **weak shift chỉ cần 5% replay** đạt ~Union baseline với **½ compute**; strong shift cần nhiều hơn.
6. **Gap:** tập trung tiếng Anh; chưa sâu đa ngôn ngữ.
7. **Liên quan đề tài:** quy tắc replay "compute-equivalent" → ngân sách dữ liệu cho DAP luật.

### 2.C — Que et al. 2024, *D-CPT Law* ⭐ `que2024dcptlawdomainspecificcontinual`
1. **Câu hỏi:** có thể **dự đoán** validation loss theo tỷ lệ trộn dữ liệu miền `r` để chọn `r` tối ưu *trước khi* train không?
2. **Chia nhóm:** 6 domain (Code, Math, **Law**, Chemistry, Music, Medical); generalize sang model/dataset/ratio chưa thấy.
3. **Method chính/phụ:** *chính* = **D-CPT Law** (mở rộng Chinchilla, thêm `r`): $L(N,D,r)=E+\frac{A}{N^\alpha}+\frac{B\,r^\eta}{D^\beta}+\frac{C}{(r+\epsilon)^\gamma}$ + **DLC** (hệ số khó của domain). *Phụ* = 3 usage thực tế.
4. **Benchmark:** Qwen-1.5, 6 domain. Metric: validation loss (general vs domain), R².
5. **Kết luận lớn nhất:** fit cực khớp (**R²>0.97**); chỉ tốn **~1% compute** để dự đoán `r` tối ưu; **Law domain: r_d ≈ 0.64** (limited legal corpus 2B).
6. **Gap:** vẫn cần chạy thí nghiệm nhỏ để fit; tokenizer/ngôn ngữ chưa bàn sâu.
7. **Liên quan đề tài:** ⭐⭐ **công cụ tính tỷ lệ trộn luật VN** — con số **~64% luật + 36% general** là anchor định lượng cho chiến lược dữ liệu. Cho **T2.3-b, T2.3-c**.

### 2.D — Li et al. 2025, *TiC-LM: Web-Scale Benchmark for Time-Continual Pretraining* `li2025ticlmwebscalebenchmarktimecontinual`
1. **Câu hỏi:** đánh giá CPT **theo dòng thời gian** (tri thức lỗi thời) thế nào?
2. **Chia nhóm:** time-split corpora (Wiki, RedPajama theo mốc thời gian).
3. **Method chính/phụ:** *chính* = **benchmark TiC-LM** + baseline replay theo thời gian. *Phụ* = đo tốc độ lỗi thời.
4. **Benchmark:** TiC-LM, TiC-Wiki, TiC-RedPajama. Metric: time-continual perplexity, forgetting rate.
5. **Kết luận lớn nhất:** chuẩn hoá đánh giá **temporal knowledge staleness**; tri thức lỗi thời theo thời gian rõ rệt.
6. **Gap:** web-general, không pháp lý.
7. **Liên quan đề tài:** ⇒ khung **temporal evaluation** cho "regime pháp lý" (luật trước/sau sửa) — đúng bài toán validity interval của đề tài.

### 2.E — Zheng et al. 2024, *Breaking (Cross-lingual CPT at Scale)* `zheng-etal-2024-breaking`
1. **Câu hỏi:** CPT xuyên ngôn ngữ (strong shift) cần gì để không quên ngôn ngữ gốc?
2. **Chia nhóm:** theo cặp ngôn ngữ; tỷ lệ replay.
3. **Method chính/phụ:** *chính* = phân tích **strong language shift**. *Phụ* = đề xuất tỷ lệ replay.
4. **Benchmark:** LLaMA-2, corpora EN & ZH. Metric: validation loss, cross-lingual.
5. **Kết luận lớn nhất:** chuyển ngôn ngữ là **strong shift** → cần **15–25% replay** giữ cấu trúc ngôn ngữ gốc.
6. **Gap:** cặp ngôn ngữ giàu tài nguyên; tiếng Việt low-resource chưa có.
7. **Liên quan đề tài:** ⭐ **tiếng Việt = strong shift** ⇒ nếu DAP, cần replay 15–25% + mở rộng tokenizer.

### 2.F — Các nghiên cứu CPT bổ trợ (compact)
| Paper | Đóng góp 1 câu | Liên quan |
|---|---|---|
| Yildiz 2025 `yildiz2025investigatingcontinualpretraininglarge` | MaxLR điều khiển trade-off plasticity/stability; stability gap do optimizer | chọn MaxLR vừa phải cho DAP luật |
| Du 2024 (MIGU) `du2024unlockingcontinuallearningabilities` | tối ưu **magnitude gradient** làm mịn stability gap | giảm quên pha đầu CPT |
| Peng 2024 (SLM) `peng2024scalablelanguagemodelgeneralized` | **MoE thích ứng** tách luồng tri thức cũ/mới | kiến trúc tách tham số nếu mở rộng |
| Guo 2024 (stability gap) *(key cần xác nhận)* | khôi phục trạng thái optimizer giảm stability gap | kỹ thuật tối ưu CPT |
| Bethune 2025 (injection) *(key cần xác nhận)* | **scaling law cho quên** khi tiêm pretraining data | tính lượng general tối thiểu cần giữ |

---

## 3. Tổng hợp xuyên paper

### Điểm đồng thuận
1. **Stability Gap là hiện tượng optimizer** (AdamW momentum), sửa bằng **re-warming / infinite LR**.
2. **Replay theo mức shift**: weak ~5% (½ compute), **strong/đa ngôn ngữ 15–25%**.
3. **Scaling law dự đoán được**: D-CPT cho phép tính `r` tối ưu với ~1% compute (R²>0.97).
4. **Thời gian quan trọng**: tri thức lỗi thời → cần đánh giá time-continual.

### Khoảng trống → đề tài
- (G-a) Tiếng Việt là **strong shift low-resource** chưa được nghiên cứu → cần replay cao + tokenizer.
- (G-b) D-CPT mới cho domain chung; **chưa áp dụng cho luật VN** → đề tài có thể dùng D-CPT để chọn `r` (nếu có DAP) hoặc trích dẫn làm cơ sở.
- (G-c) Temporal benchmark (TiC-LM) **chưa có bản pháp lý** → đề tài đề xuất đánh giá theo regime thời gian.

---

## 4. Figures (TikZ — dán vào .tex)
> Preamble đã có `arrows.meta, positioning, calc`.

### F2.3-a — Stability Gap: validation loss có/không re-warming
```latex
\begin{tikzpicture}[font=\small]
  \draw[->] (0,0) -- (7,0) node[right]{\footnotesize training steps (tokens)};
  \draw[->] (0,0) -- (0,3.2) node[above]{\footnotesize val.\ loss};
  % no re-warm: loss spike then recover
  \draw[thick,red] (0.3,1.0) .. controls (0.8,2.9) and (1.3,2.9) .. (2.0,1.2)
                   -- (6.5,0.9);
  \node[red,font=\scriptsize,align=center] at (1.2,3.0) {Stability Gap\\(no re-warm, WU=0)};
  % re-warm: smooth
  \draw[thick,blue] (0.3,1.6) .. controls (1.5,1.1) .. (3.0,0.85) -- (6.5,0.75);
  \node[blue,font=\scriptsize] at (5.0,1.15) {Re-warming (WU=1\%)};
\end{tikzpicture}
```

### F2.3-b — Re-warming scheduler (flow)
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.7cm and 1.0cm,
  s/.style={rectangle, rounded corners, draw, align=center, text width=3.0cm, minimum height=0.8cm},
  bad/.style={rectangle, rounded corners, draw, align=center, text width=3.2cm, fill=red!8},
  good/.style={rectangle, rounded corners, draw, align=center, text width=3.2cm, fill=green!8},
  arr/.style={-{Stealth[length=2mm]}}
]
\node[s] (start) {Start CPT on new corpus};
\node[bad, above right=0.3cm and 1.6cm of start] (b) {No re-warm (LR=MinLR)\\ $\Rightarrow$ loss spike};
\node[good, below right=0.3cm and 1.6cm of start] (g) {Re-warm to MaxLR \\ $\Rightarrow$ smooth convergence};
\draw[arr] (start) -- (b);
\draw[arr] (start) -- (g);
\end{tikzpicture}
```

---

## 5. Tables nội dung (số liệu thật — dán vào .tex)

### T2.3-a — CPT optimization strategies
| Strategy | Mô tả | Ưu | Nhược | Áp dụng luật VN |
|---|---|---|---|---|
| Re-warm & re-decay | tăng LR tới MaxLR (≈1% token) rồi decay | xoá stability gap, khôi phục plasticity | cần tune MaxLR | bắt buộc khi bắt đầu CPT luật |
| Infinite LR | LR hằng, chỉ anneal cuối | không cần biết token budget | loss cuối cao nếu dừng sớm | hợp crawl mở văn bản luật |
| Compute-equiv replay | replay + giảm token mới giữ FLOPs | đạt baseline với ½ compute (weak) | cần ≤25% replay (strong) | trộn ~20% token VN chung |

### T2.3-b — D-CPT Law fitting accuracy (Que 2024)
| Domain / Generalization | R² (↑) |
|---|:--:|
| General domain | 0.9967 |
| Domain-specific | 0.9796 |
| **Law domain** | **0.9850** |
| Unseen model size | 0.9516 |
| Unseen dataset size | 0.9126 |
| Unseen mixture ratio | 0.9717 |

### T2.3-c — D-CPT optimal mixture ratio ⭐ (Que 2024, Qwen-1.5-1.8B)
| Constraint | Domain | Predicted r_d | Real r_d |
|---|:--:|:--:|:--:|
| General loss drift ≤3% | Chemistry | 0.924 | 0.924 |
| Limited corpus (5B) | Music | 0.732 | 0.732 |
| **Limited legal corpus (2B)** | **Law** | **0.640** | **0.638** |

> Anchor định lượng: với corpus luật hạn chế, tỷ lệ tối ưu ≈ **64% luật + 36% general**.

### T2.3-d — Replay ratio theo mức độ shift
| Mức shift | Ví dụ | Replay cần | Nguồn |
|---|---|:--:|---|
| Weak | cùng ngôn ngữ, đổi domain | ~5% | Ibrahim2024 |
| Strong | đổi ngôn ngữ / thuật ngữ luật | 15–25% | Zheng2024 (breaking) |

D-CPT Law: $L(N,D,r)=E+\dfrac{A}{N^\alpha}+\dfrac{B\,r^\eta}{D^\beta}+\dfrac{C}{(r+\epsilon)^\gamma}$ với `r` = tỷ lệ trộn domain.

---

## 6. Dàn ý đoạn văn để viết
1. **Mở:** CPT trên corpus thô gặp **Stability Gap** (Gupta) → re-warming/infinite LR (**F2.3-a/b**).
2. **Replay theo shift:** weak 5% (Ibrahim) → strong/đa ngôn ngữ 15–25% (Zheng) (**T2.3-d**); tiếng Việt = strong shift.
3. **Scaling law dự đoán:** D-CPT (Que) tính `r` tối ưu, R²>0.97 (**T2.3-b**); **Law domain r≈0.64** (**T2.3-c**).
4. **Thời gian:** TiC-LM đánh giá temporal staleness → nối "regime pháp lý".
5. **Chốt:** nếu DAP luật VN → re-warm + replay 15–25% + D-CPT chọn `r`; nhưng đề tài **chính** vẫn frozen base + RAG, CPT là tham chiếu.
