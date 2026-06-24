# Kế hoạch & Tiến độ chi tiết — 6 tháng

> Chi tiết công việc theo tuần (26 tuần). Mốc bắt đầu giả định **T7/2026 → T12/2026** (điều chỉnh được).
> Ánh xạ về Nội dung nghiên cứu (NDNC1–NDNC7) ở `1_Noi_dung_nghien_cuu.md`.
> Quy ước milestone: 🎯 = mốc nghiệm thu; ⚠️ = rủi ro/điểm phụ thuộc.

---

## A. Gantt tổng quan (tháng × NDNC)

| NDNC \ Tháng | T1 (07) | T2 (08) | T3 (09) | T4 (10) | T5 (11) | T6 (12) |
|--------------|:---:|:---:|:---:|:---:|:---:|:---:|
| NDNC1 Lược khảo | ██ | ▓ |  |  |  |  |
| NDNC2 Dữ liệu + metadata | ██ | ██ | ▓ |  |  |  |
| NDNC3 RAG nền + baseline |  | ██ | ▓ |  |  |  |
| NDNC4 Selective RAG memory |  |  | ██ | ██ | ▓ |  |
| NDNC5 RAG unlearning |  |  |  | ▓ | ██ |  |
| NDNC6 Thực nghiệm & đánh giá |  |  |  | ▓ | ██ | ██ |
| NDNC7 Viết & công bố | ▓ | ▓ | ▓ | ▓ | ▓ | ██ |

(██ = trọng tâm, ▓ = phụ/đan xen)

---

## B. Chi tiết theo tuần

### Tháng 1 (Tuần 1–4) — Nền tảng & khởi động dữ liệu  · NDNC1, NDNC2, NDNC7
| Tuần | Công việc chi tiết | Đầu ra | Mốc |
|:---:|--------------------|--------|:---:|
| 1 | Hoàn thiện lược khảo Group 1–2 (surveys, forgetting dynamics, benchmarks); dựng khung Ch2 (2.1, 2.2). Thiết lập repo, môi trường, quản lý tài liệu/bib. | Bản thảo 2.1–2.2; repo + bib chuẩn | |
| 2 | Lược khảo Group 3–4 (CPT/scaling, PEFT/LoRA, O-LoRA) → viết 2.3; đối chiếu ReGrad → 2.4. Chốt **gap** cho từng mục tiêu. | Bản thảo 2.3–2.4; danh sách gap | |
| 3 | Lược khảo Group 5 (RAG memory, unlearning, VN datasets) → 2.5–2.7. Khảo sát nguồn văn bản luật/án lệ công khai; thiết kế schema **đơn vị tri thức** + trường metadata. | Bản thảo 2.5–2.7; schema metadata | 🎯 Ch2 đầy đủ bản thảo |
| 4 | Bắt đầu crawl/thu thập corpus luật VN; pilot tiền xử lý + chuẩn hoá điều/khoản; thử gán metadata (hiệu lực, thời gian) trên mẫu nhỏ. | Pilot corpus + pipeline tiền xử lý | ⚠️ phụ thuộc nguồn dữ liệu |

### Tháng 2 (Tuần 5–8) — Dữ liệu hoàn chỉnh + RAG nền  · NDNC2, NDNC3
| Tuần | Công việc chi tiết | Đầu ra | Mốc |
|:---:|--------------------|--------|:---:|
| 5 | Mở rộng thu thập toàn corpus; tự động hoá gán metadata; xây **citation graph** giữa đơn vị. | Corpus + citation graph v1 | |
| 6 | Làm sạch, khử trùng lặp, kiểm chất lượng metadata (sample QC); tích hợp/đối chiếu **VLQA** làm KB truy xuất. | KB pháp lý có metadata v1 | 🎯 KB sẵn sàng |
| 7 | Chốt **base LLM** + cấu hình; dựng pipeline RAG nền (index → retriever → reasoning). | Pipeline RAG nền chạy được | ⚠️ chốt base LLM |
| 8 | Hoàn thiện **static-RAG baseline**; định nghĩa giao diện truy xuất dùng chung cho NDNC4/5; smoke-test trên vài truy vấn luật. | Static-RAG baseline + interface | 🎯 Baseline mốc so sánh |

### Tháng 3 (Tuần 9–12) — Selective RAG Memory (phần 1)  · NDNC4
| Tuần | Công việc chi tiết | Đầu ra | Mốc |
|:---:|--------------------|--------|:---:|
| 9 | Thiết kế **importance score**: chọn feature (citation, hiệu lực, rủi ro, tần suất); định nghĩa công thức. | Spec importance score | |
| 10 | Hiện thực **rule-based importance**; tích hợp vào retriever; thử nghiệm sơ bộ. | Module importance v1 (rule-based) | |
| 11 | Thử **LLM-estimated importance**; so sánh với rule-based trên tập mẫu. | Báo cáo so sánh 2 phương án | |
| 12 | Thiết kế cơ chế **consolidation/decay** (cập nhật theo lịch sử truy vấn, giữ top-k). | Spec + prototype decay | 🎯 Selective memory v1 |

