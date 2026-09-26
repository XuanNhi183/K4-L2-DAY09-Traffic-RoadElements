# Annotation guideline — Đèn giao thông áp dụng cho hướng đi dự kiến của ego vehicle trong ảnh LISA

**Version:** v2

<!--
v0 = chưa có bản nháp. Đổi dòng Version ở trên thành v1 khi xong bản nháp đầu, v2 sau calibration, v3 sau blind
handoff; mỗi lần tăng version ghi một dòng vào 08_revision_log.md. `make freeze` đòi v2 trở lên.

File này là thứ nhóm peer nhận nguyên văn trong blind pack và là Guide dán vào CVAT. Peer KHÔNG nhận
edge_case_cards.md, gold_decisions.csv hay sample_pack.csv. Rule nào peer cần biết phải nằm ở đây.
No hidden rules: rule chỉ giải thích bằng miệng thì coi như không tồn tại.
Ví dụ trong guideline chỉ dùng ảnh split example hoặc calibration, không dùng ảnh blind.
-->

## 1. Objective + scope

### Mục tiêu

Với từng ảnh LISA, khoanh các đầu đèn giao thông dành cho xe và người đi bộ, ghi loại tín hiệu và trạng thái nhìn thấy, sau đó xác định đầu đèn nào trực tiếp điều khiển hướng di chuyển dự kiến của ego vehicle (xe gắn camera). Tình huống trọng tâm là đèn tròn xanh xuất hiện đồng thời với mũi tên trái đỏ ở các đầu đèn riêng biệt. Đèn đi bộ được gán nhãn để phân biệt với tín hiệu cho xe.

Output phục vụ mô hình nhận diện tín hiệu liên quan đến ego. Không suy ra lệnh lái xe, quyền ưu tiên, việc được phép rẽ hay quyết định đi/dừng. Một màu xanh nhìn thấy trong ảnh không tự động là tín hiệu áp dụng cho ego.

### Đầu vào bắt buộc: hướng đi dự kiến

- Trong CVAT, người tạo task ghi một dòng theo đúng mẫu ở đầu **Task description** (ô Markdown dùng để dán guideline), trước nội dung guideline: **“Hướng di chuyển giả định của ego: đi thẳng”**, **“Hướng di chuyển giả định của ego: rẽ trái”** hoặc **“Hướng di chuyển giả định của ego: rẽ phải”**. Dùng cùng một dòng cho tất cả annotator và peer làm cùng task. Đây là giả định của bài đánh giá, không phải ý định thực tế suy ra từ ảnh.
- Một task dùng một hướng giả định thống nhất. Annotator không tự đổi hướng giữa các ảnh. Hướng này phải giống nhau giữa các annotator và giữa owner với peer khi chấm cùng task.
- Vạch/mũi tên trên đường dùng để xác định làn và kiểm tra quan hệ đèn–làn, không thay thế đầu vào hướng đi. Không mặc định đi thẳng khi ảnh thiếu thông tin; không suy ra xe đang rẽ chỉ vì đường cong.
- Nếu thiếu hướng, có hai hướng mâu thuẫn hoặc hướng ngoài phạm vi đi thẳng/rẽ trái/rẽ phải: vẫn ghi geometry, loại và trạng thái đèn; đặt `ego_relevance=unknown`, `needs_review=true` cho các đầu đèn chưa giải quyết được quan hệ và ghi issue CVAT về đầu vào chưa rõ. Đèn đã xác định chắc chắn là đèn đi bộ vẫn có `ego_relevance=not_relevant`. Người tạo task phải làm rõ đầu vào; không tự chọn một hướng.

### Phạm vi

- Trong scope: đầu đèn giao thông dành cho xe hoặc người đi bộ nhận diện được trong ảnh, kể cả đèn nhỏ/xa, đèn không áp dụng cho ego và đèn có trạng thái chưa rõ.
- Ngoài scope: đèn gắn trên xe, đèn đường chiếu sáng, biển báo và hình phản chiếu của đèn. Quy tắc cụ thể ở mục 5.
- Ảnh nguồn: `data/lisa/`, 30 frame cùng một clip. Xét từng ảnh độc lập. Không mặc định mọi đèn trong ảnh đều thuộc giao lộ của ego hoặc cùng điều khiển một hướng.
- Các giá trị bổ sung cho mũi tên phải/thẳng và đèn đi bộ chỉ được dùng khi ảnh có bằng chứng tương ứng; schema hỗ trợ không có nghĩa clip đã có đủ mọi loại để kiểm thử.

