# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v2 | 1. Quy định toàn bộ dải vỉa hè bao gồm cả đoạn dốc garage thuộc `sidewalk`.<br>2. Loại bỏ ô đỗ xe khỏi `alternative_drivable`.<br>3. Bổ sung quy tắc occlusion: chỉ gán `sidewalk` khi bề rộng nhìn thấy >= 10px.<br>4. Thêm ví dụ phân định vùng giao cắt ngã tư cho `uncertain_area`. | Giải quyết các điểm bất đồng lớn giữa annotators được phát hiện trong đợt đo calibration nội bộ. | `BDD04`, `BDD10`, `BDD11` trong `06_calibration_report.csv` và `06_calibration_measure.csv` |
