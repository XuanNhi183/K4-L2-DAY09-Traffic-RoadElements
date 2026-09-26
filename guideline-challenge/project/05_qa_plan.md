# QA Plan

## 1. Quality goals và metrics
- Lỗi Critical (0% chấp nhận)
- Lỗi Major (<5% chấp nhận)
- Lỗi Minor (<10% chấp nhận)

## 2. Review rules
- **Ai review, review bao nhiêu:** QA review 100% ảnh blind và calibration
- **Chọn sample theo rule nào:** Theo tag critical và edge
- **Issue được ghi ở đâu, đóng thế nào:** Ghi trên CVAT issue, QA đóng sau khi fix
- **Khi phát hiện guideline gap thì update và version ra sao:** Update guideline lên phiên bản mới và ghi log

## 3. Defect severity matrix
| Loại | Box sai | Label sai | Relevance sai |
|---|---|---|---|
| Critical | Không vẽ đèn chính | Sai màu đỏ/xanh | Gán nhầm xe khác |
| Major | Box to nhỏ quá mức | Sai mũi tên | Gán sai làn |
| Minor | Lệch 2px | Sai đèn xa | Bỏ sót đèn mờ |
| Question | Mờ quá | Đèn hỏng | Xe khuất |

## 4. Sampling và acceptance criteria
Mẫu 100%
REWORK if: > 5% major
REJECT / ESCALATE if: > 0% critical
Trade-off: Ưu tiên an toàn không bỏ sót đèn đỏ
