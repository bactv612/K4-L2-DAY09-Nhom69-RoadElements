# Revision log

`02_guideline.md` hiện phản ánh tài liệu nguồn `guideline_v2.docx` do nhóm cung cấp. Việc cập nhật theo bản nguồn không chứng minh rằng calibration đã diễn ra; nhóm cần bổ sung bằng chứng calibration trước khi coi đây là bản đã được hiệu chuẩn. v3 ghi các thay đổi sau blind handoff. Mỗi lần đổi version trong `02_guideline.md`, thêm bằng chứng vào bảng dưới đây.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Chuyển bản nháp Word `02_guideline.md.docx` vào guideline Markdown; thống nhất một class `traffic_sign`, box phần nhìn thấy, ngưỡng 8 px, tolerance 2 px và cách ghi case review trong CVAT. | Tạo bản v1 cho task calibration; phân biệt `IGNORE` với trường hợp thiếu bằng chứng. | Bản nháp Word do nhóm cung cấp; đối chiếu sample ID với `data/catalog.csv`; chưa có bằng chứng calibration. |
| v2 | Theo cập nhật nhóm xác nhận: xóa attribute `has_signs` khỏi `image_status` và bổ sung các ảnh tình huống trong mục escalation. | Đồng bộ báo cáo Markdown với hai thay đổi của bản Word v2. | Đối chiếu `guideline_v1.docx` và `guideline_v2.docx`; giữ các rule/default không đổi. Ảnh `GTS092` có trong tài liệu nguồn nhưng ID không có trong `data/catalog.csv`; calibration nội bộ chưa được cung cấp/xác nhận. |
