# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền. Thay mọi placeholder mới
là xong (gate G5).

- **Nhóm peer:** NoLimit
- **Người label blind:** Hoàng Gia Linh

## 1. Peer trả lời

### 1. Rule nào rõ nhất / giúp quyết định nhanh nhất?

- **Đơn vị đối tượng (mục 2):** một vỏ vật lý = một box; không gộp đầu mũi tên trái với đầu đèn tròn, không vẽ riêng từng bóng. Ở LISA30 (mũi tên trái đỏ + đèn tròn xanh ở hai vỏ riêng) quyết định được ngay.
- **Đèn đi bộ:** luôn `ego_relevance=not_relevant`

### 2. Rule nào mơ hồ hoặc phải tự suy diễn?

- **Hướng giả định (mục 4, bước 1)**: guideline ghi "Đọc hướng giả định trong task. Nếu thiếu/mâu thuẫn, xử lý theo mục 1". Nhưng mục 1 không nói gì về hướng giả định, nên đây là tham chiếu vòng. Mục 10 lại bảo "thiếu thì escalate". Pack blind cũng không ghi hướng cho ảnh nào. Kết quả là mọi box chỉ còn cách gán ego_relevance=unknown.
- **"Đủ bằng chứng quan hệ làn–đèn":** không có ngưỡng cụ thể. Ở LISA30 ego đã ở trong giao lộ, không thấy vạch làn bên dưới → hai annotator dễ kết luận khác nhau.
- **"Xác định được biên vỏ":** quyết định có vẽ box hay không nhưng không có tiêu chí, đặc biệt ban đêm hoặc vỏ tối trên nền cây.
- **Dung sai 2 px "so với khung tham chiếu":** annotator không có khung tham chiếu; dung sai ghi cho ảnh 1280×960 trong khi 3/4 ảnh blind là 1280×720.
- **Đèn nhìn từ cạnh/sau:** chỉ có rule cho đèn xe; vỏ đèn đi bộ nhìn nghiêng (BDD12, bên trái cột) chưa có rule.
- **Đèn ở giao lộ phía xa:** không rõ khi nào đủ bằng chứng cho `not_relevant`, khi nào phải `unknown`.

### 3. Sample nào khiến guideline “vỡ”?

- **Cả pack:** guideline ghi phạm vi "ảnh LISA, 30 frame cùng một clip, 1280×960", nhưng pack có 3 ảnh BDD (1280×720, khác cảnh) và chỉ 1 ảnh LISA. Theo đúng chữ, ảnh BDD nằm ngoài scope.
- **BDD26 (ban đêm) — vỡ nặng nhất:** có đèn xanh rất rõ ngay phía trên và một vệt đỏ giống bàn tay đi bộ, nhưng chỉ thấy quầng sáng, không thấy vỏ. Làm đúng rule "không thấy vỏ → không vẽ, chỉ ghi issue" thì export ra 0 box; issue không nằm trong export nên thông tin về các đèn này mất hẳn khi chấm.
- **LISA30:** đúng tình huống trọng tâm nhưng không có hướng giả định nên không kiểm tra được mục tiêu chính.

### 4. Attribute/default nào trong CVAT dễ gây thao tác sai?

- Không có

### 5. Một thay đổi cụ thể giúp annotator mới ít hỏi hơn?

Thêm vào **mục 1** một dòng ghi rõ hướng giả định, ví dụ: _"Hướng giả định cho mọi ảnh: đi thẳng theo làn ego hiện tại, trừ khi bảng Task ghi khác"_, kèm bảng `ảnh → hướng`. Nếu cố ý không cho hướng thì ghi thẳng: _"Không có hướng → `ego_relevance=unknown`, `needs_review=true` cho mọi đèn xe"_. Thay đổi này xử lý câu hỏi chắc chắn gặp ở mọi ảnh và sửa luôn tham chiếu vòng về mục 1.

## 2. Owner phân loại

Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai được xếp vào một hướng xử lý.

| Feedback / decision sai                                                                     | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý (accept + revise / reject with evidence / add escalation rule) | Bằng chứng                                                                                                |
| ------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| Tham chiếu vòng hướng giả định, ảnh không có hướng dẫn tới mọi box đều bị gán `unknown`     | guideline gap                                                  | accept + revise                                                      | Bổ sung quy tắc ngầm định vào mục 1: "Nếu task không ghi chú, hướng giả định luôn là đi thẳng".           |
| BDD26 (ban đêm): Làm đúng rule "không thấy vỏ -> không vẽ" làm mất box tín hiệu quan trọng  | guideline gap                                                  | accept + revise                                                      | Cập nhật mục 6: Thêm ngoại lệ ban đêm/lóa sáng, cho phép khoanh quầng sáng (theo định hướng EC-03).       |
| Phạm vi dữ liệu và dung sai mâu thuẫn: Chỉ định LISA 1280x960 nhưng lại có ảnh BDD 1280x720 | guideline gap                                                  | accept + revise                                                      | Sửa Scope ở mục 1 bao gồm cả BDD100K và điều chỉnh lại câu chữ về dung sai 2px ở mục 3 cho linh hoạt hơn. |
| Mơ hồ ngưỡng "đủ bằng chứng làn-đèn" khi xe đã lọt vào sâu giữa giao lộ (LISA30)            | data ambiguity                                                 | add escalation rule                                                  | Bổ sung vào mục 4: Nếu xe ở vị trí mất vạch làn để đối chiếu, gán `unknown` và bật `needs_review=true`.   |
| Đèn đi bộ nhìn nghiêng từ cạnh/sau (như trong BDD12) chưa có luật giải quyết rõ ràng        | guideline gap                                                  | accept + revise                                                      | Bổ sung rule vào bảng mục 5: "Đèn đi bộ không thể thấy tín hiệu mặt trước -> IGNORE".                     |
