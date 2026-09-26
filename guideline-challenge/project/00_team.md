# Team

Điền trước phút 15. Thay mọi placeholder; còn sót thì `make status` báo ở gate G1.

- **Team:** TODO (ví dụ `team07`)
- **Nhóm peer test bài của mình:** TODO (cặp A ↔ B; số nhóm lẻ thì ring 3 nhóm A → B → C → A — Lab Coach công bố)
- **Nhóm mình test bài của:** TODO
- **Problem family:** Phát hiện biển báo giao thông cố định, tập trung vào biển nhỏ, xa hoặc bị che (xem README mục "1 · Chọn bài toán")
- **Nguồn ảnh:** `gtsdb` cho sample pack hiện tại (`bdd100k`, `gtsdb`, `lisa` — chỉ dùng ảnh trong `data/`)

| Thành viên | GitHub | Vai trò chính | File phụ trách |
|---|---|---|---|
| Trần Văn Bắc | TODO | QA owner | `05_qa_plan.md`, `06_calibration_report.csv`, `07_blind_handoff/` |
| Lê Vĩnh Hưng | TODO | Spec owner | `01_problem_statement.md` |
| Phạm Văn Phóng | TODO | Spec owner | `02_guideline.md`, `08_revision_log.md` |
| Trần Quốc Trọng | TODO | Gold owner | `04_edge_cases/`, `gold_decisions.csv` |
| Chu Mạnh | TODO | CVAT owner | `03_cvat_labels.json`, `03_ontology_and_cvat_setup.md`, `sample_pack.csv` |

Gợi ý chia vai (nhóm 2–3 người thì gộp): **spec owner** (`01`, `02`), **CVAT owner** (`03_*`, `sample_pack.csv`,
`09`), **gold owner** (`04_edge_cases/`), **QA owner** (`05`, `06`, `07_blind_handoff/`). Mỗi file một người sửa
chính để tránh xung đột git. Calibration thì mọi người cùng label.
