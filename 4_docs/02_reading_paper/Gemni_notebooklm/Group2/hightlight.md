# hightlight.md: Tổng Hợp Sơ Đồ & Bảng Biểu Các Bài Báo Group 2

Tài liệu này tổng hợp toàn bộ các sơ đồ (Figures) và bảng số liệu (Tables) quan trọng từ 6 bài báo thuộc Group 2 (Continual Learning & Catastrophic Forgetting in LLMs), nhằm lưu trữ và cung cấp thông tin chi tiết về mặt trực quan và số liệu cho người đọc.

---

## 1. ConTinTin: Continual Learning from Task Instructions

### Danh sách Hình ảnh & Bảng biểu
*   **Table 1 (Desiderata of ConTinTin):** Các đặc tính mong muốn của hệ thống ConTinTin.
*   **Figure 1 (Setup in ConTinTin):** Sơ đồ quy trình hoạt động gồm hai pha: Initialization và Evolution.
*   **Figure 2 (Instruction Example):** Ví dụ minh họa một bản hướng dẫn đầy đủ từ NATURAL-INSTRUCTIONS.
*   **Table 2 (Main Results):** Bảng kết quả chính so sánh InstructionSpeak với các baseline.
*   **Table 3 (Category Results):** Điểm ROUGE-L chi tiết cho 6 loại tác vụ khác nhau.
*   **Figure 3 (Influence of k):** Biểu đồ đường thể hiện ảnh hưởng của kích thước khởi tạo $k$.
*   **Figure 4 (Transferability Curves):** Biểu đồ chuyển giao tiến/lùi phân rã theo 6 nhóm tác vụ.
*   **Algorithms 1 & 2:** Thuật toán tính toán số đo chuyển giao tiến ($g^\rightarrow_i$) và lùi ($g^\leftarrow_i$).

### Chi tiết Dữ liệu & Tóm tắt Insight
*   **Figure 1 Insight:** Chỉ ra pha Evolution hoàn toàn không tiếp cận dữ liệu nhãn, mô hình chỉ được đọc phần hướng dẫn `d_{ui}` để thực thi tác vụ kiểm tra.
*   **Figure 2 Insight:** Làm nổi bật các phần "Things to avoid" (ví dụ sai, lưu ý định dạng) tô đỏ, làm tiền đề cho chiến lược "Negative Training".
*   **Table 3 Insight (Quên lãng phân loại CF):** Bảng 3 cho thấy nhóm phân loại (CF) khi học tiến đạt điểm ~70 nhưng khi quay lại (backward) điểm số tụt xuống thảm hại còn ~7.5. Lý do là vì không gian nhãn bị chồng chéo ở các tác vụ sau.

#### Số liệu Thô (Table 2 - Main Results)
| Method | $g^\rightarrow_1$ | $g^\rightarrow_{10}$ | $g^\rightarrow_{20}$ | $g^\rightarrow_{30}$ | $g^\rightarrow_{40}$ | $g^\leftarrow_1$ | $g^\leftarrow_{10}$ | $g^\leftarrow_{20}$ | $g^\leftarrow_{30}$ | $g^\leftarrow_{40}$ |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Seq-finetune | 1.44 | 3.28 | -3.74 | 2.90 | -0.36 | 1.57 | 0.04 | -0.19 | -6.48 | -9.46 |
| LAMOL | -1.34 | 1.41 | 3.31 | -5.40 | -0.03 | 2.67 | 2.21 | 9.42 | 6.33 | 7.21 |
| InstructionSpeak | **2.16** | **5.06** | **2.29** | **4.07** | **4.39** | 1.44 | **5.21** | **7.33** | **14.99** | **12.31** |
| w/o NEGATIVE | -2.89 | 1.06 | 1.33 | 2.21 | 1.78 | 2.21 | 3.37 | 11.44 | 10.36 | 8.94 |
| w/o HISTORY | 1.88 | 3.32 | 4.41 | 3.22 | 2.97 | **4.74** | -2.78 | -0.83 | 1.35 | 3.49 |

---

## 2. Fine-tuned Language Models are Continual Learners (Continual-T0)

