# 🗺️ Address — Bản đồ thư mục dự án (CDNCTQ)

> Tra nhanh: **cái gì ở đâu, dùng để làm gì, liên quan tới cái nào.** Gốc: `E:\DoCode\1 VN-Legal-AI\03_CDNCTQ`.

---

## 1. 📄 Bản thảo LaTeX — build ra PDF đề cương
Đây là phần **được compile**. File vào: **`main.tex`** (lệnh: `pdflatex → biber → pdflatex ×2`).

**Folders thuộc bản thảo (build set):**
```
1_frontmatter/   # bìa, declaration, abstract, mục lục, list of abbreviations...
2_chapters/      # NỘI DUNG CHÍNH: Ch1 Intro, Ch2 Background, Ch3 Methods, Ch4 Outcomes&Plan
3_backmatter/    # references, appendix, publications
config/          # format.tex (preamble: package, tikz, style)
figs/            # hình ảnh (nếu có)
build/           # output trung gian/PDF
```
**Files thuộc bản thảo:**
```
main.tex         # entry point, \input các chương
references.bib   # toàn bộ trích dẫn (bib keys)
```
> 🔗 Cấu hình bundle (cho review/đóng gói):
> `folders_to_include = [1_frontmatter, 2_chapters, 3_backmatter, config, figs, build]`
> `files_to_include = [main.tex, references.bib]`

---

## 2. 📚 `4_docs/` — tài liệu hỗ trợ (KHÔNG build vào PDF)
| Địa chỉ | Là gì | Liên quan tới |
|---------|-------|---------------|
| `4_docs/02_reading_paper/Gemni_notebooklm/` | **Đã tổng hợp các bài báo** (synthesis theo Group 1–5 + global + per-paper) — *nguồn dẫn chứng* | → nuôi `4_docs/write/` và `2_chapters/` |
| `4_docs/write/` | **Planning TRƯỚC khi viết vào report (.tex)** — ý lớn, dàn ý, fig/table, defense prep | → viết thành `2_chapters/*.tex` |
| `4_docs/03_Present/report/` | **Vật liệu sinh slide** (story + dẫn chứng + glossary + Q&A) | ← đúc kết từ `2_chapters/*.tex` |
| `4_docs/03_Present/slides/` | Slide xuất ra (.pptx…) | ← từ `report/` |
| `4_docs/01_notebooklm/` | Tài liệu/đầu vào cho NotebookLM | nguồn |
| `4_docs/pdf/` | PDF gốc các bài báo | nguồn |
| `4_docs/rules/` | Quy tắc/hướng dẫn viết | tham chiếu |
| `4_docs/note_cite_legal_data.txt` | Ghi chú về trích dẫn dữ liệu luật | tham chiếu |

### Bên trong `4_docs/write/` (planning)
| Địa chỉ | Là gì |
|---------|-------|
| `write/Chapter1..4/` | Planning từng chương (mỗi section 1 file md: ý lớn + paper nguồn + ghi chú cần viết) |
| `write/Research_Core/` | Lõi nghiên cứu: `1_Novelty_and_Gap`, `2_Theoretical_Foundation`, `3_RQ_Hypotheses_Design` |
| `write/Content_time/` | Nội dung nghiên cứu + timeline 6 tháng |
| `write/0_note/` | Ghi chú quyết định cấu trúc (`structure.md`, `scope_narrowing_plan.md`...) |
| `write/structure.md`, `tittle.md`, `Workflow.md` | Cấu trúc tổng / tên đề tài / workflow |

### Bên trong `4_docs/03_Present/report/`
`00_Story_Arc` (mạch kể) · `01_Introduction` · `02_Background` · `03_Methods` · `04_Outcomes_and_Plan` · `05_Talking_Points_and_QA` · `06_Reviewer_QA_Answers` · `07_Glossary`.

---

## 3. 🔁 Luồng quan hệ (nhớ hướng đi)
```
02_reading_paper (bài báo đã tổng hợp)
        │  rút ý + dẫn chứng
        ▼
4_docs/write (PLANNING: ý lớn, dàn ý, fig/table)
        │  viết thành prose
        ▼
2_chapters/*.tex  ──(+ 1_frontmatter, 3_backmatter, config, references.bib)──►  main.tex ─► PDF (build/)
        │  đúc kết để trình bày
        ▼
4_docs/03_Present/report (vật liệu slide) ─► 03_Present/slides (.pptx)
```

---

## 4. 🧩 Khác (không thuộc bản thảo)
| Địa chỉ | Là gì |
|---------|-------|
| `src/` | Mã nguồn (script xử lý dữ liệu/build, nếu có) |
| `assets/` | Tài nguyên chung (logo, ảnh) |
| `*.zip` (CDNCTQ_v2/v3) | Bản đóng gói cũ |
| `download_references.ps1`, `quick.notebooklm.md` | Script/ghi chú tiện ích |
| `pyproject.toml`, `requirements.txt`, `package.json`, `.venv` | Môi trường/phụ thuộc |

> ✍️ Cập nhật file này khi thêm folder mới để "địa chỉ" luôn đúng.
