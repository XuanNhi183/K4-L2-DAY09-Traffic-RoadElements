# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây. Thay mọi
placeholder mới là xong (gate G2).

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `traffic_light` | `rectangle` | class | Không áp dụng | Không áp dụng | Không áp dụng | Một đối tượng cho một vỏ đầu đèn cho xe hoặc người đi bộ; không chia class theo màu hoặc hướng |
| `signal_type` | Trên box `traffic_light` | attribute — select | `circular`, `left_arrow`, `right_arrow`, `straight_arrow`, `pedestrian`, `unknown` | `unknown` | Không | Phân biệt tín hiệu tròn, mũi tên trái/phải/thẳng và tín hiệu người đi bộ; không đủ bằng chứng thì `unknown` |
| `state` | Trên box `traffic_light` | attribute — select | `red`, `yellow`, `green`, `white`, `orange`, `off`, `unknown` | `unknown` | Không | Ghi màu/trạng thái quan sát được; `white/orange` dành cho đèn đi bộ khi có bằng chứng, không đổi sang xanh/đỏ |
| `ego_relevance` | Trên box `traffic_light` | attribute — select | `relevant`, `not_relevant`, `unknown` | `unknown` | Không | Quan hệ điều khiển trực tiếp với hướng đi giả định; đèn đi bộ chắc chắn là `not_relevant`, không có nghĩa người đi bộ không ảnh hưởng việc lái xe |
| `needs_review` | Trên box `traffic_light` | attribute — checkbox | `false`, `true` | `false` | Không | Đánh dấu đối tượng có thông tin chưa xác định hoặc cần reviewer xử lý |

Checkbox trong CVAT Raw dùng `values=["false"]`, `default_value="false"`; khi gán nhãn, trạng thái trên đối tượng là `true` hoặc `false`. Các dropdown mặc định `unknown`. Annotator phải kiểm tra từng trường; khi hoàn thành mà vẫn thiếu bằng chứng thì giữ `unknown` và bật `needs_review=true`.

## Class hay attribute

`traffic_light` là class vì các đầu đèn cho xe và người đi bộ dùng chung đơn vị vỏ đèn và geometry rectangle. Loại tín hiệu, màu và quan hệ với ego là các thuộc tính của đầu đèn, nên không tạo class riêng cho từng tổ hợp. Đèn đi bộ dùng `signal_type=pedestrian`, `ego_relevance=not_relevant`; vẫn ghi màu quan sát được. Đối tượng cần kiểm tra dùng `needs_review`; vấn đề không gắn được với box được ghi bằng issue CVAT, không tạo label cấp ảnh.

Các dropdown mặc định `unknown` để tránh tự tạo nhãn `red`, `circular` hoặc `relevant` khi annotator quên chọn. Giá trị mặc định không chứng minh đối tượng đã được kiểm tra; annotator phải đọc đủ từng trường trước khi nộp. `needs_review` mặc định `false`, phải bật khi có thuộc tính `unknown` hoặc vấn đề cần review. Task ảnh tĩnh dùng Shape, mọi attribute đặt `mutable=false`.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): TODO
- **Tên task calibration** (có version guideline, ví dụ `team07-calib-v1`): TODO
- **Guide của task đã dán `02_guideline.md`?** TODO (có / chưa)
- **Nhóm dùng Track hay Shape, vì sao:** TODO

## Setup test

Một thành viên **chưa tham gia setup** mở task và trả lời: label gì, dùng tool nào, gán attribute nào, khi nào
escalate. Ghi lại ai test và chỗ họ vấp:

TODO
