# Thư viện edge case

8 card. EC-01–03 được soạn trước blind test; EC-04–08 ghi các tình huống từ export của peer.

## Tổng quan

| Case | Sample | Tình huống | Quyết định |
|---|---|---|---|
| EC-01 | `GTS04` | Hai biển độc lập cùng gắn trên một cột | `LABEL` |
| EC-02 | `GTS07` | Biển nhỏ hơn ngưỡng 8 px; không có biển khác trong scope | `IGNORE` |
| EC-03 | `GTS12` | Vật mờ/lóa, chưa xác định được có phải biển hay không | `ESCALATE` |
| EC-04 | `GTS02` | Biển báo cạnh đèn tín hiệu, kể cả cụm đèn ở xa | `LABEL` biển; `IGNORE` đèn |
| EC-05 | `GTS08` | Biển nhỏ, mờ dưới cầu và biển chỉ dẫn ở xa | `LABEL` khi còn nhận ra; `UNKNOWN` nếu thiếu bằng chứng |
| EC-06 | `GTS14` | Biển tam giác sát đèn tín hiệu, cần box sát mép | `LABEL` biển; `IGNORE` đèn |
| EC-07 | `GTS25` | Hai biển rõ và hai object nền cần owner review | `LABEL` biển rõ; `ESCALATE` object chưa chốt |
| EC-08 | `GTS04` | Hai tài liệu dùng cùng sample ID cho quyết định khác nhau | `ESCALATE` mâu thuẫn nguồn |

## Các case

### EC-01 · Hai biển trên cùng một cột

**Case ID:** EC-01

**Sample:** `GTS04` · **Quyết định:** `LABEL`

**Tags:** `edge`, `multiple_instances`

- **Quan sát:** Hai biển có mặt thông tin riêng nhưng dùng chung giá đỡ.
- **Cách gán:** Tạo hai rectangle `traffic_sign`, mỗi box ôm một mặt biển. Không gộp hai biển hoặc bao cả cột.
- **Lý do:** Mỗi mặt biển là một instance riêng; gộp box làm sai số lượng và vị trí object.
- **Lỗi thường gặp:** Vẽ một box bao cả cột và hai biển.

### EC-02 · Biển ở xa dưới ngưỡng kích thước

**Case ID:** EC-02

**Sample:** `GTS07` · **Quyết định:** `IGNORE`

**Tags:** `negative`, `small_far`

- **Quan sát:** Biển xa có cạnh dài dưới 8 px; ảnh không có biển nào khác thuộc scope.
- **Cách gán:** Không tạo rectangle `traffic_sign`. Tạo đúng một tag `image_status` để ghi thời tiết và điều kiện sáng.
- **Lý do:** Guideline loại biển có cạnh dài dưới 8 px. Ảnh âm được biểu diễn bằng việc không có box `traffic_sign`; tag cho biết ảnh đã được rà soát.
- **Lỗi thường gặp:** Vẽ box cho biển dưới ngưỡng hoặc bỏ quên tag ảnh vì không có box.

### EC-03 · Vật mờ hoặc lóa chưa xác định

**Case ID:** EC-03

**Sample:** `GTS12` · **Quyết định:** `ESCALATE`

**Tags:** `ambiguity`, `low_visibility`, `escalation`

- **Quan sát:** Chưa đủ bằng chứng để xác nhận vật đó là biển giao thông.
- **Cách gán:** Không đoán nhãn `traffic_sign`. Với case cần chấm, dùng `review_candidate` khoanh vùng bằng chứng nhìn thấy, đặt `decision=ESCALATE`, rồi ghi vị trí và câu hỏi vào log.
- **Lý do:** Escalation phải phân biệt được với nhãn âm và với biển đã xác nhận.
- **Lỗi thường gặp:** Gán `traffic_sign` chỉ dựa vào màu sắc hoặc bỏ qua case mà không ghi lại.

### EC-04 · Biển báo và đèn tín hiệu cùng giao lộ

