# Problem statement + downstream contract

## Bài toán

Xác định đầu đèn và trạng thái tín hiệu áp dụng cho hướng di chuyển dự kiến của xe tại giao lộ trong chuỗi ảnh LISA, nơi đèn tròn chuyển xanh nhưng mũi tên trái vẫn đỏ, dễ gây nhầm lẫn giữa tín hiệu cho xe đi thẳng và xe rẽ trái.

## Downstream contract

1. **Downstream task / model / user là ai?** Mô hình nhận diện đèn giao thông liên quan đến xe. Hướng di chuyển dự kiến (đi thẳng hoặc rẽ trái) được ghi trước trong mô tả task, không do annotator tự đoán.
2. **Output annotation nào thực sự cần?** Mỗi đầu đèn là một đối tượng thuộc class `traffic_light`, dùng geometry `rectangle`. Các attribute gồm `signal_type` (loại tín hiệu: `circular/left_arrow/unknown`), `state` (trạng thái: `red/yellow/green/off/unknown`), `ego_relevance` (áp dụng cho hướng đi đã cho: `relevant/not_relevant/unknown`) và `needs_review` (cần kiểm tra: `true/false`). Dùng label `image_escalate`, geometry `tag`, khi cần chuyển cả ảnh cho reviewer.
3. **Failure nào gây hậu quả lớn nhất?** Gán nhầm đèn tròn xanh là tín hiệu điều khiển xe rẽ trái khi hướng rẽ đó đang chịu điều khiển của mũi tên trái đỏ.
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?** Gán `unknown` cho thuộc tính chưa rõ và `needs_review=true` trên đối tượng; nếu không xác định được đối tượng để đặt khung, thêm tag `image_escalate`. Người phụ trách QA xem lại trên CVAT; nếu thiếu quy tắc, chuyển người phụ trách guideline bổ sung. Thiếu bằng chứng thì giữ `unknown`.

## Scope

- **Trong scope (bắt buộc label):** Các đầu đèn cho xe nhìn thấy và nhận diện được trong ảnh, kể cả đèn xa, đều dùng class `traffic_light`; đèn không áp dụng cho hướng đi đã cho vẫn được vẽ và gán `ego_relevance=not_relevant`.
- **Ngoài scope (ignore):** Đèn xe, biển báo, đèn người đi bộ; không suy đoán ý định của tài xế hoặc quyết định lái xe.
- **Geometry tolerance:** `rectangle` ôm phần vỏ đầu đèn nhìn thấy, không gồm cột và quầng sáng; dung sai đề xuất tối đa 2 pixel mỗi cạnh trên ảnh gốc. Không xác định được vỏ để đặt khung thì dùng `image_escalate`.

## Output chấm được

Đối chiếu ảnh và export CVAT với gold đã freeze theo các decision sau:

| Decision / nội dung chấm | Bằng chứng trong export CVAT |
|---|---|
| **LABEL — class, attribute** | Có box `traffic_light` cho từng đầu đèn trong scope; `signal_type`, `state`, `ego_relevance` khớp expected decision. |
| **IGNORE** | Không có annotation cho đối tượng ngoài scope, như đèn xe hoặc biển báo; kiểm tra sự vắng mặt của annotation tại đối tượng đó trên ảnh. Không dùng IGNORE chỉ vì đèn có `ego_relevance=not_relevant`. |
| **UNKNOWN** | Attribute chưa đủ bằng chứng mang giá trị `unknown`, không để trống hoặc đoán một giá trị cụ thể. |
| **ESCALATE** | Đối tượng có `needs_review=true`, hoặc ảnh có tag `image_escalate` nếu không đặt được box. |
| **Geometry** | Tọa độ box `traffic_light` tuân thủ quy tắc phần vỏ nhìn thấy và dung sai đã nêu. |

Các tên class, label, attribute và giá trị trên cần được dùng thống nhất khi viết `02_guideline.md`, `03_ontology_and_cvat_setup.md` và `03_cvat_labels.json`.

## Dữ liệu và giới hạn

Nguồn: `data/lisa/`, gồm 30 ảnh liên tiếp của một clip. Dự kiến dùng 3 ảnh minh họa, 5 ảnh hiệu chỉnh nội bộ và 5 ảnh kiểm thử với nhóm khác, không trùng nhau. Các ảnh cùng một giao lộ, thông tin làn xe còn hạn chế; kết quả kiểm thử chưa chứng minh khả năng áp dụng sang cảnh mới.
