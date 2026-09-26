# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 (sửa bản nháp) | `state`: bỏ `white`, `orange`; đèn `pedestrian` ghi `state` theo ý nghĩa ký hiệu đang sáng (người đi → `green`, bàn tay/người đứng → `red`, không đọc được → `unknown` + `needs_review=true`). Sửa mục 4, mục 5, mục 10, ví dụ E14/E15 trong `02_guideline.md`; đồng bộ `01_problem_statement.md`, `03_ontology_and_cvat_setup.md`, `03_cvat_labels.json` | Gọn danh sách màu về tín hiệu dùng chung cho xe và người đi bộ; đèn đi bộ luôn `ego_relevance=not_relevant` nên quy đổi đi/dừng không ảnh hưởng quyết định cho ego, và tránh đẩy mọi đèn đi bộ sang `unknown` | Quyết định thiết kế của nhóm trước calibration; chưa có sample_id |
| v1 (sửa bản nháp) | `state`: thêm `red_yellow` (bóng đỏ và bóng vàng cùng sáng trong một vỏ); thêm ngoại lệ vào rule "nhiều bóng cùng sáng", làm rõ E06 (một bóng, không rõ đỏ hay vàng → `unknown`), thêm ví dụ E16 và một dòng common mistake | Trước đây đỏ + vàng cùng sáng buộc phải ghi `unknown` + review dù quan sát chắc chắn; tách rõ với trường hợp phân vân một bóng | Quyết định thiết kế của nhóm trước calibration; E16 là tình huống giả định, cần đối chiếu ảnh LISA thật (clip ở Mỹ, đỏ + vàng cùng sáng có thể không xuất hiện) |
