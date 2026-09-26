# Problem statement + downstream contract

## Bài toán

Xác định đầu đèn và trạng thái tín hiệu áp dụng cho xe đi thẳng (hướng giả định cố định) tại giao lộ nhiều đầu đèn. Tình huống trọng tâm nằm ở clip LISA: đèn tròn chuyển xanh nhưng mũi tên trái vẫn đỏ, dễ gán nhầm tín hiệu của làn rẽ trái cho xe đi thẳng. Ảnh BDD100K kiểm tra cùng quy tắc ở cảnh khác: đèn đi bộ, ban đêm dễ nhầm đèn hậu xe, mưa/tuyết/chạng vạng, đèn nhỏ/xa và đầu đèn nhìn từ cạnh.

## Downstream contract

1. **Downstream task / model / user là ai?** Mô hình nhận diện đèn giao thông liên quan đến xe. Hướng di chuyển giả định được cố định là **đi thẳng** cho mọi ảnh và mọi task (kể cả bộ ảnh bàn giao cho peer), ghi trong guideline và ở đầu mô tả task; annotator không tự đoán hay đổi hướng.
2. **Output annotation nào thực sự cần?** Mỗi đầu đèn là một đối tượng thuộc class `traffic_light`, dùng geometry `rectangle`. Các attribute gồm `signal_type` (loại tín hiệu: `circular/left_arrow/right_arrow/straight_arrow/pedestrian/unknown`), `state` (màu/trạng thái: `red/yellow/red_yellow/green/off/unknown`), `ego_relevance` (trực tiếp điều khiển hướng đi đã cho của xe: `relevant/not_relevant/unknown`) và `needs_review` (cần kiểm tra: `true/false`). `red_yellow` dùng khi bóng đỏ và bóng vàng cùng sáng trong một vỏ. Đèn đi bộ ghi `state` theo ý nghĩa ký hiệu đang sáng: người đi → `green`, bàn tay/người đứng → `red`. Ba dropdown mặc định `__undefined__` (chưa chọn) để annotator buộc phải tự chọn; `__undefined__` còn trong export là lỗi quên gán, không phải `unknown`.
3. **Failure nào gây hậu quả lớn nhất?** Gán sai quan hệ giữa đèn và hướng đi thẳng: đánh dấu mũi tên trái đỏ của làn rẽ là `relevant`, hoặc đánh dấu đèn tròn đang điều khiển làn đi thẳng là `not_relevant`/bỏ sót nó. Ở ảnh ban đêm, vẽ đèn hậu/đèn phanh của xe khác thành đèn đỏ cũng tạo tín hiệu dừng giả.
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?** Gán `unknown` cho thuộc tính chưa rõ và `needs_review=true` trên đối tượng; nếu không xác định được đối tượng để đặt khung, ghi issue CVAT kèm tên ảnh và vị trí. Người phụ trách QA xem lại trên CVAT; nếu thiếu quy tắc, chuyển người phụ trách guideline bổ sung. Thiếu bằng chứng thì giữ `unknown`.

## Scope

- **Trong scope (bắt buộc label):** Các đầu đèn cho xe và người đi bộ nhận diện được trong ảnh, kể cả đèn xa, đều dùng class `traffic_light`. Đèn không trực tiếp điều khiển hướng đi đã cho vẫn được vẽ và gán `ego_relevance=not_relevant`; đèn đi bộ xác định chắc chắn luôn thuộc nhóm này.
- **Ngoài scope (ignore):** Đèn gắn trên xe (đèn hậu, đèn phanh, xi-nhan), đèn chiếu sáng, biển báo và hình phản chiếu; không suy đoán ý định của tài xế hoặc quyết định lái xe.
- **Geometry tolerance:** `rectangle` ôm phần vỏ đầu đèn nhìn thấy, không gồm cột và quầng sáng; dung sai đề xuất tối đa 2 pixel mỗi cạnh trên ảnh gốc (LISA 1280 × 960, BDD100K 1280 × 720). Không xác định được vỏ để đặt khung thì ghi issue CVAT, không vẽ box phỏng đoán.

## Output chấm được

Đối chiếu ảnh và export CVAT với gold đã freeze theo các decision sau:

| Decision / nội dung chấm | Bằng chứng trong export CVAT |
|---|---|
| **LABEL — class, attribute** | Có box `traffic_light` cho từng đầu đèn trong scope; `signal_type`, `state`, `ego_relevance` khớp expected decision. |
| **IGNORE** | Không có annotation cho đối tượng ngoài scope, như đèn xe hoặc biển báo; kiểm tra sự vắng mặt của annotation tại đối tượng đó trên ảnh. Không dùng IGNORE chỉ vì đèn có `ego_relevance=not_relevant`. |
| **UNKNOWN** | Attribute chưa đủ bằng chứng mang giá trị `unknown` do annotator chủ động chọn, không để `__undefined__` hoặc đoán một giá trị cụ thể. |
| **ESCALATE** | Đối tượng có `needs_review=true` trong export. Issue không gắn được với box là thông tin review bổ sung, không chấm như decision ESCALATE từ export. |
| **Geometry** | Tọa độ box `traffic_light` tuân thủ quy tắc phần vỏ nhìn thấy và dung sai đã nêu. |

Các tên class, label, attribute và giá trị trên cần được dùng thống nhất khi viết `02_guideline.md`, `03_ontology_and_cvat_setup.md` và `03_cvat_labels.json`.

## Dữ liệu và giới hạn

Nguồn: `data/lisa/` (30 frame liên tiếp của một clip, cùng một giao lộ, 1280 × 960) và `data/bdd100k/` (ảnh đơn lẻ nhiều cảnh, 1280 × 720). Theo `sample_pack.csv`: 4 ảnh minh họa (BDD07, BDD11, LISA01, LISA15), 6 ảnh hiệu chỉnh nội bộ (BDD17, BDD18, BDD20, BDD22, LISA10, LISA25) và 4 ảnh kiểm thử với nhóm khác, không trùng nhau. Giới hạn: tình huống mũi tên trái đỏ + tròn xanh chỉ có trong một clip LISA; ảnh BDD không có mũi tên rẽ, nhiều đèn nhỏ/xa hoặc mờ do mưa, ban đêm; thông tin làn xe trong cả hai nguồn còn hạn chế. Schema có hỗ trợ mũi tên phải/thẳng và `red_yellow`, nhưng chỉ gán/chấm khi có bằng chứng trong ảnh.
