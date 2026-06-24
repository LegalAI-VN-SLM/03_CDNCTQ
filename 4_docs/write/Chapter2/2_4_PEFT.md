# 2.4 Parameter-Efficient Fine-Tuning in Continual Learning — Planning & Reading Notes
↔ `2_chapters/2_Background/2_4_*.tex` (Group 4)

> **Group 4.** Vai trò: họ giải pháp **parametric** (LoRA & biến thể) — vừa là cơ chế thích ứng hành vi cho đề tài (LoRA cho IFT), vừa là bệ phóng lập luận "PEFT vẫn sửa trọng số vĩnh viễn ⇒ facts nên ở RAG".
> Paper: `hu2021loralowrankadaptationlarge` (LoRA), `biderman2024loralearnsforgets` (learns less), `wang-etal-2023-orthogonal` (O-LoRA), `yang2024parametercollisionhinderingcontinual` (N-LoRA), `han-etal-2025-slim` (SLIM), `gao2024higherlayersneedlora` (MoLA), `ren2024analyzingreducingcatastrophicforgetting` (I-LoRA), `razdaibiedina2023progressivepromptscontinuallearning` (Progressive Prompts), `repec:axf:aidtaa:v:3:y:2026:i:1:p:52-61` (O-LoRA experimental).

---

## 0. Vai trò & mạch viết
- **Mạch 4 đoạn:** (1) LoRA là gì + giới hạn dung lượng (CPT vs IFT) → (2) Parameter Collision + cô lập không gian (O-LoRA, N-LoRA) → (3) mở rộng kiến trúc (SLIM/MoE, MoLA, Progressive Prompts) → (4) giới hạn chung của PEFT (vẫn sửa weight, tốn GPU đa-adapter) → pivot sang bán-tham-số/RAG.

## 1. Chiến lược Figure & Table
| ID | Loại | Nội dung | Nguồn |
|----|------|----------|-------|
| **F2.4-a** | TikZ | **Parameter collision vs Orthogonal projection** (O-LoRA) | Group4 synth |
| **T2.4-a** | Bảng | **PEFT CL architectures** (O-LoRA / Progressive Prompts / SLIM): cơ chế, ưu, nhược | Group4 synth T1 |
| **T2.4-b** | Bảng | **LoRA vs Full FT — SVD (CPT vs IFT)** (rank ~2000 vs ≤32) | Group4 synth T2 (Biderman) |
| (tham chiếu) | Bảng | FG% LoRA vs O-LoRA — đã ở **2.2 (T2.2-b)** | Zhang2026 |

---

## 2. Đọc sâu từng paper (template 7 câu)

### 2.A — Hu et al. 2021, *LoRA* `hu2021loralowrankadaptationlarge`
1. **Câu hỏi:** thích ứng LLM xuống miền/tác vụ mới mà không tune toàn bộ trọng số?
2. **Chia nhóm:** low-rank adapter trên các ma trận tuyến tính.
3. **Method chính/phụ:** *chính* = đóng băng $W_0$, học $\Delta W = BA$ (rank $r \ll \min(d,k)$). *Phụ* = phân tích rank.
4. **Benchmark:** GPT-3, RoBERTa, DeBERTa, GLUE, WikiText-103.
5. **Kết luận lớn nhất:** giảm ~10.000× tham số huấn luyện, giữ/cải thiện chất lượng tác vụ đơn.
6. **Gap:** không thiết kế cho CL tuần tự (vẫn quên khi học chuỗi task).
7. **Liên quan đề tài:** nền cho **LoRA = behavior adaptation (IFT)** trong khung hybrid.