## 2. Annotation unit

- Đơn vị dữ liệu là **một ảnh**; đơn vị đối tượng là **một đầu đèn vật lý**, tức một vỏ chứa các bóng đèn gắn liền nhau. Dùng Rectangle ở chế độ **Shape**, không dùng Track.
- Một vỏ chứa nhiều bóng đỏ/vàng/xanh vẫn là một instance, không vẽ riêng từng bóng. Hai vỏ tách rời là hai instance, dù gắn chung cần đèn, cùng màu hoặc cùng điều khiển một hướng.
- Không gộp đầu đèn mũi tên trái và đầu đèn tròn nằm cạnh nhau thành một box. Không bỏ một đầu đèn vì đã vẽ đầu đèn khác cùng chức năng.
- Cùng một đầu đèn xuất hiện ở hai ảnh được gán nhãn riêng trên mỗi ảnh; không dùng ID ở ảnh trước để quyết định nội dung ảnh sau.
- Một vỏ bị che thành nhiều phần vẫn là một instance nếu có bằng chứng chúng thuộc cùng vỏ; cách khoanh ở mục 3. Nếu không biết là một hay hai vỏ, không đoán số lượng, chuyển review theo mục 7.
- Chỉ dùng class `traffic_light`, không có tag toàn ảnh. Các vấn đề không gắn được với box ghi bằng issue CVAT.

## 3. Geometry rule

1. Dùng geometry `rectangle`, class `traffic_light`. Vẽ từ góc trên trái đến góc dưới phải của khung bao phần vỏ nhìn thấy.
2. Khung ôm **vỏ đầu đèn**, không chỉ ôm bóng đang sáng. Không gồm cột, cần vươn, giá đỡ, biển bên cạnh, tấm nền rộng phía sau vỏ hoặc quầng sáng. Phần chụp che nắng gắn liền với vỏ nằm trong khung.
3. Dùng quy tắc **visible**, không phải amodal: không kéo dài box để đoán phần bị che hoặc phần ngoài ảnh. Nếu nhiều mảnh của cùng vỏ còn nhìn thấy, lấy khung chữ nhật nhỏ nhất chứa các mảnh đó; khung có thể bao cả vùng che nằm giữa chúng.
4. Vỏ bị cắt ở mép ảnh: cạnh tương ứng của box dừng tại mép ảnh. Không tạo tọa độ ngoài ảnh. Đánh dấu thuộc tính chưa đọc được theo mục 6.
5. **Dung sai:** mỗi cạnh được lệch tối đa 2 pixel so với khung tham chiếu theo quy tắc trên, tính trên ảnh gốc 1280 × 960, không phải pixel màn hình sau khi zoom. Box vẫn phải có chiều rộng và chiều cao dương. Dung sai không cho phép đổi từ “vỏ đèn” sang “bóng sáng”.
6. Có thể phóng to để đặt cạnh, nhưng không tăng sáng, tô lại, dùng ảnh sinh hoặc suy ra đường viền từ ảnh khác. Nếu không thể xác định biên vỏ trong dung sai, không vẽ box giả định cho đối tượng đó; ghi vị trí cần xem lại trong issue CVAT. Các đầu đèn khác vẫn làm bình thường.

Reviewer kiểm tọa độ export trên ảnh gốc và rule visible; không chấm theo một box amodal hoặc theo kích thước quầng sáng.

## 4. Taxonomy

### Class, label và attribute

Chỉ có một class đối tượng `traffic_light`. Loại tín hiệu, màu và quan hệ với ego là attribute của cùng đối tượng, không tạo class riêng cho từng tổ hợp màu/hướng.

| Name            | Gắn vào / geometry            | Kiểu nhập       | Allowed values                                                                     | Default                     |
| --------------- | ----------------------------- | --------------- | ---------------------------------------------------------------------------------- | --------------------------- |
| `traffic_light` | Class đối tượng / `rectangle` | Rectangle Shape | Không áp dụng                                                                      | Không áp dụng               |
| `signal_type`   | Attribute của `traffic_light` | Select          | `circular`, `left_arrow`, `right_arrow`, `straight_arrow`, `pedestrian`, `unknown` | `__undefined__` (chưa chọn) |
| `state`         | Attribute của `traffic_light` | Select          | `red`, `yellow`, `red_yellow`, `green`, `off`, `unknown`                           | `__undefined__` (chưa chọn) |
| `ego_relevance` | Attribute của `traffic_light` | Select          | `relevant`, `not_relevant`, `unknown`                                              | `__undefined__` (chưa chọn) |
| `needs_review`  | Attribute của `traffic_light` | Checkbox        | `false`, `true`                                                                    | `false`                     |

