# QA plan + quality gates

**Trạng thái:** Đề xuất v1. Nhóm cần chốt threshold trước khi dùng cho production hoặc freeze gold.

## Flow

Guideline v1 → calibration → guideline v2 → production → self-QC → review → rework → quality gate.

- **Ai review, review bao nhiêu:** Mỗi annotator tự kiểm 100% ảnh mình gán. Một thành viên khác review 100% ảnh blind và 20% ảnh calibration hoặc production của từng annotator. Nếu nhóm có ít ảnh, review toàn bộ batch.
- **Chọn sample theo rule nào:** Lấy mẫu theo tag rủi ro trong `sample_pack.csv`: `small_far`, `low_visibility`, `occlusion`, `negative`, `ambiguity`, và `critical`. Bảo đảm có mẫu âm và mẫu có biển sát ngưỡng 8 px. Review viên ghi rõ sample ID và lý do chọn.
- **Issue được ghi ở đâu, đóng thế nào:** Ghi bất đồng calibration vào `06_calibration_report.csv`, câu hỏi từ peer vào `07_blind_handoff/clarification_log.csv`, và quyết định rule vào `04_edge_cases/gold_decisions.csv` trước freeze. Owner ghi nguyên nhân, action, bằng chứng sau review. Đóng issue khi export đã sửa hoặc owner ghi rõ lý do giữ quyết định.
- **Khi phát hiện guideline gap:** Sửa `02_guideline.md`, tăng version và ghi bằng chứng vào `08_revision_log.md`. Nếu đổi ontology hoặc scope sau calibration, tạo lại task calibration. Không sửa gold hoặc sample pack sau freeze.

## Defect severity

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Bỏ sót biển rõ ràng trong phạm vi trên tuyến ego, hoặc gán vật ngoài phạm vi thành biển khiến downstream đưa ra cảnh báo sai. | Bỏ qua một biển cấm rõ ở phần đường xe đang đi; gán bảng quảng cáo thành `traffic_sign`. | Dừng handoff, sửa batch liên quan, kiểm tra lại toàn bộ lát dữ liệu cùng loại. |
| Major | Sai quyết định hoặc attribute làm thay đổi output downstream, nhưng không tạo lỗi critical. | Gộp hai biển độc lập; bỏ `truncated`; gán `facing` sai khi hướng tuyến nhìn thấy rõ. | Rework object và rà lại cùng rule trên batch. |
| Minor | Sai số hình học vượt tolerance 2 px nhưng không đổi instance hoặc phạm vi; lỗi metadata không ảnh hưởng quyết định. | Một cạnh box lệch nhẹ quá 2 px. | Sửa object; ghi lỗi để theo dõi lỗi lặp. |
| Question | Bằng chứng không đủ để chọn `LABEL`, `IGNORE` hoặc attribute. | Không xác định được vật mờ có phải biển; không thấy được nhánh đường mà biển phục vụ. | Đánh dấu trong CVAT và escalate cho owner. Không đoán. |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Decision accuracy | Số quyết định đúng về `LABEL`, `IGNORE`, `UNKNOWN` hoặc `ESCALATE` chia tổng quyết định trong batch review. | Đo xem peer có hiểu đúng scope và rule ambiguity không. |
| Detection recall | Số biển trong scope được peer gán nhãn chia tổng biển trong gold. | Bỏ sót biển làm giảm coverage của detector. |
| False-positive rate | Số object ngoài scope bị xuất thành `traffic_sign` chia tổng object ngoài scope được review. | Đo rủi ro cảnh báo nhiễu do gán nhầm quảng cáo, bảng tên đường hoặc vật khác. |
| Geometry compliance | Số box có mỗi cạnh lệch không quá 2 px chia tổng box được review. | Dùng tolerance đã ghi trong guideline và áp dụng trực tiếp lên ảnh. |
| Required-field completeness | Số ảnh review có đúng một `image_status`, mọi `review_candidate` có `decision` và `reason`, và các attribute của `traffic_sign` đã được xác nhận/chỉnh theo ảnh, chia tổng ảnh được review. | Các default được điền sẵn nên không thể chỉ tìm `__undefined__` để phát hiện annotator quên kiểm tra attribute. Reviewer cần xác nhận giá trị mặc định không bị giữ máy móc khi ảnh cho thấy điều khác. |

Metric high-risk: **critical defect escape count**. Đếm defect critical mà reviewer không phát hiện trước khi chấp nhận batch. Mục tiêu đề xuất là 0.

## Quality gate

Các threshold dưới đây là đề xuất cho nhóm, chưa phải kết quả đã đo.

```text
PASS if:
  critical defect escape count = 0
  decision accuracy >= 90%
  detection recall >= 95%
  false-positive rate <= 5%
  geometry compliance >= 90%
  required-field completeness = 100%
  mọi case UNKNOWN hoặc ESCALATE đều có bằng chứng và người nhận xử lý
REWORK if:
  có critical defect, hoặc bất kỳ metric nào dưới threshold
REJECT / ESCALATE if:
  gold thiếu object, guideline không phân xử case, hoặc CVAT export không giữ decision/attribute cần chấm
```

**Trade-off:** Batch nhỏ cho phép review toàn bộ blind set và không nên bỏ qua lỗi critical. Threshold recall cao ưu tiên tránh bỏ sót biển. Threshold geometry 90% cho phép một số sai lệch sửa được; những box lệch quá 2 px vẫn phải rework. Nhóm cần xác nhận các threshold này trước calibration.
