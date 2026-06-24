# 2.2 Catastrophic Forgetting Dynamics and Benchmarks — Planning & Reading Notes
↔ `2_chapters/2_Background/2_2_*.tex` (Group 2)

> **Restructure:** 1 group = 1 section. Đây là **Group 2** (cơ chế quên + benchmark). Taxonomy CPT/CIT/CA đã nằm ở **2.1**, nên 2.2 đi thẳng vào *vì sao quên* và *đo quên thế nào*.
> 6 paper lõi: `kalajdzievski2024scalinglawsforgettingfinetuning` (rsLoRA), `li2024revisitingcatastrophicforgettinglarge` (SAM), `luo2025empiricalstudycatastrophicforgetting`, `scialom-etal-2022-fine` (Continual-T0), `wang2023tracecomprehensivebenchmarkcontinual` (TRACE), `yin-etal-2022-contintin` (ConTinTin). Cầu nối PEFT: `repec:axf:aidtaa:v:3:y:2026:i:1:p:52-61` (O-LoRA experimental).

---

## 0. Vai trò & mạch viết
- **Vai trò:** giải thích **động lực học của catastrophic forgetting** (vì sao quên) và **giao thức đo lường** (benchmark + metrics) — làm nền để biện minh: parametric tuning luôn quên ⇒ cần hướng external/RAG.
- **Mạch 4 đoạn:** (1) quên là gì + đo bằng gì (OP/BWT/FWT/ΔR) → (2) quy luật định lượng (Inverse Linear Law) → (3) hình học loss landscape + optimizer (SAM) + replay → (4) benchmark (TRACE, ConTinTin) + kết luận "an toàn bền, suy luận dễ quên" → chốt sang 2.3.

---

## 1. Chiến lược Figure & Table

| ID | Loại | Nội dung | Nguồn | Trạng thái |
|----|------|----------|-------|-----------|
| **F2.2-a** | TikZ | Loss landscape **Sharp (AdamW) vs Flat (SAM)** | Li2024 (Group2 synth) | TikZ ở §4 |
| **F2.2-b** | TikZ | Cây "3 trục của forgetting" (Architecture/Scale · Scaling Laws · Loss Geometry) | Group2 synth mindmap | TikZ ở §4 |
| **T2.2-a** | Bảng | **Replay ratio × Zero-shot retention** (số liệu thật) | Scialom2022 (Group2 synth T2) | data §5 |
| **T2.2-b** | Bảng | **Forgetting Gradient FG% — LoRA vs O-LoRA** (số liệu thật) | Zhang2026 (Group2 synth T3) | data §5 |
| **T2.2-c** | Bảng | **Định nghĩa metrics** (OP, BWT, FWT, ΔR, FG) | tổng hợp | data §5 |
| **T2.2-d** | Bảng | So sánh 6 study + ứng dụng cho luật VN | Group2 synth T1 | data §5 |

> Tối thiểu: **F (loss landscape) + T (replay ratio) + T (FG% LoRA vs O-LoRA)** — 3 hình/bảng "đắt" nhất, có số liệu.

---

## 2. Đọc sâu từng paper (template 7 câu)

### 2.A — Kalajdzievski 2024, *Scaling Laws for Forgetting When Fine-Tuning LLMs* (rsLoRA) `kalajdzievski2024scalinglawsforgettingfinetuning`
1. **Câu hỏi:** quan hệ định lượng giữa "học tác vụ mới" và "quên tri thức cũ" khi fine-tune?
2. **Chia nhóm:** phân tích theo learning rate, LoRA rank, mức độ fine-tuning; đo logit-based.
3. **Method chính/phụ:** *chính* = thiết lập **Inverse Linear Law** (L_f ∝ L_ft) + logit-based evaluation. *Phụ* = thực nghiệm rank/LR.
4. **Benchmark:** OpenOrca, WikiText-103, ARC, AdvBench. Metric: Forgetting Loss (logit), Accuracy.
5. **Kết luận lớn nhất:** học mới càng sâu ⇒ quên càng nhiều (tuyến tính); **giảm rank LoRA / dừng sớm KHÔNG chống quên**; LoRA tune làm sụt an toàn (AdvBench).
6. **Gap:** ít task; chỉ chẩn đoán, không đề xuất giải pháp.
7. **Liên quan đề tài:** ⭐ **luận cứ gốc cho frozen base** — tuning parametric luôn quên cân xứng ⇒ đóng băng base + đẩy facts ra RAG. Câu mở của 2.2.

