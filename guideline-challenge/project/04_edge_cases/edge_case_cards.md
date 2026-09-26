# Edge-case library

Tối thiểu **8 card**, khuyến nghị 10–12. Một edge case tốt là case mà hai annotator hợp lý có thể làm khác nhau nếu guideline chưa rõ. Cần có đủ độ đa dạng: occlusion / truncation / small-far / ambiguous semantics / conflicting road elements / một case critical-risk / một case guideline cho phép escalation.

---

CASE ID: EC-01
Sample: LISA10
Scene: Ngã tư nhiều cây
Observation: Đèn đỏ bị cành cây che khuất một phần nhỏ.
Decision: LABEL
Expected: label=traffic_light, attribute=red, geometry ôm sát phần đèn nhìn thấy được.
Rationale: Vẫn nhận diện được màu sắc để xe tự lái dừng lại an toàn.
Common mistake: Bỏ qua không label vì nghĩ đèn bị che lấp.
Diversity: occlusion

---

CASE ID: EC-02
Sample: BDD17
Scene: Đường phố trời mưa
Observation: Đèn ở ngã tư phía xa, bị nhòe và biến dạng bởi hạt mưa trên kính lái.
Decision: UNKNOWN
Expected: label=traffic_light, attribute=unknown.
Rationale: Tránh việc hệ thống AI học sai màu do nhiễu hạt mưa.
Common mistake: Tự đoán bừa màu đèn dù không nhìn rõ.
Diversity: small_far

---

CASE ID: EC-03
Sample: BDD26
Scene: Đường phố ban đêm
Observation: Nguồn sáng lóa mạnh, không nhìn thấy vỏ đen của hộp đèn, rất dễ nhầm với đèn đường.
Decision: LABEL
Expected: geometry: box chỉ ôm phần sáng lóa của đèn (vì không thấy phần vỏ).
Rationale: Nhận diện dựa vào vị trí lơ lửng giữa ngã tư.
Common mistake: Cố vẽ box quá to ước lượng cả phần vỏ không nhìn thấy.
Diversity: ambiguous

---

CASE ID: EC-04
Sample: BDD07
Scene: Đường lớn ngang qua ngã tư
Observation: Cụm đèn chính màu đỏ nhưng có đèn phụ (mũi tên rẽ phải) đang sáng màu xanh.
Decision: ESCALATE
Expected: Báo cáo với nhóm để có luật thống nhất: tách làm 2 box riêng hay gộp 1 box chung.
Rationale: Cần quy tắc rõ ràng để phân biệt đèn nào áp dụng cho hướng rẽ nào.
Common mistake: Gộp chung thành 1 box có attribute=red làm xe tự lái không dám rẽ phải.
Diversity: conflicting

---

CASE ID: EC-05
Sample: BDD18
Scene: Đường phố ban đêm
Observation: Đèn phản quang màu đỏ chót lơ lửng trên đuôi xe tải lớn phía trước, nhìn rất giống đèn giao thông.
Decision: IGNORE
Expected: Không label đèn này.
Rationale: Nếu label nhầm, xe tự lái có thể hiểu nhầm đây là ngã tư và phanh gấp gây tai nạn thảm khốc phía sau.
Common mistake: Bị đánh lừa, label nhầm đèn đuôi xe tải thành traffic_light.
Diversity: critical

---

CASE ID: EC-06
Sample: BDD11
Scene: Ngã tư nội ô
Observation: Đèn tín hiệu dành cho người đi bộ (hình người xanh/đỏ) lọt vào khung hình.
Decision: IGNORE
Expected: Không label các đèn tín hiệu dành riêng cho người đi bộ.
Rationale: Đề tài này chỉ tập trung vào đèn điều khiển làn xe ô tô chạy.
Common mistake: Gom nhóm và label tất cả mọi loại đèn phát sáng.
Diversity: rule_gap

---

CASE ID: EC-07
Sample: BDD24
Scene: Trời bão tuyết
Observation: Tuyết rơi bám dày đặc vào cụm đèn, che lấp hoàn toàn ánh sáng và màu sắc.
Decision: ESCALATE
Expected: Gửi cho nhóm để quyết định xem có được dùng hành vi của xe cộ xung quanh để suy luận đèn hay không.
Rationale: Điều kiện thời tiết quá khắc nghiệt cần có rule xử lý ngoại lệ riêng.
Common mistake: Bỏ qua hoàn toàn cụm đèn không màng tới.
Diversity: escalation

---

CASE ID: EC-08
Sample: LISA30
Scene: Cận cảnh xe sát ngã tư
Observation: Cụm đèn giao thông lọt ra mép ảnh, bị khung hình cắt đi một nửa.
Decision: LABEL
Expected: label=traffic_light, vẽ box ôm sát phần đèn lọt trong khung hình.
Rationale: AI vẫn cần bám sát nhận diện đèn ngay cả khi xe đã tiến sát ngã tư và đèn trôi dần ra mép ảnh.
Common mistake: Cho rằng đèn không hoàn chỉnh nên lờ đi.
Diversity: truncation

---

CASE ID: EC-09
Sample: LISA01
Scene: Ngã tư có làn rẽ nhánh
Observation: Đèn tín hiệu tròn (đi thẳng) và đèn mũi tên (rẽ trái) cùng sáng màu đỏ và nằm sát sát cạnh nhau.
Decision: LABEL
Expected: Vẽ 2 bounding box tách biệt hoàn toàn cho 2 cụm đèn.
Rationale: Downstream Contract - Hệ thống phân làn (Lane Assignment) cần 2 object riêng rẽ để map chính xác trạng thái cho làn đi thẳng và làn rẽ trái.
Common mistake: Gom biếng, vẽ 1 box lớn bao trọn cả hai cụm đèn khiến hệ thống không bóc tách được tín hiệu.
Diversity: granularity / multi-light

---

CASE ID: EC-10
Sample: BDD12
Scene: Ngã tư trung tâm thành phố ngược sáng
Observation: Ánh sáng mặt trời chiếu hắt thẳng từ phía sau cụm đèn (backlight), khiến cụm đèn tối thui (ngược sáng), hoàn toàn không thể thấy màu nào đang sáng.
Decision: UNKNOWN
Expected: label=traffic_light, attribute=unknown.
Rationale: Downstream Contract - Tránh việc model Perception tự "bịa" màu (hallucination). Hệ thống xe tự lái khi nhận tín hiệu unknown sẽ chủ động giảm tốc và quan sát luồng giao thông cắt ngang (Fail-safe).
Common mistake: Cố gắng đoán bừa màu sắc bằng cách nhìn xe cộ xung quanh.
Diversity: backlight / ambiguous semantics