**Case ID:** EC-04

**Sample:** `GTS02` · **Quyết định:** `LABEL` các biển; `IGNORE` đèn tín hiệu

**Tags:** `critical`, `scope`, `conflict`

- **Quan sát:** Hai biển nhường đường và hai biển mũi tên màu xanh nằm ở hai góc trên. Có các cụm đèn tín hiệu gần biển và ở phía xa dưới cầu.
- **Cách gán:** Vẽ riêng bốn mặt biển mục tiêu. Không vẽ `traffic_sign` cho bất kỳ đầu đèn tín hiệu nào; `facing=irrelevant` không biến đèn thành biển.
- **Lý do:** Gold GTS02/d2 yêu cầu bỏ qua đèn. Export của peer đã gán bốn đầu đèn xa thành `traffic_sign`.
- **Lỗi thường gặp:** Nhìn nhanh các cụm đèn nhỏ thành biển chữ nhật rồi gán `facing=irrelevant`.

### EC-05 · Biển nhận diện được trong ảnh tối dưới cầu

**Case ID:** EC-05

**Sample:** `GTS08` · **Quyết định:** `LABEL` biển nhận diện được; `UNKNOWN` với vật chưa đủ bằng chứng

**Tags:** `low_visibility`, `small_far`, `ambiguity`

- **Quan sát:** Hai cặp biển tròn đứng ở hai bên đường. Trong nền còn có biển chỉ dẫn màu xanh ở xa; ánh sáng dưới cầu yếu.
- **Cách gán:** Mỗi mặt biển còn nhận diện được và có cạnh dài từ 8 px là một `traffic_sign`. Đặt `visibility=blurry` nếu mặt biển mờ; không cần đọc chữ trên biển chỉ dẫn. Nếu không xác định được vật là biển, dùng `review_candidate` với `decision=UNKNOWN`.
- **Lý do:** Quy tắc scope dựa trên việc nhận ra biển giao thông, không dựa trên khả năng đọc chữ tiếng Đức hay độ sáng của cảnh.
- **Lỗi thường gặp:** Bỏ sót biển ở xa hoặc gán cả cầu/cột vào box.

### EC-06 · Biển cảnh báo tam giác cạnh đèn

**Case ID:** EC-06

**Sample:** `GTS14` · **Quyết định:** `LABEL` biển tam giác; `IGNORE` đèn tín hiệu

**Tags:** `critical`, `geometry`, `scope`

- **Quan sát:** Biển cảnh báo giao lộ hình tam giác bên phải nằm gần đèn tín hiệu. Box của peer bao thêm khoảng 3–4 px nền ở mép trái theo kiểm tra ảnh phóng.
- **Cách gán:** Vẽ một box ôm mặt tam giác, không ôm cột hoặc đèn. Kiểm từng cạnh theo dung sai 2 px trước khi export.
- **Lý do:** Biển cảnh báo là quyết định critical; geometry được chấm ở GTS14/d2 độc lập với việc đã nhận ra biển.
- **Lỗi thường gặp:** Coi phát hiện đúng là đủ rồi bỏ qua mép box, hoặc gán đèn thành biển.

### EC-07 · Biển rõ và object nền chưa chốt

**Case ID:** EC-07

**Sample:** `GTS25` · **Quyết định:** `LABEL` hai biển rõ; `ESCALATE` object còn nghi vấn

**Tags:** `multiple_instances`, `ambiguity`, `escalation`

- **Quan sát:** Biển ưu tiên hình thoi và biển giới hạn tốc độ 50 nằm trên cùng cột. Peer còn vẽ hai box ở nền và đặt `needs_review=true`.
- **Cách gán:** Giữ hai box riêng cho hai biển rõ. Với object nền đã xác nhận là biển nhưng chưa rõ `facing`, giữ `traffic_sign`, đặt `needs_review=true` và ghi vị trí/câu hỏi vào log. Nếu chưa chắc đó là biển, dùng `review_candidate` với `decision=UNKNOWN` thay vì đoán.
- **Lý do:** Cờ review không tự giải quyết câu hỏi; owner cần phân xử hai box nền và xem lại phạm vi của frozen gold.
- **Lỗi thường gặp:** Gán nhãn nghi vấn rồi để `needs_review=true` mà không có câu hỏi hoặc người xử lý.