### 2.B — Li et al. 2024, *Revisiting Catastrophic Forgetting in LLM Tuning* (SAM) `li2024revisitingcatastrophicforgettinglarge`
1. **Câu hỏi:** nguyên nhân *hình học* của CF là gì, sửa ở tầng optimizer được không?
2. **Chia nhóm:** loss landscape (sharp vs flat); optimizer; kết hợp replay/Wise-FT.
3. **Method chính/phụ:** *chính* = **Sharpness-Aware Minimization (SAM)** tìm flat minima. *Phụ* = tính trực giao với replay.
4. **Benchmark:** AdvBench, Alpaca, ARC, GSM8K. Metric: Accuracy, logit-based.
5. **Kết luận lớn nhất:** OOD tuning tạo **sharp minima** → quên; **SAM → flat minima** kháng quên; SAM **trực giao** với replay (+3.02%), 1 epoch SAM > 2 epoch AdamW.
6. **Gap:** SAM tốn chi phí/bước; quy mô vừa.
7. **Liên quan đề tài:** nếu tune LoRA, **SAM + replay nhỏ** giữ flat-basin; cung cấp **F loss landscape**.

### 2.C — Luo et al. 2025, *An Empirical Study of Catastrophic Forgetting in LLMs* `luo2025empiricalstudycatastrophicforgetting`
1. **Câu hỏi:** CF biểu hiện thế nào theo kiến trúc, quy mô, lịch sử huấn luyện?
2. **Chia nhóm:** decoder-only vs encoder-decoder; 1B–7B; có/không SFT trước.
3. **Method chính/phụ:** *chính* = nghiên cứu thực nghiệm CF. *Phụ* = đo bias (CrowSPairs).
4. **Benchmark:** Simp/Emdg/InqQG/Exp/HGen + MMLU, Hellaswag, BoolQ, RACE, CrowSPairs. Metric: FG%.
5. **Kết luận lớn nhất:** **decoder-only kháng quên hơn**; model lớn quên *tương đối* nhanh nhưng tuyệt đối cao hơn; **SFT trước (Alpaca) là "đệm" chống quên**; quên giảm bias.
6. **Gap:** mô tả là chính, không thuật toán mới.
7. **Liên quan đề tài:** ⇒ **base = decoder-only instruct (Qwen/LLaMA)** đã SFT — biện minh chọn mô hình.

### 2.D — Scialom et al. 2022, *Fine-tuned LMs are Continual Learners* (Continual-T0) `scialom-etal-2022-fine`
1. **Câu hỏi:** cần bao nhiêu replay để giữ năng lực cũ khi học chuỗi task?
2. **Chia nhóm:** quét tỷ lệ replay (0–5%); pre-trained vs random-init.
3. **Method chính/phụ:** *chính* = **ultra-low replay (0.25–1%) đủ giữ zero-shot**. *Phụ* = vai trò pre-training.
4. **Benchmark:** T0, SuperGLUE. Metric: average forgetting, BWT, zero-shot acc.
5. **Kết luận lớn nhất:** **0.25–1% replay** gần như giữ trọn zero-shot; LLM pre-trained >> random-init.
6. **Gap:** task NLP cổ điển; chưa xét privacy buffer.
7. **Liên quan đề tài:** ⇒ tune LoRA legal **trộn ~0.5% corpus VN chung** — rẻ. Cho **T replay ratio**.

### 2.E — Wang et al. 2023, *TRACE: Benchmark for Continual Learning in LLMs* `wang2023tracecomprehensivebenchmarkcontinual`
1. **Câu hỏi:** đo CF toàn diện (gồm suy giảm năng lực nền + an toàn)?
2. **Chia nhóm:** 8 task; ΔR theo General/Instruction/Safety.
3. **Method chính/phụ:** *chính* = **benchmark TRACE** + ΔR. *Phụ* = RCL (reasoning-augmented).
4. **Benchmark:** TRACE (ScienceQA, FOMC, MeetingBank, C-STANCE, 20Minuten, Py150, NumGLUE-cm/ds). Metric: OP, BWT, ΔR^{G/I/S}.
5. **Kết luận lớn nhất:** **an toàn rất bền, lập luận logic dễ quên nhất**; cần ΔR đo tri thức nền.
6. **Gap:** benchmark tĩnh → rủi ro contamination.
7. **Liên quan đề tài:** ⇒ khung **ΔR + cross-stage eval** kiểm "tune legal có hỏng suy luận chung"; cảnh báo decontamination cho VN.

