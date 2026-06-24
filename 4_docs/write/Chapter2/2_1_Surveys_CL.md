# 2.1 Surveys on Continual Learning for LLMs — Planning & Reading Notes
↔ `2_chapters/2_Background/2_1_Surveys_CL.tex`

> Đây là **tài liệu chuẩn bị viết** (reading notes + chiến lược hình/bảng) cho section khảo sát. Mục tiêu: viết được một đoạn "survey of surveys" có chiều sâu, có hình + bảng, dẫn dắt tới đề tài.
> 5 survey lõi: `wu2024...` (Wu2024), `shi2024...` (Shi2024), `zheng2024lifelong...` (Zheng2024), `guo2025...` (Guo2025), `11328884` (Agent Roadmap 2026). Awesome list: `zzz47zzz2025awesome`.

---

## 0. Vai trò & mạch viết của 2.1
- **Vai trò:** mở đầu phễu Ch2 — điểm các survey theo năm, rút ra (a) khung khái niệm chung (CPT/CIT/CA), (b) 4 paradigm chống quên, (c) xu hướng *internal→external (RAG)*, (d) khoảng trống → đề tài.
- **Mạch 5 đoạn:** (1) vì sao cần CL cho LLM → (2) khung vòng đời 3 giai đoạn → (3) ranh giới CL vs RAG vs Model Editing → (4) 4 paradigm chống quên + xu hướng dịch chuyển → (5) lên agent (Perception–Memory–Action) + khoảng trống (low-resource, controllable forgetting) → chốt sang 2.2.

---

## 1. Chiến lược Figure & Table (cần "lấy" cho section này)

| ID | Loại | Nội dung | Nguồn lấy | Trạng thái |
|----|------|----------|-----------|-----------|
| **F2.1** | TikZ | Vòng đời 3 giai đoạn CPT→CIT→CA + mũi tên *cross-stage forgetting* | Wu2024 | TikZ sẵn ở §4 |
| **F2.2** | TikZ | Cây phân loại **Internal vs External knowledge** (đánh dấu vị trí đề tài: RAG + LoRA) | Zheng2024 | TikZ sẵn ở §4 |
| **F2.3** | TikZ | Bản đồ **Perception–Memory–Action**; Memory 4 loại (RAG=Semantic, LoRA=Parametric) | Agent Roadmap 2026 | TikZ sẵn ở §4 |
| **T2.1** | Bảng | **So sánh 5 survey** (trục phân loại, phạm vi, đóng góp, gap, liên quan) | tổng hợp | data sẵn ở §3 |
| **T2.2** | Bảng | **Loại thông tin × Giai đoạn** (Fact/Domain/.../Preference × CPT/CIT/CA) + cột map sang luật VN | Wu2024 (Table 2) | data sẵn ở §5 |
| **T2.3** | Bảng | **4 paradigm chống quên** × (cơ chế, đại diện, ưu, nhược, dùng ở đâu trong đề tài) | Wu/Shi/Guo | data sẵn ở §5 |

> Khuyến nghị tối thiểu cho đề cương: **F2.1 + F2.2 + T2.1** (đủ kể câu chuyện). F2.3 + T2.2 + T2.3 thêm nếu cần dày.

---

## 2. Đọc sâu từng survey (template 7 câu)

### 2.A — Wu et al. 2024, *Continual Learning for LLMs: A Survey* `wu2024continuallearninglargelanguage`
1. **Trả lời câu hỏi gì:** Làm sao hệ thống hoá CL cho LLM **theo đúng vòng đời huấn luyện thực tế** thay vì áp khung CL của mô hình nhỏ?
2. **Chia bài toán thành nhóm nào:** 3 giai đoạn **CPT → CIT → CA**; ánh xạ **7 loại thông tin** (Fact, Domain, Language, Task, Skill, Value, Preference) vào giai đoạn phù hợp; kỹ thuật = Replay / PEFT / Architecture.
3. **Method chính vs phụ:** *Chính* = stage-aligned taxonomy + khái niệm **Cross-Stage Forgetting**. *Phụ* = catalog kỹ thuật (Continual-T0, O-LoRA, LLaMA PRO…).
4. **Benchmark:** TemporalWiki, Firehose, CKL, TRACE (CPT); CITB, ConTinTin (CIT). Metrics: Average Performance, FWT, BWT, + *cross-stage* score.
5. **Kết luận lớn nhất:** CL phải phân tầng theo lifecycle; **cross-stage forgetting** (CIT/CA làm xói mòn CPT/safety) là bottleneck; **CL ≠ RAG ≠ Model Editing**.
6. **Gap còn lại:** thuần định tính (không benchmark thống nhất); CA mỏng; thiếu nền lý thuyết quên; gộp nhầm PEFT thường vào CL. **Đề xuất tương lai: *controllable forgetting* (quên có kiểm soát).**
7. **Liên quan đề tài:** (i) khung CPT→CIT→CA = xương sống pipeline luật VN; (ii) phân biệt CL/RAG/Editing = **chỗ dựa biện minh hybrid RAG+LoRA**; (iii) **"controllable forgetting" chính là đóng góp unlearning của đề tài** — survey gọi tên gap, đề tài lấp.

