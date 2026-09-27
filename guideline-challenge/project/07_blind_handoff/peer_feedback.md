# Peer feedback + owner response

## Peer submission

- **Nhóm peer:** Nhomsiunhan.
- **CVAT username trong export:** `nhomsieunhan`.
- **CVAT job:** 24, export định dạng CVAT for images 1.1.
- **Dữ liệu nhận được:** `annotations.xml`.
- **Đối chiếu:** export có đủ năm sample ID trong blind-pack đã freeze: GTS02, GTS08, GTS14, GTS18 và GTS25.

Thư mục `D:\VinUni\cvat_data\blind-pack` hiện chứa GTS13–GTS17, không trùng với năm sample ID trong export. Báo cáo này đối chiếu export với blind-pack đã gửi trong `guideline-challenge/handoff/blind-pack.zip` và frozen gold của dự án.

## 1. Câu hỏi debrief

| Câu hỏi | Trả lời |
|---|---|
| Rule nào rõ nhất / giúp quyết định nhanh nhất? |  |
| Rule nào mơ hồ hoặc phải tự suy diễn? |  |
| Sample nào khiến guideline “vỡ”? |  |
| Attribute / default nào trong CVAT dễ gây thao tác sai? |  |
| Một thay đổi cụ thể giúp annotator mới ít hỏi hơn? |  |

## 2. Owner đối chiếu export

Export có 23 box `traffic_sign`. Bảng dưới đây đếm annotation và attribute được lưu trong XML; số lượng không tự xác nhận rằng box đúng vị trí hoặc đúng scope.

| Sample | Box `traffic_sign` | `facing=irrelevant` | `visibility=blurry` | `needs_review=true` |
|---|---:|---:|---:|---:|
| GTS02 | 9 | 4 | 0 | 0 |
| GTS08 | 6 | 0 | 2 | 0 |
| GTS14 | 2 | 1 | 0 | 0 |
| GTS18 | 2 | 0 | 0 | 0 |
| GTS25 | 4 | 1 | 1 | 2 |
| **Tổng** | **23** | **6** | **3** | **2** |

### Kết quả và xử lý

| Phát hiện | Nguyên nhân | Xử lý | Bằng chứng |
|---|---|---|---|
| Cả 5 ảnh đều thiếu tag `image_status`. Guideline yêu cầu đúng một tag cho mỗi ảnh. | Execution error | Reject với bằng chứng; yêu cầu peer bổ sung tag rồi export lại. | `annotations.xml` không có tag ở bất kỳ ảnh nào; `02_guideline.md`, mục 3. |
| GTS02 có bốn box trên các đèn tín hiệu ở xa nhưng gán `traffic_sign`; gold yêu cầu `ignore=traffic_light`. | Execution error | Chấm `GTS02/d2 = 0`; yêu cầu peer xóa bốn box này. | `annotations.xml`, các box vùng x≈366–396 và x≈805–832; ảnh GTS02; `gold_decisions.csv` dòng GTS02/d2. |
| GTS14 có một box ở nền bên trái ngoài biển tam giác mục tiêu. GTS25 có hai box ngoài hai biển mục tiêu trong gold. | Cần chốt scope từng object | Rà các object bổ sung. Nếu gold bỏ sót biển hợp lệ, ghi `gold sai:` ở decision liên quan; giữ nguyên gold đã freeze. | Ảnh GTS14 và GTS25; `gold_decisions.csv`; `annotations.xml`. |
| GTS25 có 2 object với `needs_review=true`. | Cần owner review | Xác nhận hướng phục vụ và ghi quyết định vào clarification log. | Hai box GTS25 có `needs_review=true`; guideline mục 6. |
| XML có 6 object `facing=irrelevant`, 3 object `visibility=blurry`; mọi box đều có giá trị `truncated` và `needs_review`. | Cần review giá trị | Kiểm tra attribute khi QA toàn bộ batch; ghi lỗi theo từng object. | Attribute trong `annotations.xml`; guideline mục 3–6. |
| Box biển tam giác GTS14 rộng hơn mép trái khoảng 3–4 px trên ảnh phóng, vượt dung sai 2 px. Ba dòng geometry còn lại nhìn sát mép biển và không bao cột, cầu. | Geometry error ở GTS14; đánh giá thủ công | Chấm `GTS14/d2 = 0`, ba dòng geometry khác bằng `1`. Cần owner kiểm lại nếu có tọa độ gold chính xác. | Ảnh gốc và box trong `annotations.xml`; `gold_decisions.csv`, các dòng geometry. |

## 3. Điểm chấm

Đã điền `correct` và lý do cho cả 10 dòng trong `transfer_score.csv`, rồi chạy `python lab9.py gts`.

| Chỉ số | Kết quả |
|---|---:|
| D · Decision accuracy | 5/6 = 83,3 |
| C · Critical decisions | 3/3 = 100,0 |
| G · Geometry compliance | 3/4 = 75,0 |
| I · Independence | 100,0 theo 0 dòng trong `clarification_log.csv` |
| **GTS** | **87,5** |

Điểm geometry dựa trên ảnh phóng. I = 100 theo 0 dòng trong `clarification_log.csv`. GTS tính từ mười gold decision và không tính lỗi thiếu `image_status`. Batch cần sửa theo mục 2.
