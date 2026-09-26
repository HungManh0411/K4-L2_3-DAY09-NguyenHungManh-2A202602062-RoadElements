# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây. Thay mọi
placeholder mới là xong (gate G2).

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `direct_drivable` | Polygon | class | - | - | - | Làn đường xe ego đang đi. |
| `alternative_drivable` | Polygon | class | - | - | - | Làn đường cùng chiều có thể chuyển sang. |
| `uncertain_area` | Polygon | class | - | - | - | Vùng mặt đường không rõ là direct hay alternative. |
| `needs_review` | - | attribute (checkbox) | `false`, `true` | `false` | false | Đánh dấu vùng nghi ngờ ranh giới do bóng râm hoặc ánh sáng (dùng chung cho cả 3 class trên). |
| `sidewalk` | Polygon | class | - | - | - | Khu vực vỉa hè dành cho người đi bộ. |
| `review_required` | Tag | class (tag) | - | - | - | Báo cáo ngoại lệ cho toàn bộ ảnh. |
| `reason` | - | attribute (select) | `__undefined__`, `severe_weather_snow_rain`, `zero_visibility`, `unclear_road_boundary`, `conflicting_evidence` | `__undefined__` | false | Lý do báo cáo ngoại lệ. |

## Class hay attribute

- Việc tách thành 3 class độc lập (`direct_drivable`, `alternative_drivable`, `uncertain_area`) giúp CVAT tự động tô màu khác nhau cho từng loại mặt đường, giúp Annotator dễ nhìn và kiểm tra lỗi trực quan hơn so với việc gộp chung vào 1 class và dùng attribute.
- `needs_review` và `review_required` là cơ chế Escalation để QA kiểm tra lại các Edge Case.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): CVAT 2.76.0
- **Tên task calibration** (có version guideline, ví dụ `team07-calib-v1`): `team08-drivable-calib-v1`
- **Guide của task đã dán `02_guideline.md`?** Đã dán.
- **Nhóm dùng Track hay Shape, vì sao:** Dùng **Shape**, vì task này xử lý ảnh tĩnh 2D rời rạc (Static Image), không phải chuỗi video liên tục nên không thể dùng Track.

## Sample pack dùng cho CVAT

- **Example (3 ảnh):** `BDD01`, `BDD02`, `BDD03`.
- **Calibration (7 ảnh):** `BDD04`, `BDD10`, `BDD11`, `BDD16`, `BDD17`, `BDD20`, `BDD24`.
- **Blind (5 ảnh):** `BDD12`, `BDD15`, `BDD18`, `BDD19`, `BDD23`.
- Task calibration chỉ upload ảnh trong `build/calibration/`; không đưa ảnh blind vào task này.
- Các ảnh được nhắc trong phần Examples của guideline (`BDD01`, `BDD02`, `BDD03`, `BDD10`, `BDD16`) chỉ nằm ở
  split example/calibration và không bị lộ trong blind test.

## Setup test

Một thành viên **chưa tham gia setup** mở task và trả lời: label gì, dùng tool nào, gán attribute nào, khi nào
escalate. Ghi lại ai test và chỗ họ vấp:

- **Người test:** Lê Ngọc Nam
- **Trả lời bộ câu hỏi:**
  - **Label gì?** Chọn các class polygon độc lập (`direct_drivable`, `alternative_drivable`, `uncertain_area`, `sidewalk`).
  - **Dùng tool nào?** Công cụ `Draw new polygon` (Phải dùng chế độ Shape, không dùng Track).
  - **Gán attribute nào?** Tích ô `needs_review` ở thanh bên phải nếu không nhìn rõ ranh giới cục bộ.
  - **Khi nào escalate?** Khi ranh giới đường hoàn toàn không thể xác định (do tuyết phủ, mưa to, ảnh quá tối). Lúc này chuyển qua dùng Tag `review_required` cho ảnh và chọn `reason` tương ứng.
- **Chỗ vấp:** Hơi lúng túng lúc đầu vì chưa phân biệt rõ khi nào tích `needs_review` (nghi ngờ cục bộ) và khi nào dùng tag `review_required` (báo cáo ngoại lệ toàn bộ ảnh).