### EC-08 · Cùng sample ID nhưng hai mô tả khác nhau

**Case ID:** EC-08

**Sample:** `GTS04` · **Quyết định:** `ESCALATE` mâu thuẫn tài liệu trước khi dùng làm gold

**Tags:** `data_ambiguity`, `escalation`

- **Quan sát:** Ví dụ GTS04 trong guideline mô tả hai biển độc lập cần `LABEL`; ảnh tình huống từ Word v2 gắn cùng ID GTS04 nhưng mô tả bảng chỉ tên đường cần `IGNORE`.
- **Cách gán:** Đối chiếu ảnh gốc và vị trí từng object. Nếu hai mô tả nói về hai object khác nhau, ghi rõ tọa độ/vùng ảnh cho mỗi quyết định. Nếu không khớp cùng ảnh hoặc không rõ nguồn, tạm ngừng dùng ID này làm gold và hỏi owner.
- **Lý do:** Sample ID đơn lẻ không định danh được object; trộn hai quyết định trái nhau sẽ làm annotator và người chấm hiểu khác.
- **Lỗi thường gặp:** Chọn một trong hai câu trả lời chỉ vì thấy cùng `GTS04` mà không kiểm ảnh và vị trí.

## Ảnh tình huống lấy từ guideline v2

Các ảnh dưới đây được chép từ mục escalation của tài liệu Word. Chúng minh họa các tình huống nguồn, không tự động xác nhận sample ID có trong dataset của dự án.

### `GTS04` · Bảng chỉ tên đường — `IGNORE`

Nguồn mô tả bảng này là tên đường thuần chữ, ngoài scope.

![Ảnh GTS04: bảng chỉ tên đường được đánh dấu](../assets/guideline-v2/edge-case-1.png)

### `GTS07` · Biển nhỏ nhưng còn nhận diện được — `LABEL`

Nguồn ghi kích thước box đủ lớn để gán nhãn.

![Ảnh GTS07: biển nhỏ được đánh dấu](../assets/guideline-v2/edge-case-2.png)

### Không ghi sample ID · Hai biển nhỏ — `IGNORE`

Nguồn ghi không đủ thông tin để quyết định.

![Ảnh tình huống hai biển nhỏ không có sample ID](../assets/guideline-v2/edge-case-3.png)

### `GTS092` · Biển ở hai nhánh đường — `ESCALATE`

Nguồn yêu cầu phân xử `facing`. `GTS092` hiện không có trong `data/catalog.csv`; cần xác minh ID trước khi đưa case này vào sample pack.

![Ảnh GTS092: biển báo ở nhánh đường](../assets/guideline-v2/edge-case-4.png)

![Ảnh GTS092: hai biển trên các nhánh đường](../assets/guideline-v2/edge-case-5.png)

## Mâu thuẫn trong tài liệu nguồn

- Ví dụ `GTS04` trong guideline yêu cầu `LABEL` hai biển trên cùng cột, còn ảnh escalation `GTS04` mô tả bảng tên đường cần `IGNORE`. Xác nhận đây là hai object khác nhau và ghi rõ vị trí.
- Ví dụ `GTS07` mô tả ảnh âm dưới ngưỡng 8 px, còn ảnh escalation `GTS07` ghi `LABEL` khi box đạt ngưỡng. Xác nhận object cụ thể và kích thước trước khi chốt gold.
- Chỉ đưa `GTS092` vào dữ liệu dự án sau khi xác minh ảnh và sample ID tồn tại trong catalog. Giữ nguyên gold đã freeze trong lúc đối chiếu.