### Tháng 4 (Tuần 13–16) — Selective RAG (phần 2) + khởi động Unlearning  · NDNC4, NDNC5, NDNC6
| Tuần | Công việc chi tiết | Đầu ra | Mốc |
|:---:|--------------------|--------|:---:|
| 13 | Hoàn thiện consolidation/decay; thêm **ràng buộc compliance** (giữ luật còn hiệu lực dù importance thấp). | Selective RAG Memory hoàn chỉnh | 🎯 NDNC4 done |
| 14 | Định nghĩa **taxonomy forget request** (luật hết hiệu lực, sealed, dữ liệu cá nhân); thiết kế khung **P+Q**. | Spec unlearning + taxonomy | |
| 15 | Hiện thực **P** (ẩn/loại entry) + **Q** (ràng buộc retrieval/prompt) trên KB. | Module unlearning v1 | |
| 16 | Thiết kế khung thực nghiệm + bộ metric (accuracy, FGT/BWT, compliance, cost, metric unlearning); chuẩn bị script đánh giá. | Bộ metric + script eval | ⚠️ khoá thiết kế đánh giá |

### Tháng 5 (Tuần 17–20) — Hoàn thiện Unlearning + Thực nghiệm chính  · NDNC5, NDNC6
| Tuần | Công việc chi tiết | Đầu ra | Mốc |
|:---:|--------------------|--------|:---:|
| 17 | Hoàn thiện **privacy & compliance filtering** + log audit; kiểm harmlessness sơ bộ. | RAG Unlearning hoàn chỉnh | 🎯 NDNC5 done |
| 18 | Chạy thực nghiệm: baseline vs selective RAG (VLQA, ViLegalNLI, LegalSLM); thu accuracy + cost. | Kết quả run 1 | |
| 19 | Chạy: selective RAG + unlearning; đo forgetting + compliance + metric unlearning; test chuyển **regime** trước/sau sửa luật. | Kết quả run 2 | |
| 20 | **Ablation**: thành phần importance, ngưỡng decay, P-vs-Q; tổng hợp bảng kết quả. | Bảng ablation đầy đủ | 🎯 Hoàn tất thực nghiệm |

### Tháng 6 (Tuần 21–26) — Phân tích, viết & công bố  · NDNC6, NDNC7
| Tuần | Công việc chi tiết | Đầu ra | Mốc |
|:---:|--------------------|--------|:---:|
| 21 | Phân tích kết quả; kiểm định **H1/H2/H3**; xử lý kết quả bất thường; bổ sung run nếu thiếu. | Phân tích + nghiệm chứng giả thuyết | |
| 22 | Viết Ch3 (method) hoàn chỉnh dựa trên cài đặt thực tế. | Ch3 final | |
| 23 | Viết Ch4 (kết quả mong đợi/đạt được + kế hoạch); cập nhật Ch2 theo phản hồi. | Ch4 final | |
| 24 | Đóng gói **mã nguồn + bộ metric/benchmark**; viết README/tài liệu tái lập. | Gói code + benchmark công bố | 🎯 Sản phẩm phụ |
| 25 | Soạn bản thảo bài báo/hội nghị (nếu có); rà soát trích dẫn (citation check). | Bản thảo bài báo | |
| 26 | Hoàn thiện toàn văn; rà lỗi LaTeX/biber; chuẩn bị bảo vệ/nộp. | Đề cương/luận văn final | 🎯 Hoàn thành |

---

## C. Các mốc nghiệm thu chính (Milestones)
- 🎯 **Cuối T1:** Ch2 đầy đủ bản thảo + schema metadata.
- 🎯 **Giữa T2:** KB pháp lý có metadata sẵn sàng.
- 🎯 **Cuối T2:** Static-RAG baseline (mốc so sánh).
- 🎯 **Cuối T3:** Selective RAG Memory v1.
- 🎯 **Giữa T4:** NDNC4 hoàn chỉnh (selective + compliance).
- 🎯 **Đầu T5:** NDNC5 hoàn chỉnh (unlearning).
- 🎯 **Cuối T5:** Hoàn tất thực nghiệm + ablation.
- 🎯 **Cuối T6:** Đề cương/luận văn + code + benchmark hoàn thành.

## D. Rủi ro & giảm thiểu
| Rủi ro | Ảnh hưởng | Giảm thiểu |
|--------|-----------|------------|
| ⚠️ Thiếu/khó thu thập dữ liệu luật + metadata hiệu lực | trễ NDNC2 → dây chuyền | bắt đầu thu thập ngay T1; dùng VLQA làm KB tối thiểu nếu thiếu |
| ⚠️ Chốt base LLM muộn | trễ NDNC3 | quyết định trong T2; chuẩn bị 2 phương án (Qwen/LLaMA VN) |
| ⚠️ Importance/decay không cải thiện so baseline | yếu H1 | có sẵn ablation + phương án rule-based đơn giản làm fallback |
| ⚠️ Độ trễ/đo harmlessness của unlearning | yếu H3 | giới hạn taxonomy forget request; đo trên tập nhỏ có kiểm soát |
| ⚠️ Thời gian compute hạn chế | trễ NDNC6 | ưu tiên VLQA/ViLegalNLI trước, LegalSLM sau; chạy quy mô nhỏ trước |

> Các chỗ cần bạn chốt sớm (ảnh hưởng tiến độ): **base LLM (T2)**, **danh mục forget request (T4)**, **mốc regime pháp lý (T2)**, **ngưỡng nghiệm thu định lượng (T4)**.
