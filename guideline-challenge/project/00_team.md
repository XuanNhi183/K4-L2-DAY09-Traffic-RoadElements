# Team

Điền trước phút 15. Thay mọi placeholder; còn sót thì `make status` báo ở gate G1.

- **Team:** Traffic
- **Nhóm peer test bài của mình:** TODO (cặp A ↔ B; số nhóm lẻ thì ring 3 nhóm A → B → C → A — Lab Coach công bố)
- **Nhóm mình test bài của:** TODO
- **Problem family:** Traffic light (state, relevance, direction)
- **Nguồn ảnh:** `lisa` (một clip 30 frame, tình huống trọng tâm đèn tròn xanh + mũi tên trái đỏ) và `bdd100k` (ảnh
  có đèn: ban ngày, ban đêm, mưa, tuyết, chạng vạng; bộ blind chủ yếu lấy từ BDD để là cảnh chưa thấy)

| Thành viên | GitHub | Vai trò chính | File phụ trách |
|---|---|---|---|
| Võ Lê Xuân Nhi | XuanNhi183 | Trưởng nhóm; xây dựng và cập nhật guideline | 00_team.md, 01_problem_statement.md, 02_guideline.md, 08_revision_log.md |
| Võ Thị Bảo Chi | vothibaochi | Phụ trách kế hoạch QA/QC và calibration nội bộ | 05_qa_plan.md, các file/thư mục 06_calibration_* |
| Nguyễn Văn Hoàng | hoangharry22 | Phụ trách ontology, cấu hình CVAT và quy trình gán nhãn | 03_ontology_and_cvat_setup.md, 03_cvat_labels.json, 09_cvat_export_or_task_reference.txt |
| Nguyễn Phương Thảo | bobosbasket-gif | Phụ trách kiểm thử với nhóm peer, đánh giá chất lượng và tổng hợp phản hồi | Thư mục 07_blind_handoff/ |
| Trương Văn Vượng | hoangharry22 | Phụ trách chọn dữ liệu, xây dựng edge cases và quản lý gold decisions | sample_pack.csv, thư mục 04_edge_cases/, FREEZE.txt |


Gợi ý chia vai (nhóm 2–3 người thì gộp): **spec owner** (`01`, `02`), **CVAT owner** (`03_*`, `sample_pack.csv`,
`09`), **gold owner** (`04_edge_cases/`), **QA owner** (`05`, `06`, `07_blind_handoff/`). Mỗi file một người sửa chính để tránh xung đột git. Calibration thì mọi người cùng label.