Task ảnh tĩnh dùng attribute không mutable. Trong cấu hình Raw CVAT, checkbox có `input_type=checkbox`, `default_value="false"`, `values=["false"]`; trạng thái được lưu trên đối tượng là `true` hoặc `false`.

Ba dropdown mặc định `__undefined__` (nghĩa là **chưa chọn**). `__undefined__` không phải giá trị được phép nộp: annotator phải tự chọn một giá trị cho từng dropdown của mọi box. Sau khi đọc ảnh mà thuộc tính vẫn chưa đủ bằng chứng thì chủ động chọn `unknown` và bật `needs_review=true`. Box nào trong export còn `__undefined__` là lỗi quên gán thuộc tính đó, không được hiểu là `unknown`. Tên và giá trị phải được đồng bộ nguyên văn trong `03_ontology_and_cvat_setup.md` và `03_cvat_labels.json`; không dùng xen kẽ tên từ bản guideline khác.

### Cách chọn `signal_type` và `state`

- `circular`: tín hiệu đọc được là hình tròn; `left_arrow`, `right_arrow`, `straight_arrow`: nhận ra mũi tên hướng trái, phải hoặc thẳng (mũi tên lên). Hướng của ký hiệu và vị trí vỏ trong ảnh là hai thứ khác nhau: không chọn mũi tên trái chỉ vì vỏ ở bên trái ảnh.
- `pedestrian`: nhận diện chắc chắn đầu đèn cho người đi bộ từ ký hiệu người đi bộ, bàn tay hoặc chữ dành cho người đi bộ. Không dùng biển báo có hình người làm bằng chứng duy nhất; phải nhận diện được đó là đầu đèn. Chỉ thấy chữ số đếm ngược mà chưa xác định được đối tượng phục vụ thì `signal_type=unknown`, `needs_review=true`.
- Đèn cho xe: ghi `red`, `yellow`, `green` theo màu của tín hiệu đang sáng trong chính vỏ đó. `red_yellow` chỉ dùng khi nhìn rõ **bóng đỏ và bóng vàng cùng sáng** trong cùng vỏ; chỉ thấy một bóng sáng mà không phân biệt được đỏ hay vàng thì `state=unknown`, không dùng `red_yellow`.
- Đèn `pedestrian`: schema không có màu trắng/cam, nên ghi `state` theo ý nghĩa ký hiệu đang sáng: người đang đi (thường trắng hoặc xanh) → `green`; bàn tay hoặc người đứng (thường cam hoặc đỏ) → `red`. Không đọc được ký hiệu nào đang sáng thì `state=unknown`, `needs_review=true`. Quy đổi này chỉ áp dụng cho đèn đi bộ.
- Nếu đèn cho xe trông trắng do chói, dùng `state=unknown` và review, không đoán màu. Không lấy màu từ đầu đèn khác, không suy màu chỉ từ vị trí bóng trên/dưới.
- Nếu đọc được màu nhưng không phân biệt được tròn/mũi tên: giữ màu đã đọc được trong `state`, đặt `signal_type=unknown` và `needs_review=true`. Không xóa một quan sát chắc chắn chỉ vì thuộc tính khác chưa rõ.
- Nếu nhận ra hình ký hiệu nhưng không chắc màu: giữ `signal_type`, đặt `state=unknown`, `needs_review=true`.
- `off`: nhìn đủ các vị trí bóng trong vỏ, không bị che/chói và không thấy bóng nào sáng. Không suy nguyên nhân hỏng/mất điện. Không dùng `off` khi chỉ một bóng tắt trong lúc bóng khác đang sáng. Khi toàn vỏ tắt, chỉ ghi loại nếu hình ký hiệu vẫn đọc được; nếu không, `signal_type=unknown`.
- Nếu có nhiều tín hiệu cùng sáng trong một vỏ nhưng khác loại hoặc khác màu, schema này không biểu diễn đầy đủ: thuộc tính nào không thể gán một giá trị duy nhất thì đặt `unknown`, bật `needs_review`; không tách một vỏ thành nhiều box hoặc tự chọn bóng ưu tiên. Ngoại lệ duy nhất về màu: bóng đỏ và bóng vàng cùng sáng thì `state=red_yellow`. Mũi tên kết hợp nhiều hướng hoặc ký hiệu khác chưa có trong schema dùng `signal_type=unknown` và chuyển review, không ép vào một hướng.
- Mọi đầu đèn được vẽ đều phải có loại và trạng thái, **kể cả `ego_relevance=not_relevant`**. Không có giá trị `not_applicable` trong schema này.