### 2.B — Shi et al. 2024, *CL of LLMs: A Comprehensive Survey* `shi2024continuallearninglargelanguage`
1. **Trả lời câu hỏi gì:** Tổ chức CL-LLM sao cho khớp **thực tế công nghiệp** (bất đối xứng tài nguyên supplier–consumer)?
2. **Chia nhóm:** **Dual-axis** — *Vertical* (CPT→DAP→CFT) và *Horizontal* (Temporal / Content / Language shift, không ranh giới task). CFT = CIT / CMR / CMA.
3. **Method chính vs phụ:** *Chính* = dual-axis taxonomy + chỉ số **FUAR** + giải thích **flat-loss basin**. *Phụ* = catalog kỹ thuật.
4. **Benchmark:** TimeLMs, CC-RecentNews, CKL, TRACE, CITB, FEVER, VitaminC. Metrics: OP, BWT, FWT, **FUAR** (Forgotten/(Updated+Acquired)).
5. **Kết luận lớn nhất:** LLM lớn nằm trong **vùng loss phẳng → kháng quên tự nhiên**; replay là bắt buộc thực tế; phân bổ thuật toán theo tài nguyên (consumer ưu tiên PEFT + replay nhỏ).
6. **Gap còn lại:** không benchmark thống nhất; ranh giới CPT/DAP mờ (đều next-token trên unlabeled); CA mỏng; thiếu lý thuyết scaling↔forgetting.
7. **Liên quan đề tài:** (i) **DAP** = pha tiếp tục pretrain trên corpus luật VN; (ii) **flat-basin → nên chọn base lớn hơn để kháng quên**; (iii) **Horizontal Temporal shift = luật mới thay luật cũ** → đúng bài toán regime; (iv) **FUAR** = chỉ số đo "quên luật cũ" tốt hơn OP.

### 2.C — Zheng et al. 2024, *Towards Lifelong Learning of LLMs: A Survey* `zheng2024lifelonglearninglargelanguage`
1. **Trả lời câu hỏi gì:** Hệ thống hoá lifelong learning LLM **bao gồm cả tri thức ngoài** (không chỉ cập nhật trọng số)?
2. **Chia nhóm:** **Internal Knowledge** (CPT: vertical/language/temporal + CFT: task-specific {classification, NER, RE, MT} & task-agnostic {instruction tuning, knowledge editing, alignment}) **vs External Knowledge** (Retrieval/RAG + Tool/API) — tổng **12 kịch bản**.
3. **Method chính vs phụ:** *Chính* = lưỡng phân **Internal/External** + bản đồ 12 scenario. *Phụ* = generative replay (LAMOL/LFPT5), MoE động.
4. **Benchmark:** GLUE/SuperGLUE (chuỗi 15 task), AG News/Amazon/Yelp/DBpedia/Yahoo. Metrics: AA, AIA, FWT, BWT.
5. **Kết luận lớn nhất:** **3 xu hướng dịch chuyển** — (a) task-specific→task-agnostic, (b) full FT→**PEFT**, (c) **internal→external (RAG)**. ⭐ Đây là chỗ dựa mạnh nhất cho hướng RAG-centric.
6. **Gap còn lại:** gọi RAG/Tool là "learning" là **co giãn định nghĩa CL**; knowledge editing phân tích nông; **bỏ qua privacy/GDPR** của replay buffer.
7. **Liên quan đề tài:** (i) xu hướng **internal→external = đúng hướng đề tài**; (ii) gap **privacy/GDPR** ↔ **right-to-be-forgotten / unlearning**; (iii) Continual NER/RE cho trích xuất thực thể pháp lý (mở rộng).

