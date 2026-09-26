# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây. Thay mọi
placeholder mới là xong (gate G2).

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `traffic_light` | `rectangle` | class | Không áp dụng | Không áp dụng | Không áp dụng | Một đối tượng cho một vỏ đầu đèn cho xe hoặc người đi bộ; không chia class theo màu hoặc hướng |
| `signal_type` | Trên box `traffic_light` | attribute — select | `circular`, `left_arrow`, `right_arrow`, `straight_arrow`, `pedestrian`, `unknown` | `__undefined__` (chưa chọn) | Không | Phân biệt tín hiệu tròn, mũi tên trái/phải/thẳng và tín hiệu người đi bộ; không đủ bằng chứng thì `unknown` |
| `state` | Trên box `traffic_light` | attribute — select | `red`, `yellow`, `red_yellow`, `green`, `off`, `unknown` | `__undefined__` (chưa chọn) | Không | Ghi màu/trạng thái quan sát được; `red_yellow` khi bóng đỏ và bóng vàng cùng sáng trong một vỏ; đèn đi bộ ghi theo ý nghĩa ký hiệu: người đi → `green`, bàn tay/người đứng → `red` |
| `ego_relevance` | Trên box `traffic_light` | attribute — select | `relevant`, `not_relevant`, `unknown` | `__undefined__` (chưa chọn) | Không | Quan hệ điều khiển trực tiếp với hướng đi giả định; đèn đi bộ chắc chắn là `not_relevant`, không có nghĩa người đi bộ không ảnh hưởng việc lái xe |
| `needs_review` | Trên box `traffic_light` | attribute — checkbox | `false`, `true` | `false` | Không | Đánh dấu đối tượng có thông tin chưa xác định hoặc cần reviewer xử lý |

Checkbox trong CVAT Raw dùng `values=["false"]`, `default_value="false"`; khi gán nhãn, trạng thái trên đối tượng là `true` hoặc `false`. Ba dropdown mặc định `__undefined__`: trong `03_cvat_labels.json`, `__undefined__` đứng đầu `values` và là `default_value`, nhưng **không phải giá trị được phép nộp**, nên cột Allowed values không liệt kê nó. Annotator phải tự chọn từng trường; khi đã đọc ảnh mà vẫn thiếu bằng chứng thì chọn `unknown` và bật `needs_review=true`. Export còn `__undefined__` nghĩa là quên gán.

## Class hay attribute

`traffic_light` là class vì các đầu đèn cho xe và người đi bộ dùng chung đơn vị vỏ đèn và geometry rectangle. Loại tín hiệu, màu và quan hệ với ego là các thuộc tính của đầu đèn, nên không tạo class riêng cho từng tổ hợp. Đèn đi bộ dùng `signal_type=pedestrian`, `ego_relevance=not_relevant`; `state` ghi theo ý nghĩa ký hiệu đang sáng (người đi → `green`, bàn tay/người đứng → `red`) vì schema không có màu trắng/cam. Đối tượng cần kiểm tra dùng `needs_review`; vấn đề không gắn được với box được ghi bằng issue CVAT, không tạo label cấp ảnh.

Các dropdown mặc định `__undefined__` thay vì một giá trị thật: nếu mặc định là `red`, `circular` hoặc `relevant`, annotator quên chọn sẽ tạo nhãn "im lặng"; nếu mặc định là `unknown`, export không phân biệt được "quên chọn" với "đã xem nhưng không đủ bằng chứng". Với `__undefined__`, mọi giá trị trong export đều là lựa chọn có chủ ý, và `py lab9.py calib` tách được lỗi quên gán khỏi bất đồng thật. `needs_review` mặc định `false`, phải bật khi có thuộc tính `unknown` hoặc vấn đề cần review. Task ảnh tĩnh dùng Shape, mọi attribute đặt `mutable=false`.

## CVAT

- **Phiên bản CVAT** (`py lab9.py cvat`): 2.75.1
- **Tên task calibration** (có version guideline, ví dụ `team07-calib-v1`): mỗi người một task `traffic-calib-<tên>` (ví dụ
  `traffic-calib-hoang`), dựng theo guideline v1 với bộ calibration 6 ảnh BDD/LISA
- **Guide của task đã dán `02_guideline.md`?** Có
- **Nhóm dùng Track hay Shape, vì sao:** Shape. Mỗi ảnh là một quan sát độc lập (ảnh BDD đơn lẻ, frame LISA không liên
  tiếp), guideline mục 8 không dùng temporal rule; export bằng **CVAT for images 1.1**

## Hướng dẫn tạo task calibration trên máy mình

Mỗi người tự tạo task riêng trên CVAT của máy mình và **tự vẽ, không xem bài nhau**. Làm đúng thứ tự dưới đây.
Chi tiết nút bấm xem thêm `GUIDE.md` mục 2–4.

### Bước 0 — Chuẩn bị (một lần)

1. Mở **Docker Desktop**, đợi chạy xong. Trong terminal, vào thư mục CVAT của bạn (thường là `cvat-day2`) và gõ
   `docker compose start`.
2. Mở terminal trong thư mục `guideline-challenge` của repo nhóm, lấy bản mới nhất và kiểm tra CVAT:

   ```powershell
   git pull
   py lab9.py cvat
   ```

   Thấy `✓ CVAT ... tại http://localhost:8080` là được.
