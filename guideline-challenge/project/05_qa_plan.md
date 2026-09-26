# QA plan + quality gates

Không được viết "reviewer kiểm tra lại". Phải có sampling, metric, threshold và action khi fail. Thay mọi placeholder
mới là xong (gate G6).

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate. Ghi cụ thể cho project của nhóm:

- **Ai review, review bao nhiêu:** Ngô Đức Mạnh (QA Owner) sẽ chịu trách nhiệm review bài. Mức độ review: 100% đối với các ảnh rủi ro cao, 20% đối với ảnh bình thường.
- **Chọn sample theo rule nào** (random, theo tag rủi ro, theo annotator mới…): 
  - Ưu tiên 1: Chấm 100% các ảnh có tag `review_required` và các polygon có tích `needs_review = true`.
  - Ưu tiên 2: Random 20% các ảnh còn lại trong batch của mỗi annotator.
- **Issue được ghi ở đâu, đóng thế nào:** Issue được mở trực tiếp bằng tính năng Issue/Comment trên giao diện CVAT tại vị trí pixel bị lỗi. Annotator sau khi sửa (rework) xong phải mark issue là "Resolved", sau đó QA vào check lại và "Close" issue.
- **Khi phát hiện guideline gap thì update và version ra sao:** Khi phát hiện case mới chưa có luật, QA báo cho Spec Owner (Vũ Tiến Thăng). Thăng sẽ chốt luật, thêm vào mục Edge Case ở file `02_guideline.md`, cập nhật version (VD: `v1` -> `v2`) và nhắn thông báo vào group chat của nhóm để mọi người đồng bộ.

## Defect severity

Nhóm được đổi mapping nếu downstream contract khác, nhưng phải giải thích và chốt trước khi QA.

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Lỗi sai class nghiêm trọng gây nhầm lẫn không gian an toàn, hoặc lấn làn nguy hiểm. | Vẽ polygon `direct_drivable` trườn lên vỉa hè (`sidewalk`), hoặc vẽ lấn qua vạch vàng kép sang làn ngược chiều. | **REJECT/REWORK** toàn bộ batch. Phạt vẽ lại. |
| Major | Sai logic giao thông hoặc không tuân thủ quy tắc Visible Surface (bao trùm vật cản). | Nhầm `alternative_drivable` thành `direct_drivable`. Vẽ xuyên qua gầm xe đang đỗ mà không khoét lỗ. | **REWORK** các frame bị lỗi. Yêu cầu annotator tự sửa. |
| Minor | Lỗi hình học (Geometry) hoặc thiếu điểm neo (points) làm polygon bị méo. | Viền polygon lệch khỏi ranh giới vỉa hè / mép đường lớn hơn 3 pixel. Cắt góc (cut corners) ở đường cong. | Nhắc nhở, tự QA sửa lại cho nhanh hoặc yêu cầu Rework nếu sai > 3 lỗi. |
| Question | Tình huống bất định không thể xử lý bằng guideline hiện tại. | Ảnh bị sương mù nửa kín nửa hở, không phân biệt được vạch kẻ hay mép lề. | **ESCALATE**. Đổi sang tag `review_required` và chuyển QA/Spec Owner phân xử. |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| **Critical Defect Escape Rate** | (Số lỗi Critical lọt qua vòng QA / Tổng số sample) * 100% | Bài toán xe tự lái yêu cầu an toàn tuyệt đối. Lỗi lấn vỉa hè hay lấn làn ngược chiều lọt qua có thể gây tai nạn nghiêm trọng. |
| **Major Defect Rate** | (Số lỗi Major phát hiện / Tổng số sample check) * 100% | Phản ánh mức độ hiểu luật và tính cẩn thận của annotator (đặc biệt trong việc khoét lỗ xe đỗ). |
| **Polygon Boundary Accuracy** | Đánh giá cảm quan (Nhẩm IoU > 95% và sai số viền < 3px) | Đảm bảo hệ thống perception nhận diện chính xác mép đường để xe chạy mượt mà. |

Metric high-risk tách riêng (ví dụ critical defect escape rate): Đặt mục tiêu **Critical Defect Escape Rate = 0%**. Tuyệt đối không được phép có lỗi lấn vỉa hè/ngược chiều tồn tại sau khi bàn giao dữ liệu.

## Quality gate

Threshold là đề xuất của nhóm, không phải chuẩn ngành. Giải thích trade-off cost/risk.

```text
PASS if:
  - Critical = 0
  - Major Defect Rate < 5%
  - Minor Defect Rate < 10%
REWORK if: 
  - Có 1-2 lỗi Critical
  - HOẶC Major Defect Rate nằm trong khoảng 5% - 15%
REJECT / ESCALATE if: 
  - Có >= 4 lỗi Critical (Chứng tỏ làm ẩu hoặc không hiểu Guideline)
  - HOẶC Major Defect Rate > 15%
```

Trade-off: Việc bắt buộc áp dụng quy tắc Visible Surface (chừa mọi xe đang đỗ và người đi bộ) sẽ khiến **Cost (Thời gian và công sức vẽ của Annotator)** tăng lên đáng kể. Tuy nhiên, nó giảm thiểu tối đa **Risk (Rủi ro)** mô hình nhận diện nhầm vật cản tĩnh thành không gian an toàn (Drivable space). Ngoài ra, việc phạt nặng (REJECT) nếu lấn vỉa hè cũng làm tăng chi phí quản lý của QA nhưng lại bảo vệ tuyệt đối an toàn cho bài toán lái xe tự động (Downstream Task).