### Cách chọn `ego_relevance`

`relevant` nghĩa là có đủ bằng chứng đầu đèn trực tiếp điều khiển chuyển động dự kiến của ego; không có nghĩa đèn xanh. `not_relevant` nghĩa là có bằng chứng đầu đèn điều khiển đối tượng, hướng/làn/giao lộ khác. `unknown` nghĩa là chưa đủ bằng chứng để kết luận một trong hai.

Đèn đã nhận diện chắc chắn là `pedestrian` luôn có `ego_relevance=not_relevant` vì nó dành cho người đi bộ, không trực tiếp điều khiển xe. Đây chỉ là định nghĩa quan hệ trong annotation, không có nghĩa người đi bộ hoặc vạch qua đường không ảnh hưởng quyết định lái xe. Vẫn ghi `state` theo quy tắc đèn đi bộ ở trên (người đi → `green`, bàn tay/người đứng → `red`); nếu ký hiệu đang sáng chưa rõ thì `state=unknown`, `needs_review=true`.

Thực hiện theo thứ tự:

1. **Đọc hướng giả định** trong task. Nếu thiếu/mâu thuẫn, xử lý theo mục 1.
2. **Xác định làn ego và đường đi qua giao lộ** bằng các vạch giới hạn làn nhìn thấy, dải phân cách, vạch hướng dẫn trong giao lộ và mũi tên/biển dành cho làn nếu đọc được. Không chỉ dùng điểm giữa cạnh dưới ảnh; camera có thể lệch, và ảnh có thể không cho thấy đủ ranh giới làn.
3. **Kiểm tra quan hệ của đầu đèn với làn/hướng đó.** Cần bằng chứng về mặt đèn hướng tới luồng xe của ego và sự liên hệ với làn, như biển/mũi tên chỉ làn rõ ràng hoặc bố trí đèn cùng đường dẫn làn nhìn thấy, không có một cách ghép khác hợp lý. Chỉ “ở giữa ảnh”, “gần xe nhất”, “đang xanh”, “cùng màu đèn bên cạnh” hoặc “treo phía trên” đều không đủ nếu quan hệ làn còn mơ hồ.
4. **Chọn giá trị theo bảng dưới.** Nếu hướng giả định không phù hợp với làn nhìn thấy, không tự đổi hướng hay chuyển ego sang làn khác: đặt quan hệ chưa giải quyết là `unknown` và chuyển review.

| Hướng giả định và bằng chứng                                                                        | `ego_relevance`                                      |
| --------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Đi thẳng; xác lập được đèn tròn hoặc mũi tên thẳng điều khiển làn/luồng đi thẳng của ego            | `relevant`                                           |
| Đi thẳng; xác lập được đầu đèn chỉ điều khiển rẽ trái hoặc rẽ phải của làn rẽ riêng                 | `not_relevant`                                       |
| Rẽ trái; xác lập được mũi tên trái điều khiển đúng hướng/làn rẽ của ego                             | `relevant`                                           |
| Rẽ trái; xác lập được đèn tròn chỉ điều khiển các làn đi thẳng khác                                 | `not_relevant`                                       |
| Rẽ phải; xác lập được mũi tên phải điều khiển đúng hướng/làn rẽ của ego                             | `relevant`                                           |
| Rẽ phải; xác lập được đầu đèn chỉ điều khiển làn đi thẳng hoặc rẽ trái khác                         | `not_relevant`                                       |
| Mũi tên thẳng/trái/phải điều khiển một hướng khác hướng ego đã cho, quan hệ điều khiển được xác lập | `not_relevant`                                       |
| Xác định chắc chắn là đầu đèn cho người đi bộ                                                       | `not_relevant`, bất kể màu và hướng giả định của ego |
| Đèn thuộc luồng xe khác hoặc giao lộ khác và có bằng chứng rõ                                       | `not_relevant`                                       |
| Không xác định được làn, hướng mặt đèn, phạm vi điều khiển hoặc có bằng chứng mâu thuẫn             | `unknown`, `needs_review=true`                       |

Không thấy mũi tên tương ứng không có nghĩa mọi đèn tròn đều áp dụng cho xe rẽ trái/rẽ phải. Mũi tên tắt không tự động chuyển quyền điều khiển sang đèn tròn. Các tình huống này chỉ kết luận khi có bằng chứng quan hệ; nếu thiếu thì `unknown`.

