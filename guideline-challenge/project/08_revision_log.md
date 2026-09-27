# Revision log

v1 và v2 dựa trên tài liệu Word của nhóm. v3 ghi các thay đổi sau khi Nhomsiunhan dùng blind-pack v2. Gold và sample pack giữ nguyên sau freeze; gói đã gửi vẫn dùng guideline v2.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Chuyển bản nháp Word `02_guideline.md.docx` vào guideline Markdown; thống nhất một class `traffic_sign`, box phần nhìn thấy, ngưỡng 8 px, tolerance 2 px và cách ghi case review trong CVAT. | Tạo bản v1 cho task calibration; phân biệt `IGNORE` với trường hợp thiếu bằng chứng. | `02_guideline.md.docx`; đối chiếu sample ID với `data/catalog.csv`. |
| v2 | Xóa attribute `has_signs` khỏi `image_status` và bổ sung các ảnh tình huống trong mục escalation. | Đồng bộ báo cáo Markdown với bản Word v2. | Đối chiếu `guideline_v1.docx` và `guideline_v2.docx`; giữ các rule/default không đổi. `GTS092` có trong tài liệu Word nhưng không có trong `data/catalog.csv`. |
| v3 | Nhấn mạnh đèn tín hiệu ở xa vẫn ngoài scope; `facing=irrelevant` không thay thế quyết định IGNORE. Thêm bước kiểm đúng một tag `image_status` trên mọi ảnh, kiểm mép box tối đa 2 px và ghi log cho `needs_review=true`. Sửa mô tả split của `GTS01`. | Peer gán bốn đèn tín hiệu GTS02 thành biển, bỏ tag `image_status` ở cả năm ảnh và vẽ box GTS14 lệch mép. Các lỗi này cần được kiểm ngay trước export. | `07_blind_handoff/peer_output/annotations.xml`; `transfer_score.csv` dòng GTS02/d2 và GTS14/d2; `peer_feedback.md`; `sample_pack.csv`. Geometry được đánh giá qua ảnh phóng. |
