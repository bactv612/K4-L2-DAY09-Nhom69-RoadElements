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

- **Phiên bản CVAT:** Đã kiểm tra trước calibration; số phiên bản cụ thể không được lưu trong báo cáo.
- **Tên task và ID calibration:** Đã tạo task calibration; tên và ID cụ thể không được lưu trong báo cáo.
- **Đã dán Guide vào task:** Đã dán toàn bộ `02_guideline.md` vào Guide của task.
- **Shape hay Track:** Đã chọn Shape vì task dùng ảnh tĩnh, không có nhãn theo thời gian.

## Kiểm tra thiết lập

Chưa thực hiện. Sau khi tạo task calibration, nhờ một thành viên không tham gia thiết lập tự mở task và xác định bốn label, geometry cho `traffic_sign` và `review_candidate`, các attribute bắt buộc, cùng điều kiện dùng `image_escalate`. Ghi tên người thử và điểm họ còn phân vân tại đây.