Một hướng có thể được điều khiển bởi nhiều đầu đèn: đánh giá từng vỏ; tất cả đầu đèn có đủ bằng chứng đều được gán `relevant`, không ép chọn đúng một đèn. Nếu các đầu đèn đã xác lập cùng điều khiển một hướng hiển thị tín hiệu mâu thuẫn, giữ nguyên màu quan sát được, bật `needs_review` cho các đầu đèn đó và ghi issue CVAT mô tả sự mâu thuẫn; không sửa màu để tạo đồng thuận.

## 5. Inclusion / exclusion

| Tình huống                                                                                 | Quyết định                                                                                                                           |
| ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Nhận diện chắc chắn một đầu đèn cho xe hoặc người đi bộ và xác định được phần vỏ nhìn thấy | Vẽ `traffic_light`, điền đủ attribute                                                                                                |
| Nhận diện chắc chắn đầu đèn cho người đi bộ                                                | Vẽ `traffic_light`, `signal_type=pedestrian`, `state` theo mục 4 (người đi → `green`, bàn tay → `red`), `ego_relevance=not_relevant` |
| Đầu đèn không áp dụng cho ego nhưng vẫn nhận diện được                                     | Vẫn vẽ; ghi loại/màu thật và `ego_relevance=not_relevant` nếu có đủ bằng chứng                                                       |
| Đầu đèn xa, thuộc luồng khác hoặc giao lộ tiếp theo                                        | Vẫn vẽ nếu nhận diện và đặt khung được; không bỏ chỉ vì ở xa. Quan hệ chắc chắn khác ego là `not_relevant`, chưa rõ là `unknown`     |
| Đầu đèn cho xe nhìn từ cạnh/sau, nhận diện được vỏ nhưng không thấy tín hiệu               | Vẽ phần vỏ nhìn thấy; loại/trạng thái không đọc được là `unknown`, không gán `off`                                                   |
| Chắc chắn là đèn gắn trên xe, đèn chiếu sáng, biển báo hoặc hình phản chiếu                | IGNORE: không vẽ `traffic_light` cho vật đó                                                                                          |
| Chỉ thấy chấm sáng, chưa biết có phải đầu đèn giao thông; hoặc không xác định được biên vỏ | Không tạo box phỏng đoán; ghi issue CVAT với vị trí cần xem lại                                                                      |
| Không có đầu đèn trong scope và không có vùng nghi vấn                                     | Không vẽ box chỉ để lấp ảnh trống                                                                                                    |

Không quy định số đầu đèn cố định cho một ảnh. Quét toàn ảnh, bao gồm vùng tối và các đèn nhỏ; không dừng sau khi tìm thấy các đèn lớn phía trên.

## 6. Visibility / occlusion

| Trường hợp                                               | Geometry                                          | Attribute / review                                                                                                                                  |
| -------------------------------------------------------- | ------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Che một phần hoặc cắt mép ảnh                            | Khoanh phần vỏ còn thấy theo mục 3                | Giữ phần đọc được. Loại/màu/quan hệ nào thiếu bằng chứng thì `unknown`, `needs_review=true`                                                         |
| Che hoàn toàn                                            | Không suy ra vị trí để vẽ                         | Không tạo instance chỉ vì ảnh trước có đèn. Nếu ảnh hiện tại có bằng chứng một vùng cần kiểm tra nhưng không khoanh được, ghi issue CVAT kèm vị trí |
| Đèn nhỏ/xa, vẫn nhận diện được vỏ và biên                | Vẫn vẽ, không có ngưỡng loại chỉ theo khoảng cách | Đọc từng thuộc tính riêng. Không đủ rõ thì `unknown` và review                                                                                      |
| Nhỏ đến mức không phân biệt vỏ với chấm sáng             | Không vẽ box theo quầng sáng                      | Nếu nghi là đèn cần gán nhãn, ghi issue CVAT kèm vị trí                                                                                             |
| Chói, nhòe hoặc ánh sáng yếu                             | Khoanh theo vỏ, không theo vùng sáng lan          | Màu hoặc hình ký hiệu không chắc thì `unknown`; không “chọn màu an toàn hơn”                                                                        |
| Chỉ thấy một bóng tối, không thấy đủ phần còn lại của vỏ | Khoanh nếu biên vỏ rõ                             | `state=unknown`, không kết luận toàn đầu đèn `off`                                                                                                  |
| Phản chiếu trên kính/mặt đường                           | Không vẽ hình phản chiếu                          | Đầu đèn thật vẫn được xét độc lập nếu nhìn thấy                                                                                                     |

