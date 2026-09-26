# Thư viện edge case

> **Trạng thái:** 3 card nháp. Cần bổ sung ít nhất 5 card trước khi freeze, trong đó có một case critical và ít nhất một case escalation. Xác nhận quyết định và vị trí object trong calibration.

## Tổng quan

| Case | Sample | Tình huống | Quyết định |
|---|---|---|---|
| EC-01 | `GTS04` | Hai biển độc lập cùng gắn trên một cột | `LABEL` |
| EC-02 | `GTS07` | Biển nhỏ hơn ngưỡng 8 px; không có biển khác trong scope | `IGNORE` |
| EC-03 | `GTS12` | Vật mờ/lóa, chưa xác định được có phải biển hay không | `ESCALATE` |

## Các case

### EC-01 · Hai biển trên cùng một cột

**Sample:** `GTS04` · **Quyết định:** `LABEL`

**Tags:** `edge`, `multiple_instances`

- **Quan sát:** Hai biển có mặt thông tin riêng nhưng dùng chung giá đỡ.
- **Cách gán:** Tạo hai rectangle `traffic_sign`, mỗi box ôm một mặt biển. Không gộp hai biển hoặc bao cả cột.
- **Lý do:** Mỗi mặt biển là một instance riêng; gộp box làm sai số lượng và vị trí object.
- **Lỗi thường gặp:** Vẽ một box bao cả cột và hai biển.

### EC-02 · Biển ở xa dưới ngưỡng kích thước

**Sample:** `GTS07` · **Quyết định:** `IGNORE`

**Tags:** `negative`, `small_far`

- **Quan sát:** Biển xa có cạnh dài dưới 8 px; ảnh không có biển nào khác thuộc scope.
- **Cách gán:** Không tạo rectangle `traffic_sign`. Tạo đúng một tag `image_status` để ghi thời tiết và điều kiện sáng.
- **Lý do:** Guideline loại biển có cạnh dài dưới 8 px. Ảnh âm được biểu diễn bằng việc không có box `traffic_sign`; tag cho biết ảnh đã được rà soát.
- **Lỗi thường gặp:** Vẽ box cho biển dưới ngưỡng hoặc bỏ quên tag ảnh vì không có box.

### EC-03 · Vật mờ hoặc lóa chưa xác định

**Sample:** `GTS12` · **Quyết định:** `ESCALATE`

**Tags:** `ambiguity`, `low_visibility`, `escalation`

- **Quan sát:** Chưa đủ bằng chứng để xác nhận vật đó là biển giao thông.
- **Cách gán:** Không đoán nhãn `traffic_sign`. Với case cần chấm, dùng `review_candidate` khoanh vùng bằng chứng nhìn thấy, đặt `decision=ESCALATE`, rồi ghi vị trí và câu hỏi vào log.
- **Lý do:** Escalation phải phân biệt được với nhãn âm và với biển đã xác nhận.
- **Lỗi thường gặp:** Gán `traffic_sign` chỉ dựa vào màu sắc hoặc bỏ qua case mà không ghi lại.

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

## Điểm cần chốt trong calibration

- Ví dụ `GTS04` trong guideline yêu cầu `LABEL` hai biển trên cùng cột, còn ảnh escalation `GTS04` mô tả bảng tên đường cần `IGNORE`. Xác nhận đây là hai object khác nhau và ghi rõ vị trí.
- Ví dụ `GTS07` mô tả ảnh âm dưới ngưỡng 8 px, còn ảnh escalation `GTS07` ghi `LABEL` khi box đạt ngưỡng. Xác nhận object cụ thể và kích thước trước khi chốt gold.
- Chỉ đưa `GTS092` vào dữ liệu dự án sau khi xác minh ảnh và sample ID tồn tại trong catalog.