### Danh sách Hình ảnh & Bảng biểu
*   **Table 1 (Task Reformatting):** Định dạng tác vụ thông thường thành prompt chỉ dẫn.
*   **Table 2 (Instruction Examples):** Prompt ví dụ và nhãn của 8 tác vụ sinh mới.
*   **Figure 1 (Rehearsal Ablations):** Đồ thị tác động của tỷ lệ rehearsal ($0\%$, $0.25\%$, $1\%$).
*   **Figure 2 (Sequential Learning Curve):** Relative Gain theo thời gian trên 8 tác vụ tuần tự.
*   **Table 3 (Main Results):** So sánh CT0 (3B & 11B) với Upper Bound, LAMOL và T0 gốc.
*   **Table 4 (Zero-shot Constraints):** Tỷ lệ đáp ứng ràng buộc từ khóa ($n=1, 2, 3$).
*   **Figure 3 (Emotional Haiku):** Khả năng kết hợp zero-shot cảm xúc vào thơ Haiku.
*   **Table 5 (Model Initialization Ablation):** Thay thế T0 bằng T5 và mô hình ngẫu nhiên.
*   **Table 6 (Qualitative Generations):** So sánh văn bản sinh bởi CT0 và T0pp.

### Chi tiết Dữ liệu & Tóm tắt Insight
*   **Figure 1 Insight:** Chứng minh việc không phát lại (0%) gây quên lãng thảm họa ngay lập tức đối với khả năng zero-shot ban đầu. Tần suất phát lại chỉ 0.25% đến 1% mang lại tính ổn định tuyệt đối.
*   **Table 5 Insight:** Chỉ ra transformer khởi tạo ngẫu nhiên không thể duy trì tri thức trong học liên tục dù huấn luyện thế nào, chứng tỏ khả năng học liên tục bắt nguồn từ pha pre-training tự giám sát khổng lồ.

#### Số liệu Thô (Table 3 - Main Results)
| Model | T0tr (Acc) | T0zs (Acc) | ASSET (BLEU/SARI) | Simp (BLEU/SARI) | HGen (R1/Cons) | Haiku (Hcust) | CQA (BS) | InqQG (1Tok/BS) | EmDg (BS) | Exp (BS) | TwSt (Clf/BS) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| T0_3B | 49.8 | 48.2 | 70.1/41.0 | 12.8/41.1 | 33.6/32.2 | 34.2 | 47.6 | 2.1/58.7 | 48.6 | 32.7 | 54.4/38.0 |
| T0pp | 54.2 | 65.6 | 56.5/37.7 | 11.7/40.1 | 34.9/35.9 | 31.6 | 46.0 | 2.4/59.8 | 49.7 | 37.2 | 66.4/45.1 |
| UB_3B | 49.8 | 48.2 | 79.9/45.2 | 13.8/44.6 | 39.7/81.0 | 62.6 | 90.0 | 5.3/63.3 | 55.7 | 71.8 | 74.8/56.5 |
| CT0_3B | 47.9 | 46.6 | 78.0/44.5 | 14.6/43.7 | 37.3/77.5 | 60.4 | 86.8 | 5.2/61.9 | 55.3 | 72.4 | 74.8/56.5 |
| CT0_pp | **53.7** | **64.4** | **85.9/46.6** | **14.6/44.7** | **40.7/85.5** | **65.8** | **89.8** | **4.8/65.2** | **56.2** | **73.0** | **74.4/57.9** |

---

## 3. Empirical Study of Catastrophic Forgetting in LLMs During Continual Fine-tuning

### Danh sách Hình ảnh & Bảng biểu
*   **Table 1 (Instruction Formats):** Ví dụ gán nhãn chỉ dẫn của 5 tác vụ học liên tục.
*   **Table 2 (Evaluation Categories):** Bảng tổng hợp các bài test tri thức, lập luận, đọc hiểu, bias.
*   **Table 3 (Tuning Validation):** Điểm số trước và sau học tác vụ mới của LLMs.
*   **Figure 1 (Framework Setup):** Sơ đồ quy trình huấn luyện tuần tự và đánh giá tĩnh.
*   **Figure 3 (BLOOMZ Scale):** Đường đồ thị suy giảm năng lực của BLOOMZ-1.1B vs. 7.1B.
*   **Figure 4 (Architecture Comparison):** Đồ thị so sánh sụt giảm của BLOOMZ vs. mT0.
*   **Table 4 (Main Forgetting Rates):** Bảng điểm số tuyệt đối ($Re_o, Re_{-1}$) và độ quên lãng ($FG\%$) của BLOOMZ và mT0.
*   **Table 5 (Bias Mitigation FG):** Bảng đo lường độ sụt giảm định kiến xã hội.
*   **Table 6 (Effect of SFT Pre-training):** So sánh base model và instruction-tuned (LLaMA vs. Alpaca).
*   **Figure 5 (LLaMA vs. Alpaca curve):** Biểu đồ đường so sánh độ kháng quên của Alpaca.
*   **Figure 6 (Instruction mixing):** Biểu đồ so sánh việc trộn 10k dữ liệu Alpaca.

