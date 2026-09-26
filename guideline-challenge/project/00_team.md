# Team

Điền trước phút 15. Thay mọi placeholder; còn sót thì `make status` báo ở gate G1.

- **Team:** team08
- **Nhóm peer test bài của mình:** team03 (cặp A ↔ B; số nhóm lẻ thì ring 3 nhóm A → B → C → A — Lab Coach công bố)
- **Nhóm mình test bài của:** team03
- **Problem family:** Drivable area tại ranh giới vỉa hè, lối đi bộ và lề đường
- **Nguồn ảnh:** `bdd100k` (chỉ dùng ảnh trong `data/`)

| Thành viên | GitHub | Vai trò chính | File phụ trách |
|---|---|---|---|
| Vũ Tiến Thăng | VUTIENTHANG2k4 | Spec owner | `01_problem_statement.md`, `02_guideline.md` |
| Lê Ngọc Nam | duy12345-6789 | CVAT và sample-pack owner | `03_ontology_and_cvat_setup.md`, `03_cvat_labels.json`, `sample_pack.csv`, `09_cvat_export_or_task_reference.txt` |
| Phạm Ngọc Đông | orc123 | Gold và edge-case owner | `04_edge_cases/edge_case_cards.md`, `04_edge_cases/gold_decisions.csv` |
| Ngô Đức Mạnh | LilKoon | QA và calibration owner | `05_qa_plan.md`, `06_calibration_report.csv` |
| Nguyễn Hùng Mạnh | HungManh0411 | Blind-handoff và tích hợp owner | `07_blind_handoff/`, `08_revision_log.md`, theo dõi `make status` và `make check` |

Gợi ý chia vai (nhóm 2–3 người thì gộp): **spec owner** (`01`, `02`), **CVAT owner** (`03_*`, `sample_pack.csv`,
`09`), **gold owner** (`04_edge_cases/`), **QA owner** (`05`, `06`, `07_blind_handoff/`). Mỗi file một người sửa
chính để tránh xung đột git. Calibration thì mọi người cùng label.