### 2.B — Biderman et al. 2024, *LoRA Learns Less and Forgets Less* `biderman2024loralearnsforgets`
1. **Câu hỏi:** LoRA thực sự học/quên thế nào so với Full FT, ở CPT vs IFT?
2. **Chia nhóm:** so CPT (tỷ token lớn) vs IFT (nhỏ); phân tích SVD của $\Delta W$.
3. **Method chính/phụ:** *chính* = phân tích SVD + "learns less, forgets less". *Phụ* = generation diversity.
4. **Benchmark:** LLaMA-2-7B, OpenOrca, WikiText-103.
5. **Kết luận lớn nhất:** $\Delta W$ của CPT có **rank nội tại ~2000** ⇒ LoRA ($r\le256$) **không đủ cho CPT**; nhưng **IFT rank thấp (≤32) đủ** ⇒ LoRA ngang Full FT; LoRA bảo tồn diversity, là regularizer chống quên.
6. **Gap:** kết luận trên model 7B; chưa đa ngôn ngữ.
7. **Liên quan đề tài:** ⭐ **luận cứ phân vai**: dùng **Full FT cho CPT (nếu có), LoRA r=256 cho IFT legal** — facts không nên nhồi vào LoRA. Cho **T2.4-b**.

### 2.C — Wang et al. 2023, *Orthogonal Subspace Learning* (O-LoRA) `wang-etal-2023-orthogonal`
1. **Câu hỏi:** học tuần tự nhiều task trên LoRA mà không ghi đè (parameter collision)?
2. **Chia nhóm:** chiếu gradient task mới lên không gian trực giao của task cũ.
3. **Method chính/phụ:** *chính* = **Orthogonal projection** (đóng băng hướng cũ, học trên bù trực giao). *Phụ* = thực nghiệm chuỗi task.
4. **Benchmark:** TRACE, LLaMA, Alpaca. Metric: OP, BWT.
5. **Kết luận lớn nhất:** triệt tiêu can thiệp chéo → **gần zero forgetting** trên task cũ.
6. **Gap:** **nhạy siêu tham số** ($r$, $\alpha$, $\lambda$) → sụp đổ trên task logic/toán.
7. **Liên quan đề tài:** dùng khi học **nhiều phân ngành luật tuần tự** (hình sự→dân sự→doanh nghiệp). Cho **F2.4-a**.

### 2.D — Yang et al. 2024, *Parameter Collision* (N-LoRA) `yang2024parametercollisionhinderingcontinual`
1. **Câu hỏi:** trực giao có thật sự hết va chạm tham số không?
2. **Chia nhóm:** phân tích va chạm ở mức scalar; thêm sparsity.
3. **Method chính/phụ:** *chính* = **N-LoRA** ($\ell_1$ sparsity → vùng thưa, rời nhau). *Phụ* = so với O-LoRA.
4. **Benchmark:** LLaMA-2, Alpaca, custom tasks.
5. **Kết luận lớn nhất:** trực giao chưa đủ ở mức scalar; **N-LoRA giảm va chạm ~58.1× so O-LoRA** → chống quên mạnh hơn.
6. **Gap:** thêm ràng buộc sparsity → tune phức tạp.
7. **Liên quan đề tài:** lựa chọn nâng cấp nếu O-LoRA chưa đủ cô lập.

### 2.E — Han et al. 2025, *SLIM (Soft LoRA + Identity Mixture)* `han-etal-2025-slim`
1. **Câu hỏi:** scale nhiều task mà không xung đột, giữ năng lực chung?
2. **Chia nhóm:** routing mềm giữa LoRA experts + identity (cho query chung "bỏ qua" adapter).
3. **Method chính/phụ:** *chính* = **soft routing + identity mixture**. *Phụ* = MoE-LoRA.
4. **Benchmark:** TRACE, LLaMA, Vicuna. Metric: OP, BWT, average forgetting.
5. **Kết luận lớn nhất:** phân tán tri thức vào experts → giảm xung đột, compute/bước không đổi; query chung đi qua identity (không hỏng năng lực gốc).
6. **Gap:** router dễ collapse khi experts bão hoà.
7. **Liên quan đề tài:** ⇒ **route query theo phân ngành luật** (hình sự/dân sự/thương mại) tới expert riêng.

### 2.F — PEFT bổ trợ (compact)
| Paper | Đóng góp 1 câu | Liên quan |
|---|---|---|
| Gao 2024 MoLA `gao2024higherlayersneedlora` | tầng cao cần nhiều LoRA expert hơn (inverted triangle) | phân bổ expert theo tầng |
| Ren 2024 I-LoRA `ren2024analyzingreducingcatastrophicforgetting` | nội suy tuyến tính giữa minima task kề | giảm quên giữa task |
| Razdaibiedina 2023 ProgPrompts `razdaibiedina2023progressivepromptscontinuallearning` | nối soft prompt theo task, base frozen | prototype nhanh format task |