### Chi tiết Dữ liệu & Tóm tắt Insight
*   **Figure 4 Insight:** Cho thấy kiến trúc mã hóa-giải mã (mT0) bị quên tri thức nghiêm trọng hơn nhiều so với decoder-only (BLOOMZ) ở cùng quy mô.
*   **Table 5 Insight:** Chỉ ra hiện tượng quên lãng có lợi ích phụ làm giảm thiên vị giới tính/sắc tộc (điểm Perplexity thiên vị giảm tương ứng từ mốc 75% xuống 63%).

#### Số liệu Thô (Table 4 - Forgetting Metric FG)
| Model | DK Re_o | DK Re_-1 | DK FG | Reason Re_o | Reason Re_-1 | Reason FG | RC Re_o | RC Re_-1 | RC FG |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| mT0-1.2b | 26.82 | 22.47 | 9.18% | 45.43 | 40.22 | 7.75% | 35.06 | 29.54 | 17.45% |
| mT0-3.7b | 30.99 | 20.14 | 20.15% | 48.61 | 38.39 | 16.73% | 41.10 | 30.45 | 28.42% |
| BLOOMZ-1.1b | 27.19 | 23.84 | 9.54% | 47.37 | 41.97 | 6.73% | 36.77 | 27.28 | 18.04% |
| BLOOMZ-7.1b | 33.08 | 25.61 | 18.37% | 59.15 | 49.24 | 13.62% | 48.79 | 33.05 | 26.75% |

---

## 4. TRACE: A Comprehensive Benchmark for Continual Learning in LLMs

### Danh sách Hình ảnh & Bảng biểu
*   **Figure 1 (TRACE Sơ đồ):** Bản đồ quy trình học 8 tác vụ và 4 chỉ số radar.
*   **Figure 2 (GPT-4 evaluation):** Biểu đồ cột chồng Win/Tie/Lose trên Helpfulness và Safety.
*   **Figure 3 (Sample size ablation):** Ảnh hưởng của cỡ mẫu dữ liệu huấn luyện (500, 1000, 5000).
*   **Figure 4 (BBH curve):** Đột phá spike điểm BBH sau khi học ScienceQA.
*   **Figure 5 (RCL Workflow):** So sánh quy trình huấn luyện RCL và Naive CL.
*   **Figure 6 (OpenCompass results):** Nhóm bar so sánh hiệu năng các phương pháp (RCL, Replay, SeqFT).
*   **Table 1 (OP & BWT Main Table):** Kết quả của 5 mô hình lớn trên TRACE dưới các method.
*   **Table 2 (General Ability Delta):** MMLU, GSM, BBH, TyDiQA trước/sau SeqFT.
*   **Table 3 (RCL Mitigation Results):** Kết quả so sánh RCL với SingleFT và O-LoRA.
*   **Table 4 (Dataset Stats):** Thông số 8 tập dữ liệu thành phần.

### Chi tiết Dữ liệu & Tóm tắt Insight
*   **Figure 2 Insight:** Làm nổi bật sự phân cực rõ rệt: Khả năng tuân thủ chỉ dẫn (Helpfulness) bị quên sạch (Loss cao), nhưng tính an toàn (Safety) được giữ vững (tỷ lệ Tie rất cao).
*   **Figure 4 Insight:** Cho thấy cấu trúc giải thích lập luận từng bước (reasoning paths) trong ScienceQA giúp khôi phục năng lực lập luận logic bị tổn thương ở các tác vụ trước đó.

#### Số liệu Thô (Table 1 - OP & BWT)
| Model | ICL OP | SeqFT OP (BWT) | LoraSeqFT OP (BWT) | Replay OP (BWT) |
| :--- | :--- | :--- | :--- | :--- |
| LLaMA-2-7B-Chat | 38.9 | 48.7 (-8.3%) | 12.7 (-45.7%) | **55.5 (2.6%)** |
| LLaMA-2-13B-Chat | 41.9 | 49.9 (-7.0%) | 28.0 (-36.5%) | **56.6 (0.4%)** |
| Vicuna-7B-V1.5 | 42.2 | 49.2 (-8.4%) | 33.4 (-23.7%) | **55.3 (0.2%)** |
| Vicuna-13B-V1.5 | 46.9 | 51.7 (-5.9%) | 31.6 (-28.4%) | **56.9 (0.6%)** |
| Baichuan2-7B-Instruct | 44.6 | 43.4 (-15.4%) | 43.8 (-9.0%) | **51.7 (1.1%)** |

---

## 5. Scaling Laws for Forgetting When Fine-Tuning Large Language Models

