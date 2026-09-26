# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v2 | 1. Quy định toàn bộ dải vỉa hè bao gồm cả đoạn dốc garage thuộc `sidewalk`.<br>2. Loại bỏ ô đỗ xe khỏi `alternative_drivable`.<br>3. Bổ sung quy tắc occlusion: chỉ gán `sidewalk` khi bề rộng nhìn thấy >= 10px.<br>4. Thêm ví dụ phân định vùng giao cắt ngã tư cho `uncertain_area`. | Giải quyết các điểm bất đồng lớn giữa annotators được phát hiện trong đợt đo calibration nội bộ. | `BDD04`, `BDD10`, `BDD11` trong `06_calibration_report.csv` và `06_calibration_measure.csv` |
| v3 | 1. Bổ sung quy định rõ dải lề đường cao tốc (shoulder) ngoài vạch trắng liền: IGNORE hoàn toàn, không vẽ drivable hay sidewalk.<br>2. Cấm tuyệt đối vẽ polygon drivable lên hàng xe đỗ bên lề kể cả trong điều kiện ban đêm.<br>3. Bổ sung quy tắc sân cây xăng / bãi đỗ tư nhân sau vỉa hè: IGNORE.<br>4. Nhấn mạnh vỉa hè có cột đèn ban đêm bắt buộc gán `sidewalk`. | Khắc phục các lỗi sai của nhóm peer trong đợt blind test (GTS đạt 63.0) nhằm bảo đảm tính chuyển giao tuyệt đối của guideline. | BDD12, BDD15, BDD18, BDD19 trong `transfer_score.csv` và `peer_feedback.md` |