### 2.F — Yin et al. 2022, *ConTinTin: Continual Learning from Task Instructions* `yin-etal-2022-contintin`
1. **Câu hỏi:** học liên tục từ *instruction* của task mới, quên do đâu?
2. **Chia nhóm:** chuỗi task instruction; xung đột không gian nhãn.
3. **Method chính/phụ:** *chính* = **benchmark ConTinTin**. *Phụ* = phân tích label-overlap.
4. **Benchmark:** ConTinTin, SuperGLUE. Metric: average forgetting, FWT.
5. **Kết luận lớn nhất:** **task nhãn trùng lặp gây quên mạnh nhất** (xung đột không gian nhãn).
6. **Gap:** mô hình cũ; ít liên hệ LLM lớn.
7. **Liên quan đề tài:** ⇒ **prompt template phân biệt rõ** giữa task pháp lý (QA/NLI/syllogism).

> **Cầu nối sang 2.4 (PEFT):** Zhang 2026 `repec:axf:aidtaa:v:3:y:2026:i:1:p:52-61` đo FG% LoRA vs O-LoRA → đặt cuối 2.2 (bảng T2.2-b) làm bản lề.

---

## 3. Tổng hợp xuyên paper

### Điểm đồng thuận
1. **CF là quy luật**: Inverse Linear Law (học mới ↔ quên cũ).
2. **Thủ thuật rẻ vô dụng**: giảm rank / dừng sớm không cứu.
3. **3 đòn bẩy thật**: (a) optimizer flat-minima (SAM), (b) replay nhỏ (0.25–1%), (c) kiến trúc (decoder-only + SFT trước).
4. **Phân tầng độ bền**: safety > general > **reasoning (dễ quên nhất)**.
5. **Đo bằng**: OP, BWT, FWT, ΔR, FG% (logit-based nhạy hơn).

### Khoảng trống → đề tài
- (G-a) CF nội tại của parametric tuning **không tránh khỏi** ⇒ củng cố **frozen base + RAG**.
- (G-b) Benchmark tĩnh dễ **contamination** ⇒ cần protocol đánh giá sạch cho legal VN.
- (G-c) Reasoning dễ quên ⇒ **không nhồi reasoning vào weight**; dựa context chất lượng (selective RAG) + LoRA behavior nhẹ.

---

## 4. Figures (TikZ — dán vào .tex)
> Preamble đã có `arrows.meta, positioning, calc`.

### F2.2-a — Loss landscape: Sharp (AdamW) vs Flat (SAM)
```latex
\begin{tikzpicture}[font=\small]
  \draw[->] (-0.3,0) -- (4.2,0) node[right]{$w$};
  \draw[->] (0,-0.3) -- (0,3) node[above]{loss};
  \draw[thick,blue] (0.3,2.8) .. controls (1.8,-0.4) and (2.0,-0.4) .. (3.6,2.8);
  \node at (1.95,-0.75) {\footnotesize Sharp (AdamW)};
  \node[red] at (1.95,1.0) {$\bullet$};
  \begin{scope}[xshift=6cm]
    \draw[->] (-0.3,0) -- (4.2,0) node[right]{$w$};
    \draw[->] (0,-0.3) -- (0,3) node[above]{loss};
    \draw[thick,blue] (0.2,2.8) .. controls (1.2,0.2) .. (1.7,0.2)
                      -- (2.5,0.2) .. controls (3.0,0.2) .. (4.0,2.8);
    \node at (2.1,-0.75) {\footnotesize Flat (SAM)};
    \node[red] at (2.1,0.2) {$\bullet$};
  \end{scope}
\end{tikzpicture}
```
*Caption:* một dịch chuyển nhỏ $\Delta w$ ở thung lũng nhọn (AdamW) gây bùng nổ loss task cũ; ở thung lũng phẳng (SAM) vẫn giữ loss thấp.