### Danh sách Hình ảnh & Bảng biểu
*   **Figure 1 (Qualitative Examples):** Minh họa 3 hành vi (Học mới, Quên tri thức, Quên an toàn).
*   **Figure 2 (Inverse Law scatter):** Tương quan tuyến tính nghịch đảo giữa fine-tuning loss và forgetting loss.
*   **Figure 3 (Loss Trajectories):** Đường cong thực nghiệm vs. mô hình power-law dự đoán.
*   **Figure 4 (ARC evaluation discrepancy):** Đồ thị so sánh độ lệch đánh giá ground-truth vs. base-predictions.
*   **Figure 5 (Malicious Output script):** Minh họa sinh mã độc Windows 10 do quên an toàn.
*   **Table 1 (Scaling coefficients):** Bảng các hệ số khớp lũy thừa cho OpenOrca và News.
*   **Figure 6 (Appendix - IA3 evaluation):** Đồ thị tương quan bị lỗi trên cấu trúc IA3 ($R^2=0.18$).
*   **Figures 7 & 8 (Appendix):** Ví dụ chi tiết về lỗi bạo lực, mã độc, sai lệch logic.

### Chi tiết Dữ liệu & Tóm tắt Insight
*   **Figure 2 Insight:** Chỉ ra các điểm chạy thực nghiệm nằm khít trên đường tuyến tính ($R^2 > 0.94$), chứng tỏ hiệu năng học tác vụ mới là động cơ chính dẫn đến quên lãng.
*   **Figure 4 Insight:** Chứng minh việc đối chứng accuracy của mô hình đã tinh chỉnh trực tiếp với dự đoán của mô hình gốc (thay vì ground-truth label) giúp bộc lộ rõ rệt sự phân rã tri thức.

#### Số liệu Thô (ARC & AdvBench Forgetting - Rank 8 LoRA)
| Model Checkpoint (LoRA Rank 8) | ARC Accuracy vs. Ground Truth | ARC Accuracy vs. Base Model | AdvBench Refusals (out of 50) |
| :--- | :--- | :--- | :--- |
| Pre-trained LLaMA-2 7B Chat | 54.1% | 100.0% | 32 |
| News-Fine-Tuned (N=260) | 48.0% | 65.0% | 24 |
| OpenOrca-Fine-Tuned (N=260) | 52.0% | 67.2% | 16 |

---

## 6. Revisiting Catastrophic Forgetting in Large Language Model Tuning

### Danh sách Hình ảnh & Bảng biểu
*   **Figure 1 (3D Loss Landscape):** Minh họa hình học vùng tối ưu của 3 cấu hình SFT.
*   **Table 1 (LLS flatness vs Performance):** Bảng số liệu liên kết SC, AG, MAG với MMLU.
*   **Table 2 (Main Results):** Kết quả chi tiết của SAM vs. Baselines chia theo bộ dữ liệu, quy mô và kết hợp phương pháp.
*   **Table 3 (Epoch experiments):** Thử nghiệm SAM 1 epoch so với AdamW 1 và 2 epochs.
*   **Table 4 (Domain performance preserving):** Khả năng học tác vụ tinh chỉnh của SAM.
*   **Tables 5-8 (Appendix):** Bảng số liệu phân rã chi tiết cho toàn bộ các tập kiểm tra thành phần.

### Chi tiết Dữ liệu & Tóm tắt Insight
*   **Figure 1 Insight:** Chứng minh trực quan miền tối ưu của mô hình học ShareGPT/Auto-Wiki bị bóp méo dữ dội tạo thành các "hẻm núi" sắc nhọn, là nguồn gốc vật lý gây ra mất ổn định trọng số.
*   **Table 2 (c) Insight:** Chỉ ra SAM tăng thêm +3.02% điểm trung bình cho phương pháp Rehearsal truyền thống và +0.97% cho Wise-FT khi áp dụng kết hợp.

#### Số liệu Thô (Table 2a - SAM vs. AdamW on LLaMA-2-7B)
| Training Task & Setting | DK (MMLU) | Understanding | Reasoning | Exams | AVG |
| :--- | :--- | :--- | :--- | :--- | :--- |
| LLaMA-2-7B Alpaca (Base) | 40.53 | 58.74 | 63.33 | 45.08 | 51.92 |
| -> ShareGPT (AdamW w/o SAM) | 26.08 | 52.84 | 58.68 | 45.76 | 45.84 |
| -> ShareGPT (SAM w/ SAM) | **40.08** | **57.91** | **63.78** | **44.41** | **51.55** |
| -> Open-Platypus (AdamW w/o SAM) | 31.13 | 50.70 | 61.09 | 37.97 | 45.22 |
| -> Open-Platypus (SAM w/ SAM) | **41.07** | **58.28** | **64.50** | **45.08** | **52.23** |
| -> Meta-Math (AdamW w/o SAM) | 33.13 | 52.61 | 58.46 | 36.27 | 45.12 |
| -> Meta-Math (SAM w/ SAM) | **34.79** | **55.77** | **62.38** | **42.71** | **48.91** |