### 2.D — Guo et al. 2025, *CL for Generative AI: From LLMs to MLLMs and Beyond* `guo2025continuallearninggenerativeai`
1. **Trả lời câu hỏi gì:** Khung CL **chung cho mọi mô hình tạo sinh** (LLM, MLLM, VLA, Diffusion)?
2. **Chia nhóm:** 3 nhánh **brain-inspired** — Architecture / Regularization / Replay — trải trên 4 modality.
3. **Method chính vs phụ:** *Chính* = taxonomy 3 nhánh + phạm vi xuyên modality. *Phụ* = self-generative replay, GRPO (RL) cho CL.
4. **Benchmark:** AG News/Yelp/TRACE (LLM), CLiMB/CoIN (MLLM), LIBERO/ALFRED (VLA), CLoG (Diffusion). Metrics: ZT, BWT, OP.
5. **Kết luận lớn nhất:** thống nhất được CL xuyên modality; **RL (GRPO) tổng quát hoá tốt hơn SFT**; **self-generative replay** là hướng giảm phụ thuộc buffer thật.
6. **Gap còn lại:** thuần định tính; safety/alignment-collapse nông; analogy sinh học lỏng; thiếu so sánh compute/VRAM.
7. **Liên quan đề tài:** (i) self-generative replay bảo mật (không lưu query thật); (ii) nếu dùng alignment thì GRPO có chỗ dựa; (iii) hướng mở rộng MLLM cho **văn bản luật scan/ảnh**.

### 2.E — Zheng et al. 2026, *Lifelong Learning of LLM-based Agents: A Roadmap* `11328884`
1. **Trả lời câu hỏi gì:** Làm sao tích hợp lifelong learning vào **LLM agent tương tác môi trường động**?
2. **Chia nhóm:** kiến trúc **Perception – Memory – Action**; **Memory** chia 4 loại: **Working / Episodic / Semantic / Parametric**; môi trường = POMDP có điều kiện mục tiêu.
3. **Method chính vs phụ:** *Chính* = roadmap P-M-A + taxonomy bộ nhớ 4 loại. *Phụ* = catalog kỹ thuật từng module (MemGPT, Reflexion, RAG, O-LoRA, ROME…).
4. **Benchmark:** WebArena, OSWorld, Crafter, AlfWorld. Metrics: AA, BWT, FWT, Plasticity, Learning Speed.
5. **Kết luận lớn nhất:** agent phải cân bằng **stability–plasticity**; bộ nhớ nên **phân tầng** — **RAG = Semantic memory**, **LoRA = Parametric memory**.
6. **Gap còn lại:** thuần định tính; **không chốt giải pháp tối ưu stability-plasticity**; **bỏ ngỏ privacy / "right to be forgotten" của Episodic**; thiếu phân tích compute; thiếu gold benchmark.
7. **Liên quan đề tài:** ⭐ **survey neo kiến trúc của đề tài**: RAG=Semantic + LoRA=Parametric = đúng khung hybrid; gap **right-to-be-forgotten → đóng góp unlearning**; metrics BWT/FWT cho Ch4.

---

## 3. Tổng hợp xuyên survey

### T2.1 — Bảng so sánh 5 survey *(đưa vào .tex)*
| Survey | Năm | Trục phân loại chính | Phạm vi | Đóng góp lớn nhất | Gap nổi bật | Liên quan đề tài |
|--------|----:|----------------------|---------|-------------------|-------------|------------------|
| Wu et al. | 2024 | Stage-aligned CPT/CIT/CA | LLM | Cross-stage forgetting; CL≠RAG≠Editing | định tính; đề xuất *controllable forgetting* | xương sống pipeline + biện minh unlearning |
| Shi et al. | 2024 | Dual-axis Vertical/Horizontal | LLM | Flat-basin; FUAR; supplier-consumer | ranh giới CPT/DAP mờ | DAP cho luật; FUAR đo quên; temporal=regime |
| Zheng et al. | 2024 | Internal vs External (12 scenario) | LLM | Xu hướng internal→**external (RAG)** | gọi RAG là "learning" là stretch; bỏ GDPR | chỗ dựa RAG-centric; GDPR→unlearning |
| Guo et al. | 2025 | Brain-inspired Arch/Reg/Replay | LLM→MLLM→VLA→Diffusion | CL xuyên modality; RL>SFT | định tính; safety nông | self-replay; mở rộng MLLM luật |
| Zheng et al. (Agent) | 2026 | Perception–Memory–Action | LLM agents | Memory 4 loại (RAG=Semantic, LoRA=Parametric) | không chốt stability-plasticity; bỏ right-to-be-forgotten | **neo kiến trúc hybrid + unlearning** |