---

## 3. Tổng hợp & khoảng trống → đề tài
**Đồng thuận:** (1) LoRA = "learns less, forgets less" — hợp **IFT**, không hợp **CPT** (rank ~2000); (2) cô lập không gian (O-LoRA/N-LoRA) hoặc experts (SLIM/MoLA) chống collision; (3) tất cả vẫn **sửa weight vĩnh viễn** + tốn GPU đa-adapter + nhạy siêu tham số.
**Khoảng trống → đề tài:** PEFT giải quyết *behavior* tốt nhưng **không phải nơi chứa facts biến động** (luật đổi/unlearn) ⇒ **pivot sang RAG (2.5–2.7)**. → câu chốt section: "facts → RAG, behavior → LoRA".

---

## 4. Figure (TikZ)
### F2.4-a — Parameter collision vs Orthogonal projection
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.6cm and 1.0cm,
  b/.style={rectangle, rounded corners, draw, align=center, text width=2.6cm, minimum height=0.7cm},
  bad/.style={rectangle, rounded corners, draw, fill=red!8, align=center, text width=2.6cm},
  good/.style={rectangle, rounded corners, draw, fill=green!8, align=center, text width=2.6cm},
  arr/.style={-{Stealth[length=2mm]}}
]
% standard
\node[b] (t1) {Task 1 grad};
\node[b, right=of t1] (w) {Shared LoRA $W$};
\node[b, right=of w] (t2) {Task 2 grad};
\node[bad, below=of w] (loss) {Overwrite $\Rightarrow$ forgetting};
\draw[arr](t1)--(w); \draw[arr](t2)--(w); \draw[arr](w)--(loss);
% orthogonal
\node[b, below=1.4cm of t1] (a1) {Task 1 $\to S_1$};
\node[b, right=of a1] (proj) {Project $\perp S_1$};
\node[good, right=of proj] (s2) {$S_2 = S_1^{\perp}$ \\ zero forgetting};
\draw[arr](a1)--(proj); \draw[arr](proj)--(s2);
\end{tikzpicture}
```

## 5. Tables (số liệu thật)
### T2.4-a — PEFT CL architectures
| Architecture | Cô lập tham số | Ưu | Nhược | Áp dụng luật |
|---|---|---|---|---|
| O-LoRA | chiếu trực giao bù task cũ | zero forgetting; không tăng size | nhạy $\lambda$ | học tuần tự các ngành luật |
| Progressive Prompts | nối soft prompt, base frozen | bảo vệ 100% tham số; FWT tốt | context phình theo task | prototype format task |
| SLIM / Soft-LoRA | router mềm tới experts | ít xung đột; compute/bước cố định | router dễ collapse | route hình sự/dân sự/thương mại |

### T2.4-b — LoRA vs Full FT (SVD: CPT vs IFT) — Biderman 2024
| Thuộc tính | CPT (quy mô lớn) | IFT (quy mô nhỏ) |
|---|---|---|
| Rank nội tại $\Delta W$ | **rất cao** ($r\ge2000$ cho 90% variance) | **rất thấp** ($r\le32$ đủ >95%) |
| LoRA ($r\le256$) | không đạt Full FT | **ngang Full FT** |
| Khuyến nghị CDNCTQ | **Full FT cho CPT tiếng Việt** | **LoRA ($r=256$) cho task pháp lý** |

## 6. Dàn ý viết
1. LoRA + giới hạn dung lượng (Biderman, **T2.4-b**): CPT cần Full FT, IFT hợp LoRA.
2. Parameter collision → O-LoRA/N-LoRA (**F2.4-a**), FG% (tham chiếu 2.2).
3. Mở rộng: SLIM/MoLA/ProgPrompts (**T2.4-a**).
4. Chốt: PEFT vẫn sửa weight ⇒ facts → RAG; pivot sang 2.5.