Phóng to ảnh gốc giúp kiểm tra pixel, không tạo thêm bằng chứng. Khi hai cách đọc vẫn hợp lý sau khi quan sát, chuyển review; không dùng màu của xe khác, chuyển động của xe khác hay suy đoán chu kỳ đèn để chọn nhãn.

## 7. Ambiguity / escalation

### Decision và cách thể hiện trong CVAT

| Decision               | Khi nào dùng                                                                                 | Dấu hiệu trong export                                                                       |
| ---------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **LABEL**              | Nhận diện được đầu đèn và đặt khung theo rule                                                | Box `traffic_light` với đủ `signal_type`, `state`, `ego_relevance`, `needs_review`          |
| **IGNORE**             | Chắc chắn ngoài scope theo mục 5                                                             | Không có annotation tại đối tượng đó; reviewer đối chiếu ảnh với các box trong export       |
| **UNKNOWN**            | Đối tượng có thể vẽ nhưng một hoặc nhiều thuộc tính thiếu bằng chứng                         | Thuộc tính tương ứng là `unknown`, `needs_review=true`; các thuộc tính chắc chắn giữ nguyên |
| **ESCALATE đối tượng** | Thuộc tính còn `unknown`, bằng chứng mâu thuẫn hoặc có tình huống schema chưa biểu diễn được | `needs_review=true` trên box; có thể giữ các giá trị chắc chắn                              |

### Quy trình đối chiếu annotation

Reviewer đối chiếu từng ảnh theo cùng thứ tự để lỗi không bị gộp thành một nhận xét chung:

1. **Đếm instance:** rà toàn ảnh theo thứ tự trái sang phải, trên xuống dưới; ghi nhận đầu đèn bị bỏ sót, box thừa hoặc box trùng. Một instance là một vỏ vật lý theo mục 2.
2. **Ghép box với vỏ:** đối chiếu từng box với đúng vỏ đèn trong ảnh; kiểm tra box ôm phần vỏ nhìn thấy theo mục 3. Không xem thuộc tính đúng là bù cho box sai, thiếu hoặc trùng.
3. **Đối chiếu thuộc tính riêng:** với từng box đã ghép, kiểm `signal_type`, `state`, rồi `ego_relevance` theo mục 4. Thuộc tính còn `__undefined__` ghi là lỗi của thuộc tính đó (quên gán), không coi là `unknown`. Chỉ đánh giá mỗi thuộc tính bằng bằng chứng ảnh và hướng giả định đã ghi cho task.
4. **Kiểm tra review:** nếu một thuộc tính là `unknown` hoặc đối tượng cần QA xử lý, xác nhận `needs_review=true`. Nếu còn vấn đề không gắn được với box, tìm issue CVAT tương ứng.

Ghi từng sai khác theo ảnh và loại lỗi: **missing/extra/duplicate instance**, **geometry**, **signal_type**, **state**, **ego_relevance** hoặc **review flag**. Không gộp nhiều sai khác thành một lỗi duy nhất; nếu phân vân giữa hai giá trị, ghi bằng chứng quan sát được và áp dụng quy tắc UNKNOWN/ESCALATE ở trên. Cách phân loại này giúp so sánh annotator theo từng phần của bài toán và chỉ ra khâu cần hiệu chỉnh.

Nếu không đặt được box hoặc có vấn đề toàn ảnh, ghi issue CVAT kèm tên ảnh, vị trí gần đúng trong ảnh, điều quan sát được và câu hỏi cụ thể cho QA; giữ các box hợp lệ khác. QA ghi kết luận và cách xử lý ngay trong issue trước khi đóng issue, để người khác có thể lần theo lý do. Issue là thông tin review bổ sung, không phải annotation trong export. Không đưa riêng một issue không có box vào gold như decision ESCALATE được chấm từ export; decision ESCALATE chấm được phải có `needs_review=true` trên đối tượng.

UNKNOWN và ESCALATE có thể cùng xuất hiện: một cái ghi trạng thái thông tin, một cái yêu cầu review. `not_relevant` không đồng nghĩa IGNORE. `unknown` không được dùng thay cho việc chưa đọc ảnh.

### Người xử lý và bằng chứng

