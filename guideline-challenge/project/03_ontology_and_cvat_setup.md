# Ontology và thiết lập CVAT

Bảng dưới đây định nghĩa label và attribute trong `03_cvat_labels.json`. Giữ hai file khớp với `02_guideline.md`.

## Bảng ontology

| Name | Geometry | Type | Giá trị cho phép | Default | Mutable? | Lý do |
|---|---|---|---|---|---|---|
| `traffic_sign` | Rectangle | Class | Một class cho mọi biển cố định thuộc phạm vi | N/A | Không | Task phát hiện vị trí, không phân loại ý nghĩa biển. |
| `visibility` | N/A | Attribute của `traffic_sign` | `visible`, `blurry` | `visible` | Không | Ghi nhận mặt biển có mờ nhưng vẫn nhận ra được hay không. |
| `truncated` | N/A | Attribute của `traffic_sign` | `false`, `true` | `false` | Không | Ghi nhận biển bị mép ảnh cắt mất một phần. |
| `facing` | N/A | Attribute của `traffic_sign` | `relevant`, `irrelevant` | `relevant` | Không | Ghi nhận biển có phục vụ hướng đi nhìn thấy của xe hay không. |
| `needs_review` | N/A | Attribute của `traffic_sign` | `false`, `true` | `false` | Không | Đánh dấu biển đã xác nhận nhưng còn câu hỏi cần owner xử lý. |
| `occluded` | Thuộc tính của rectangle | Thuộc tính có sẵn trong CVAT | `false`, `true` | `false` | Không | Dùng cờ che khuất có sẵn. Không tạo custom attribute trùng tên. |
| `review_candidate` | Rectangle | Class chỉ dùng để review | Có attribute `decision` và `reason` | N/A | Không | Làm cho quyết định `IGNORE`, `UNKNOWN` và `ESCALATE` hiện trong export. Lọc label này khỏi dữ liệu huấn luyện detector. |
| `decision` | N/A | Attribute của `review_candidate` | `IGNORE`, `UNKNOWN`, `ESCALATE` | `__undefined__` | Không | Ghi quyết định cho object được review. |
| `reason` | N/A | Attribute của `review_candidate` | `out_of_scope`, `too_small`, `not_sure_sign`, `image_quality`, `ego_route_unclear`, `other` | `__undefined__` | Không | Ghi lý do review hoặc bỏ qua candidate. |
| `image_status` | Tag | Label cấp ảnh | Attribute `weather`, `brightness` | `sunny`, `day` | Không | Ghi điều kiện ảnh đã được rà soát. Nếu không có box `traffic_sign` thì ảnh không có biển thuộc phạm vi. |
| `image_escalate` | Tag | Label cấp ảnh | Không có attribute | N/A | Không | Đánh dấu ảnh không thể phân xử ở cấp object. |

## Class hay attribute

Dùng một class `traffic_sign` vì mọi biển thuộc phạm vi có cùng geometry và mục tiêu phát hiện. Không mã hóa họ biển hoặc ý nghĩa biển thành class. Dùng attribute cho độ rõ, truncation, mức liên quan đến tuyến xe và trạng thái review. Chỉ dùng `review_candidate` cho object cần chấm hoặc chưa giải quyết. Đây không phải class của detector. Cờ `occluded` có sẵn trong CVAT là trường che khuất duy nhất.

Theo guideline, CVAT mặc định `visibility=visible`, `truncated=false`, `facing=relevant`, `needs_review=false`, `weather=sunny` và `brightness=day`. Annotator phải kiểm tra các giá trị mặc định trên từng object/ảnh và đổi khi bằng chứng cho thấy giá trị khác; không xem mặc định là kết quả đã xác minh. Đặt `needs_review=true` khi biển đã xác nhận nhưng còn câu hỏi cho owner. Mỗi ảnh đã rà soát có đúng một tag `image_status`; tag được yêu cầu kể cả khi ảnh không có box `traffic_sign`.

## CVAT

- **Phiên bản CVAT:** `v2.74.1`
- **Task calibration:** Đã tạo.
- **Đã dán Guide vào task:** Đã dán toàn bộ `02_guideline.md` vào Guide của task.
- **Shape hay Track:** Đã chọn Shape vì task dùng ảnh tĩnh, không có nhãn theo thời gian.

## Kiểm tra thiết lập

Export chấm chéo của Nhomsiunhan (CVAT job 24) chứa đủ bốn label trong ontology và các attribute của `traffic_sign`; điều này xác nhận schema đã vào task của peer. Cả năm ảnh peer xuất đều thiếu tag `image_status`, dù label này có trong schema. Cần kiểm tra tag trên từng ảnh trước khi nhận batch.

Export của nhóm ở gốc repo (`job_9_annotations_2026_09_26_04_47_52_cvat for images 1.1.zip`) là CVAT job 9, gồm 28 ảnh, 72 box `traffic_sign` và đúng một tag `image_status` trên mỗi ảnh. Schema trong ZIP chỉ có hai label `traffic_sign`, `image_status`. Nó còn `has_signs` đã xóa ở guideline v2, nhưng thiếu `needs_review`, `review_candidate` và `image_escalate` của `03_cvat_labels.json`. Cần cập nhật labels và Guide của task rồi xuất lại.