3. Gom ảnh calibration:

   ```powershell
   py lab9.py pack calibration
   ```

   Lệnh tạo thư mục `guideline-challenge\build\calibration\` có **6 ảnh**: BDD17, BDD18, BDD20, BDD22, LISA10, LISA25.
   Ảnh BDD (1280 × 720) và LISA (1280 × 960) khác kích thước, bình thường.
4. Mở Chrome hoặc Edge, vào `http://localhost:8080`, đăng nhập tài khoản CVAT của bạn.

### Bước 1 — Tạo task

1. Trang **Tasks** → nút **+** → **Create a new task**.
2. **Name:** `traffic-calib-<tên bạn>`, ví dụ `traffic-calib-hoang`. Viết không dấu, không khoảng trắng.
3. **Labels:** bấm tab **Raw**, xoá hết nội dung có sẵn, mở `project\03_cvat_labels.json`, copy **toàn bộ** rồi dán
   vào, bấm **Done**/**Save**.
4. Chuyển sang tab **Constructor** và kiểm tra:
   - Có đúng **1 label** `traffic_light`, kiểu **Rectangle**.
   - Có 4 attribute: `signal_type`, `state`, `ego_relevance` (dạng select) và `needs_review` (checkbox).
   - `state` có `red_yellow`, **không** có `white` hay `orange`. Ba dropdown mặc định `__undefined__`.

   Sai bất kỳ điểm nào: bạn chưa `git pull` hoặc dán thiếu, làm lại bước 3.
5. **Select files** → **My computer** → mở `guideline-challenge\build\calibration\`, chọn **cả 6 ảnh**.
6. Mở **Advanced configuration**, giữ nguyên **Sorting method** mặc định. Không đổi gì khác.
7. Bấm **Submit & Open**.

### Bước 2 — Dán guideline và hướng đi vào Guide

1. Ở trang task, dưới **Task description** bấm **Edit**.
2. Dòng **đầu tiên** ghi đúng dòng hướng đi dưới đây (cả nhóm và nhóm peer dùng cùng một dòng, guideline mục 1):

   ```text
   Hướng di chuyển giả định của ego: đi thẳng
   ```

3. Xuống dòng, dán **toàn bộ** nội dung `project\02_guideline.md`, bấm **Submit**.
4. Bấm dòng **Job #…** để mở màn hình vẽ. Nút **Guide** ở góc trên phải mở lại nội dung vừa dán.

### Bước 3 — Vẽ

- Mỗi **vỏ đầu đèn** là một box: thanh công cụ trái → **Draw new rectangle** → label `traffic_light` → **Shape**,
  click góc trên trái rồi góc dưới phải. Vẽ box tiếp theo: phím **N**.
- Quét hết ảnh, kể cả **đèn nhỏ ở xa**, đèn bị cây che, đèn mờ sau giọt mưa và **đèn đi bộ**. Ảnh ban đêm: không vẽ
  đèn hậu/đèn phanh của xe khác, không vẽ biển báo. Ảnh không có đầu đèn nào thì không vẽ gì, cứ chuyển ảnh tiếp.
- Với **từng box**, ở sidebar phải (tab **Objects**) chọn đủ **ba dropdown**. Không box nào được để
  `__undefined__`. Đã xem kỹ mà vẫn không chắc thì chọn `unknown` **và** tích `needs_review`.
- Chuyển ảnh: **F** (sau) / **D** (trước). Lưu thường xuyên: **Ctrl+S**.
- Không hỏi, không nhìn bài người khác. Chỗ nào guideline không trả lời được thì ghi lại câu hỏi. Đó chính là dữ
  liệu cho calibration.

### Bước 4 — Export và nộp

1. **Ctrl+S** lần cuối.
2. Trang task → **Actions** → **Export task dataset** (hoặc trong job: **Menu** → **Export job dataset**).
3. **Export format:** `CVAT for images 1.1`. **Save images: tắt.** Bấm **OK**.
4. Tải file từ thông báo hoặc trang **Requests** trên thanh trên cùng.
5. Đổi tên file thành `<tên bạn>.zip` (không dấu, ví dụ `hoang.zip`), chép vào `project\06_calibration_exports\`.
   Lab9 lấy tên file làm tên người vẽ.
6. Commit và push file zip đó, báo nhóm. Khi đủ file, người QA chạy `py lab9.py calib` (xem `README.md`, bước 04).

### Khi gặp lỗi

| Hiện tượng | Cách xử lý |
|---|---|
| `py lab9.py cvat` báo CVAT chưa chạy | Mở Docker Desktop, `docker compose start` trong thư mục CVAT, đợi 1 phút rồi thử lại |
| Không thấy `build\calibration\` | Chưa chạy `py lab9.py pack calibration`, hoặc chưa `git pull` nên `sample_pack.csv` còn trống |
| Constructor thiếu label hoặc attribute | Xoá task, `git pull`, tạo lại và dán lại toàn bộ JSON |
| Upload thiếu ảnh hoặc dán nhầm labels | Xoá task và tạo lại, nhanh hơn sửa |
| Dropdown bấm chuột không ăn | Bấm vào ô rồi dùng **↓** và **Enter** |
| `calib` báo export thiếu hoặc thừa ảnh | Task không có đủ đúng 6 ảnh calibration, tạo lại task |

## Setup test

Một thành viên **chưa tham gia setup** mở task và trả lời: label gì, dùng tool nào, gán attribute nào, khi nào
escalate. Ghi lại ai test và chỗ họ vấp:

Hoan tat