1. Annotator lưu annotation và tạo issue trong CVAT tại đối tượng/vùng liên quan: tên ảnh, vị trí, thuộc tính chưa rõ, bằng chứng đã thấy và câu hỏi cần giải quyết. Không chỉ báo bằng miệng. Với đối tượng đã vẽ, issue bổ sung lý do và không thay thế `needs_review=true` trong export. Với vùng không đặt được box, issue được QA kiểm tra riêng.
2. Người phụ trách QA đọc issue và ảnh, kiểm tra theo guideline. Nếu bằng chứng đủ, sửa thuộc tính, ghi lý do và tắt `needs_review` khi đã giải quyết hết vấn đề trên đối tượng.
3. Nếu thiếu quy tắc, QA chuyển người phụ trách guideline quyết định và ghi thành rule có thể dùng lại. Trong blind test, owner không giải thích domain rule cho peer; peer giữ dấu review và câu hỏi được ghi vào clarification log.
4. Nếu dữ liệu vẫn không đủ bằng chứng, giữ `unknown` cùng `needs_review=true`, kể cả khi issue đã được kết luận là không thể xác định. Đóng issue cấp ảnh khi đã ghi kết luận xử lý; không tạo box để thay thế một dấu cấp ảnh. Không ép consensus bằng cách tự chọn đỏ, xanh hoặc hướng đi.

## 8. Temporal rule

**Không áp dụng — task ảnh tĩnh.** Mỗi ảnh là một lần quan sát độc lập, dùng Shape; không có track ID, nội suy, thời điểm bắt đầu/kết thúc track hoặc attribute mutable.

Không lấy trạng thái ở ảnh trước/sau để điền cho ảnh hiện tại. Không suy ra đèn nhấp nháy, hỏng hoặc sắp đổi màu từ một ảnh. Nếu tại ảnh hiện tại không thấy bóng sáng, chỉ chọn `off` khi đáp ứng đầy đủ điều kiện mục 4; nếu không thì `unknown`. Không mặc định các frame liên tiếp phải có cùng nhãn.

## 9. Examples

Các tình huống dưới đây minh họa cách áp dụng rule; **chưa phải gold của một ảnh cụ thể**. Nhóm sẽ bổ sung ảnh/crop, mã `sample_id` và split sau khi chốt `sample_pack.csv`. Chỉ dùng ảnh `example` hoặc `calibration`, không dùng ảnh blind. Trước khi bàn giao, mỗi ví dụ giữ lại phải được đối chiếu với ảnh thật; tình huống không có trong dữ liệu phải bỏ hoặc ghi rõ là tình huống giả định.

Ảnh minh họa cần kèm toàn cảnh để thấy quan hệ làn–đèn và crop để đọc tín hiệu. Việc crop chỉ phục vụ minh họa; tọa độ annotation và dung sai vẫn tính trên ảnh gốc. Chưa gán `sample_id` trong bảng để tránh vô tình lấy một ảnh blind làm ví dụ.

| sample_id                         | Thấy gì                                                                                                                                         | Expected output                                                                                                 | Rule áp dụng                       |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ---------------------------------- |
| Chờ ảnh example/calibration — E01 | Đầu đèn tròn đỏ, biên vỏ rõ; hướng giả định đi thẳng; đủ bằng chứng đèn điều khiển đúng làn ego                                                 | Một box: `signal_type=circular`, `state=red`, `ego_relevance=relevant`, `needs_review=false`                    | 2, 3, 4                            |
| Chờ ảnh example/calibration — E02 | Mũi tên trái đỏ và đèn tròn xanh nằm ở hai vỏ riêng; hướng giả định rẽ trái; xác lập được mũi tên cho làn rẽ ego, đèn tròn chỉ cho làn đi thẳng | Hai box: mũi tên `left_arrow/red/relevant`; đèn tròn `circular/green/not_relevant`; cả hai `needs_review=false` | 2, 4; không gán xanh chung cho ego |
| Chờ ảnh example/calibration — E03 | Bố trí như E02, nhưng hướng giả định đi thẳng và đủ bằng chứng quan hệ làn                                                                      | Mũi tên `left_arrow/red/not_relevant`; đèn tròn `circular/green/relevant`; vẫn giữ màu đỏ của đèn không áp dụng | 4, 5                               |

## 10. Common mistakes

