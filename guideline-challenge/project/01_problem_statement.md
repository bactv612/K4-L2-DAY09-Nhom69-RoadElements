# Problem statement + downstream contract

## Bài toán

Phát hiện vị trí các biển báo giao thông cố định trong ảnh đường, với trọng tâm là biển nhỏ, ở xa, bị che một phần hoặc khó đọc. Nhóm chỉ phân biệt biển có thuộc phạm vi hay không, không phân loại ý nghĩa từng biển.

## Downstream contract

1. **Downstream task / model / user:** Module perception hỗ trợ cảnh báo người lái cần biết vị trí biển để đưa vùng đó vào bước xử lý tiếp theo.
2. **Output annotation:** Một bounding box cho mỗi mặt biển thuộc phạm vi; class `traffic_sign`; các attribute `visibility`, `truncated`, `facing` và cờ rà soát. Mỗi ảnh có một tag `image_status` ghi thời tiết và điều kiện sáng. Ảnh không có biển trong phạm vi có tag này nhưng không có box `traffic_sign`.
3. **Failure có hậu quả lớn nhất:** Bỏ sót một biển nhìn thấy rõ hoặc gán nhầm vật ngoài phạm vi thành biển, làm giảm độ tin cậy của bộ phát hiện. Biển thuộc hướng di chuyển của xe bị bỏ sót cần được ưu tiên xử lý.
4. **Escalation:** Người gán nhãn đánh dấu đối tượng hoặc ảnh cần rà soát trong CVAT, rồi ghi sample, vị trí, câu hỏi và bằng chứng vào log. Owner của guideline chốt rule; nếu nhóm không đủ bằng chứng thì ghi lại câu hỏi để Lab Coach xử lý.

## Scope

- **Trong scope:** Biển báo giao thông cố định nhìn thấy trong ảnh đường, gồm biển cấm, hiệu lệnh, cảnh báo và chỉ dẫn. Biển không cần quay mặt về phía xe mới được gán nhãn.
- **Ngoài scope:** Đèn giao thông, vạch kẻ đường, biển quảng cáo, bảng chỉ có tên đường, biển gắn trên xe hoặc rơ-moóc, mặt sau không có thông tin và ảnh phản chiếu.
- **Geometry tolerance:** Rectangle ôm mép ngoài phần mặt biển nhìn thấy. Lệch tối đa 2 px mỗi cạnh được chấp nhận. Bỏ qua biển có cạnh dài dưới 8 px.

## Output chấm được

- `LABEL`: label `traffic_sign` cùng bounding box và attribute.
- `IGNORE`: không tạo `traffic_sign`. Với object nằm trong blind set mà nhóm muốn chấm quyết định bỏ qua, dùng label rà soát `review_candidate` với `decision=IGNORE`.
- `UNKNOWN` hoặc `ESCALATE`: dùng `review_candidate` tại vị trí object, chọn decision tương ứng và ghi lý do. Nếu toàn ảnh không thể đánh giá, gắn tag `image_escalate`.
- Ảnh không có biển trong phạm vi: không tạo box `traffic_sign`; vẫn gắn tag `image_status` sau khi quét hết ảnh.

Các giá trị này phải được xuất trong CVAT. Label rà soát không thuộc đầu ra huấn luyện của detector và phải được lọc khỏi tập `traffic_sign`.

## Dữ liệu và giới hạn

Sample pack có 9 ảnh GTSDB: 3 ảnh `example` (`GTS04`, `GTS07`, `GTS12`), 1 ảnh `calibration` (`GTS01`) và 5 ảnh `blind` (`GTS02`, `GTS08`, `GTS14`, `GTS18`, `GTS25`). README khuyến nghị 5–8 ảnh calibration. GTSDB chứa biển của Đức nên nhóm không suy luận ý nghĩa pháp lý từ nội dung chữ. Bộ ảnh này chưa đại diện đầy đủ cho điều kiện đường tại Việt Nam.
