# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây. Thay mọi
placeholder mới là xong (gate G2).

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `drivable_area` | Polygon | class | - | - | - | Bề mặt đường an toàn cho xe chạy. |
| `areaType` | - | attribute (select) | `__undefined__`, `direct`, `alternative`, `uncertain` | `__undefined__` | false | Phân loại trạng thái/ưu tiên của làn đường. |
| `sidewalk` | Polygon | class | - | - | - | Khu vực vỉa hè dành cho người đi bộ. |
| `needs_review` | - | attribute (checkbox) | `false`, `true` | `false` | false | Đánh dấu vùng nghi ngờ ranh giới do bóng râm hoặc ánh sáng. |
| `review_required` | Tag | class (tag) | - | - | - | Báo cáo ngoại lệ cho toàn bộ ảnh. |
| `reason` | - | attribute (select) | `__undefined__`, `severe_weather_snow_rain`, `zero_visibility`, `unclear_road_boundary`, `conflicting_evidence` | `__undefined__` | false | Lý do báo cáo ngoại lệ. |

## Class hay attribute

- Việc chia `drivable_area` thành class duy nhất với các thuộc tính (`areaType = direct/alternative/uncertain`) giúp dễ dàng định nghĩa bề mặt đường, đồng thời đảm bảo phân biệt được mức ưu tiên.
- **Tại sao để `__undefined__` làm Default:** Bắt buộc annotator phải tự tay chọn `direct` hoặc `alternative`. Nếu đặt default là `direct`, annotator lười có thể bỏ qua và gán sai cho toàn bộ làn phụ, sinh ra **bias** cực lớn và nguy hiểm.
- `needs_review` và `review_required` là cơ chế Escalation để QA kiểm tra lại các Edge Case.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): CVAT 2.76.0
- **Tên task calibration** (có version guideline, ví dụ `team07-calib-v1`): `team08-drivable-calib-v1`
- **Guide của task đã dán `02_guideline.md`?** Đã dán.
- **Nhóm dùng Track hay Shape, vì sao:** Dùng **Shape**, vì task này xử lý ảnh tĩnh 2D rời rạc (Static Image), không phải chuỗi video liên tục nên không thể dùng Track.

## Setup test

Một thành viên **chưa tham gia setup** mở task và trả lời: label gì, dùng tool nào, gán attribute nào, khi nào
escalate. Ghi lại ai test và chỗ họ vấp:

- **Người test:** Ngô Đức Mạnh
- **Trả lời bộ câu hỏi:**
  - **Label gì?** Chỉ dùng một class duy nhất là `drivable_area`.
  - **Dùng tool nào?** Công cụ `Draw new polygon` (Phải dùng chế độ Shape, không dùng Track).z
  - **Gán attribute nào?** Bắt buộc phải bấm vào hình bánh răng / thanh bên phải để đổi `areaType` từ `__undefined__` sang `direct`, `alternative` hoặc `uncertain`. Tích thêm ô `needs_review` nếu không nhìn rõ do bóng râm.
  - **Khi nào escalate?** Khi ranh giới đường hoàn toàn không thể xác định (do tuyết phủ, mưa to, ảnh quá tối). Lúc này chuyển qua dùng Tag `review_required` cho ảnh và chọn `reason` tương ứng.
- **Chỗ vấp:** Lúc đầu tôi quên không chọn `areaType` cứ để là `__undefined__`, may mà Guideline bắt buộc phải chọn nên tôi mới nhớ ra. Hơi lúng túng lúc đầu vì chưa phân biệt rõ khi nào tích `needs_review` (nghi ngờ cục bộ) và khi nào dùng tag `review_required` (báo cáo ngoại lệ toàn bộ ảnh).