| Lỗi thường gặp                                                                                                           | Cách tránh / kiểm tra                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| Tự cho ego đi thẳng vì không thấy mũi tên trên đường                                                                     | Đọc hướng giả định trong task; thiếu thì escalate, không tạo hướng mới                                                                |
| Chọn đèn gần nhất, ở giữa ảnh hoặc đang xanh là relevant                                                                 | Xác lập quan hệ làn–hướng–đèn theo mục 4; thiếu bằng chứng dùng `unknown`                                                             |
| Thấy mũi tên trái ở bất kỳ vị trí nào là gán cho ego rẽ trái                                                             | Kiểm tra nó điều khiển đúng làn/luồng của ego trước                                                                                   |
| Gán màu xanh của đèn tròn cho vỏ mũi tên trái đang đỏ                                                                    | Mỗi vỏ là một instance; đọc loại và màu riêng trước khi gán relevance                                                                 |
| Bỏ đèn không áp dụng hoặc không ghi màu của nó                                                                           | Đèn đó vẫn trong scope; giữ đủ attribute và dùng `not_relevant` khi có bằng chứng                                                     |
| Gộp hai vỏ sát nhau hoặc vẽ từng bóng trong cùng vỏ                                                                      | Một box cho một vỏ, theo mục 2                                                                                                        |
| Box chỉ ôm bóng sáng hoặc bao cả quầng sáng/cột                                                                          | Kiểm lại biên vỏ và dung sai 2 pixel ở ảnh gốc                                                                                        |
| Đèn mờ hoặc bị che được gán `off`                                                                                        | Chỉ dùng `off` khi nhìn đủ vỏ và xác nhận không bóng nào sáng                                                                         |
| Phân vân màu thì chọn đỏ để thận trọng                                                                                   | Chọn `state=unknown`, `needs_review=true`; không tạo nhãn màu thiếu bằng chứng                                                        |
| Dùng `red_yellow` khi chỉ phân vân một bóng là đỏ hay vàng                                                               | `red_yellow` chỉ khi thấy rõ hai bóng đỏ và vàng cùng sáng; phân vân thì `unknown`                                                    |
| Bỏ sót dropdown (export còn `__undefined__`), chọn `unknown` mà chưa đọc ảnh, hoặc chọn `unknown` nhưng không bật review | Chọn đủ ba dropdown cho mọi box; còn thiếu bằng chứng sau khi đọc thì chọn `unknown` và bật `needs_review`                            |
| Dùng ảnh trước/sau để điền màu hoặc đoán phần vỏ bị che                                                                  | Mỗi ảnh độc lập; chỉ dùng bằng chứng của ảnh hiện tại                                                                                 |
| Box cần review nhưng chỉ ghi issue hoặc nhắn miệng                                                                       | Lưu `needs_review=true` trên box; issue không có box được kiểm tra riêng, không coi là decision ESCALATE trong export                 |
| Bỏ đèn đi bộ hoặc gán nó điều khiển ego vì đang sáng xanh/trắng                                                          | Vẫn vẽ với `signal_type=pedestrian`, `ego_relevance=not_relevant`; `state` theo ý nghĩa ký hiệu (người đi → `green`, bàn tay → `red`) |
| Gán mũi tên thẳng là đèn tròn, hoặc đoán hướng mũi tên theo vị trí vỏ                                                    | Đọc hình ký hiệu, chọn `straight_arrow/left_arrow/right_arrow`; hình không rõ thì `unknown`                                           |

### Checklist trước khi Save / export

- [ ] Đã đọc hướng giả định; không tự suy ra ý định thực tế của xe.
- [ ] Đã quét toàn ảnh; mỗi đầu đèn nhận diện và đặt khung được có đúng một box.
- [ ] Mỗi box thuộc `traffic_light`, đúng phần vỏ nhìn thấy và dung sai; không vẽ đèn xe/biển báo.
- [ ] Đã chọn cả ba dropdown của từng box: không box nào còn `__undefined__`, không chọn `unknown` thay cho việc đọc ảnh, không dùng tên/giá trị ngoài mục 4.
- [ ] Loại và màu được ghi độc lập với `ego_relevance`, kể cả đầu đèn không áp dụng.
- [ ] Mọi `relevant`/`not_relevant` đều có bằng chứng quan hệ; không ép số lượng đèn relevant.
- [ ] Có ít nhất một attribute `unknown` thì `needs_review=true`; vấn đề không gắn được với box đã ghi issue CVAT.
- [ ] Issue đã chỉ rõ ảnh/vị trí cần kiểm tra; box cần review có checkbox tương ứng trong export.
- [ ] Đã Save trước khi xuất **CVAT for images 1.1**, không kèm ảnh; kiểm bản export có các box và attribute đã lưu.