### Điểm đồng thuận (consensus) của cả 5 survey
1. **Khung vòng đời chung**: CPT (facts/domain/language) → CIT/CFT (task/skill) → CA (value/preference).
2. **4 paradigm chống quên**: Replay · Regularization · Architecture/PEFT · **External Memory (RAG)**.
3. **Stability–Plasticity dilemma** là trục lý thuyết trung tâm.
4. **Hai xu hướng dịch chuyển**: full FT → **PEFT**, và internal (parametric) → **external (RAG)**.
5. **Metrics chuẩn**: AA/OP, **BWT** (quên), **FWT** (chuyển giao tiến), (+ FUAR, Plasticity).

### Khoảng trống hội tụ → mở đường cho đề tài
- **(G1) Low-resource / đa ngôn ngữ thiếu** (Wu, Zheng2024, Agent đều nêu) → ⇒ case study **tiếng Việt**.
- **(G2) Controllable forgetting / right-to-be-forgotten under-explored** (Wu future, Agent gap, Zheng2024 GDPR) → ⇒ **đóng góp unlearning**.
- **(G3) External memory (RAG) đang lên nhưng chưa khai thác cho *selective legal memory*** → ⇒ **đóng góp selective RAG**.
- **(G4) Thiếu benchmark định lượng cho legal/agent** → ⇒ **bộ metric/benchmark legal-CL VN**.

---

## 4. Figures (TikZ — dán vào .tex)

> Preamble chung: `\usepackage{tikz}` · `\usetikzlibrary{arrows.meta, positioning, fit, backgrounds}`

### F2.1 — Vòng đời CPT→CIT→CA + Cross-Stage Forgetting
```latex
\begin{tikzpicture}[
  font=\small, node distance=1.6cm,
  stage/.style={rectangle, rounded corners, draw, align=center,
                minimum height=1.1cm, minimum width=3.2cm},
  fwd/.style={-{Stealth[length=2.5mm]}, thick},
  cf/.style ={-{Stealth[length=2.5mm]}, thick, dashed, draw=red!70, bend left=30}
]
\node[stage] (cpt) {\textbf{CPT}\\ Fact / Domain / Language};
\node[stage, right=of cpt] (cit) {\textbf{CIT}\\ Task / Skill / Tool};
\node[stage, right=of cit] (ca)  {\textbf{CA}\\ Value / Preference};
\draw[fwd] (cpt) -- (cit);
\draw[fwd] (cit) -- (ca);
% cross-stage forgetting (downstream corrupts upstream)
\draw[cf] (ca.south) to node[below,font=\scriptsize,red!70]{cross-stage forgetting} (cpt.south);
\draw[cf] (cit.north) to node[above,font=\scriptsize,red!70]{} (cpt.north);
\end{tikzpicture}
```

### F2.2 — Internal vs External knowledge (đánh dấu vị trí đề tài)
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.5cm and 1.0cm,
  root/.style={rectangle, rounded corners, draw, align=center, minimum height=0.8cm},
  box/.style ={rectangle, rounded corners, draw, align=center, minimum height=0.7cm, text width=2.6cm},
  ours/.style={rectangle, rounded corners, draw, very thick, fill=green!10, align=center, text width=2.6cm},
  arr/.style ={-{Stealth[length=2mm]}}
]
\node[root] (r) {Continual Learning of LLMs};
\node[box, below left=1.0cm and 1.2cm of r]  (int) {\textbf{Internal}\\ (parametric, gradient)};
\node[box, below right=1.0cm and 1.2cm of r] (ext) {\textbf{External}\\ (non-parametric)};
\node[box, below=of int]  (cpt) {CPT / CFT};
\node[ours, below=of cpt] (lora) {LoRA (IFT)\\ \emph{[đề tài: behavior]}};
\node[ours, below=of ext] (rag) {RAG memory\\ \emph{[đề tài: facts]}};
\node[box, below=of rag]  (tool) {Tool / API};
\draw[arr] (r)--(int); \draw[arr] (r)--(ext);
\draw[arr] (int)--(cpt); \draw[arr] (cpt)--(lora);
\draw[arr] (ext)--(rag); \draw[arr] (rag)--(tool);
\end{tikzpicture}
```

### F2.3 — Perception–Memory–Action + 4 loại Memory
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.6cm and 1.1cm,
  mod/.style={rectangle, rounded corners, draw, align=center, minimum height=0.9cm, minimum width=2.4cm},
  mem/.style={rectangle, draw, align=center, minimum height=0.7cm, text width=2.3cm},
  ours/.style={rectangle, draw, very thick, fill=green!10, align=center, text width=2.3cm},
  arr/.style={-{Stealth[length=2mm]}, thick}
]
\node[mod] (p) {Perception};
\node[mod, right=2.2cm of p] (m) {Memory};
\node[mod, right=2.2cm of m] (a) {Action};
\draw[arr] (p)--(m); \draw[arr] (m)--(a);
\draw[arr] (a) to[bend left=25] (p);   % feedback loop
\node[mem, below=1.0cm of m, xshift=-2.0cm] (w) {Working};
\node[mem, right=of w] (e) {Episodic};
\node[ours, right=of e] (s) {Semantic\\ \emph{= RAG}};
\node[ours, right=of s] (par) {Parametric\\ \emph{= LoRA}};
\draw[arr] (m)--(w); \draw[arr] (m)--(e); \draw[arr] (m)--(s); \draw[arr] (m)--(par);
\end{tikzpicture}
```