### F2.2-b — Ba trục của catastrophic forgetting
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.6cm and 0.9cm,
  root/.style={rectangle, rounded corners, draw, align=center},
  b/.style={rectangle, rounded corners, draw, align=center, text width=3.1cm},
  arr/.style={-{Stealth[length=2mm]}}
]
\node[root] (r) {Catastrophic Forgetting};
\node[b, below=1.0cm of r] (s) {\textbf{Scaling laws}\\ Inverse Linear Law};
\node[b, left=of s] (a) {\textbf{Architecture \& scale}\\ decoder-only; SFT buffer};
\node[b, right=of s] (g) {\textbf{Loss geometry}\\ sharp vs flat; SAM};
\draw[arr](r)--(a); \draw[arr](r)--(s); \draw[arr](r)--(g);
\end{tikzpicture}
```

---

## 5. Tables nội dung (số liệu thật — dán vào .tex)

### T2.2-a — Replay ratio × Zero-shot retention (Scialom 2022)
| Replay % | Target loss | Zero-shot Acc (%) | Compute | Nhận xét |
|:--:|:--:|:--:|:--:|---|
| 0.00 (naive) | 1.72 | 22.4 | 1.0× | quên thảm khốc hoàn toàn |
| 0.25 | 1.78 | 48.6 | 1.0025× | hồi phục một phần |
| 0.50 | 1.82 | 53.4 | 1.005× | giữ tốt suy luận chung |
| 1.00 | 1.87 | **54.8** | 1.01× | gần trần pre-trained |
| 5.00 | 1.95 | 55.1 | 1.05× | bão hoà, học task chậm |

### T2.2-b — Forgetting Gradient FG% (LoRA vs O-LoRA, Zhang 2026)
| Setup | Domain FG% | Reasoning FG% | Reading FG% |
|---|:--:|:--:|:--:|
| Full FT | 42.45 | 37.90 | 42.29 |
| LoRA (r=64) | 40.43 | 40.73 | 43.66 |
| LoRA (r=128) | 42.71 | 40.90 | 44.95 |
| Prefix-Tuning | 37.15 | 38.54 | 42.05 |
| **O-LoRA (r=64, λ=0.5)** | **1.11** | **0.43** | **−1.20** |
| **O-LoRA (r=128, λ=0.5)** | **0.77** | **0.25** | **−0.24** |

> O-LoRA giảm FG ~40× so LoRA thường → bản lề dẫn sang 2.4 (PEFT).

### T2.2-c — Định nghĩa metrics quên
| Metric | Ý nghĩa |
|---|---|
| OP / AA | hiệu năng/độ chính xác trung bình sau chuỗi task |
| BWT | chuyển giao lùi (âm = quên task cũ) |
| FWT | chuyển giao tiến (tri thức cũ giúp task mới) |
| ΔR^{G/I/S} | suy giảm General / Instruction / Safety (TRACE) |
| FG% | Forgetting Gradient (logit-based, nhạy) |

### T2.2-d — So sánh 6 study + ứng dụng luật VN
| Study | Phát hiện cốt lõi | Áp dụng CDNCTQ |
|---|---|---|
| rsLoRA | học↔quên tuyến tính; rank↓ vô dụng | dùng CL thật, không tweak hyperparam |
| SAM | flat-minima kháng quên; trực giao replay | SAM + LoRA + replay nhỏ |
| Empirical CF | decoder-only bền; SFT là đệm | base decoder-only instruct |
| Continual-T0 | 0.25–1% replay đủ | trộn 0.5% corpus VN chung |
| TRACE | safety bền, reasoning dễ quên | check ΔR sau update luật |
| ConTinTin | nhãn trùng gây quên | prompt template tách bạch task |

---

## 6. Dàn ý đoạn văn để viết
1. **Mở:** CF là quy luật — Inverse Linear Law (rsLoRA) → mọi parametric tuning đều quên cân xứng.
2. **Hình học:** loss landscape sharp/flat → SAM (**F2.2-a**); cộng dồn với replay.
3. **Đòn bẩy rẻ:** replay 0.25–1% (Continual-T0, **T2.2-a**); decoder-only + SFT buffer (Empirical CF).
4. **Benchmark & phân tầng độ bền:** TRACE/ConTinTin, ΔR; safety bền — reasoning dễ quên.
5. **Bản lề PEFT:** O-LoRA giảm FG ~40× (**T2.2-b**) → dẫn sang 2.3/2.4.
6. **Chốt:** CF nội tại ⇒ củng cố hướng frozen base + RAG.