---

## 5. Tables nội dung (data sẵn — đưa vào .tex)

### T2.2 — Loại thông tin × Giai đoạn (Wu2024) + map sang luật VN
| Loại thông tin | CPT | CIT | CA | Map sang VN-Legal |
|----------------|:---:|:---:|:--:|-------------------|
| Fact | ✓ | ✗ | ✗ | điều luật/sự kiện pháp lý mới → **RAG** (đề tài) |
| Domain | ✓ | ✓ | ✗ | thuật ngữ/ngữ cảnh luật VN |
| Language | ✓ | ✗ | ✗ | tiếng Việt (Strong shift) |
| Task | ✗ | ✓ | ✗ | QA/NLI/syllogism → **LoRA** |
| Skill (Tool) | ✗ | ✓ | ✗ | tra cứu/trích dẫn điều luật |
| Value | ✗ | ✗ | ✓ | tuân thủ đạo đức tư pháp |
| Preference | ✗ | ✗ | ✓ | phong cách tư vấn |

> Điểm nhấn: **Fact đi vào RAG** (đề tài), **Task/Skill đi vào LoRA** — đúng phân vai hybrid.

### T2.3 — 4 paradigm chống quên × vị trí trong đề tài
| Paradigm | Cơ chế | Đại diện | Ưu | Nhược | Trong đề tài |
|----------|--------|----------|----|-------|--------------|
| Rehearsal/Replay | trộn lại dữ liệu cũ | Continual-T0, ER | rẻ, hiệu quả | lưu dữ liệu → privacy | replay nhỏ khi train LoRA |
| Regularization | phạt thay đổi trọng số quan trọng | EWC, LwF | không cần buffer | yếu khi shift mạnh | (không dùng chính) |
| Architecture/PEFT | cô lập update vào module | LoRA, O-LoRA, MoE-LoRA | bảo vệ base | tốn GPU đa-adapter | **LoRA cho behavior** |
| External Memory | giữ tri thức ngoài, truy xuất | RAG | cập nhật/unlearn dễ, không drift | phụ thuộc retriever | **RAG cho facts (trục chính)** |

---

## 6. Dàn ý đoạn văn để viết (writing outline)
1. **Mở:** tri thức LLM nhanh lỗi thời → CL là thách thức trung tâm; 5 survey 2024–2026 hệ thống hoá lĩnh vực. *(cite cả 5)*
2. **Khung vòng đời:** trình bày CPT/CIT/CA (Wu) + dual-axis Vertical/Horizontal (Shi) → **F2.1**. Nhấn cross-stage forgetting.
3. **Ranh giới CL vs RAG vs Editing** (Wu, Zheng2024): → biện minh hybrid; chèn câu "đề tài chọn external (RAG) cho facts + PEFT (LoRA) cho behavior".
4. **4 paradigm + xu hướng dịch chuyển** (Zheng2024, Guo2025): full→PEFT, internal→external → **T2.3** + **F2.2**.
5. **Lên agent** (Roadmap 2026): Perception–Memory–Action, RAG=Semantic, LoRA=Parametric → **F2.3**.
6. **Khoảng trống → đề tài:** G1 low-resource VN, G2 controllable forgetting/right-to-be-forgotten, G3 selective RAG, G4 benchmark → chốt, dẫn sang 2.2. Chèn **T2.1** ở đầu hoặc cuối làm "bản đồ survey".

> Lưu ý văn phong: tránh liệt kê khô — mỗi survey nên có **1–2 câu "so với survey trước, paper này thêm gì"** để tạo mạch tiến hoá (2024 phân tầng → 2024 dual-axis → 2024 thêm external → 2025 xuyên modality → 2026 lên agent).
