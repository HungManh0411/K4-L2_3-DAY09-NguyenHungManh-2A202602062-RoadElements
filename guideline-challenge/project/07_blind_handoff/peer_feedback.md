# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền.

- **Nhóm peer:** team03
- **Người label blind:** Hoàng Minh Tuấn (team03)

## 1. Peer trả lời

1. **Rule nào rõ nhất / giúp quyết định nhanh nhất?** Quy tắc vẽ làn `direct_drivable` phủ trùm qua vạch người đi bộ (crosswalk) và bo quanh thân/gầm xe ô tô phía trước theo nguyên tắc visible surface rất dễ hiểu và thao tác nhanh.
2. **Rule nào mơ hồ hoặc phải tự suy diễn?** Quy tắc về dải lề đường bê tông ngoài vạch trắng liền ở cao tốc (BDD19) chưa nói rõ là IGNORE hay sidewalk; khu vực xe đỗ phía sau bó vỉa (BDD15) và dải nối sân cây xăng với vỉa hè (BDD12) chưa có quy tắc loại trừ cụ thể khiến người vẽ lúng túng.
3. **Sample nào khiến guideline "vỡ"?** 
   - `BDD19`: Dải lề bê tông quá rộng gây nhầm lẫn giữa lề đường và lối đi bộ.
   - `BDD18`: Cảnh ban đêm ánh đèn đường loá khiến khó nhận diện ranh giới vỉa hè phía sau hàng xe đỗ.
4. **Attribute / default nào trong CVAT dễ gây thao tác sai?** Việc phân biệt khi nào dùng polygon `uncertain_area` và khi nào gắn tag `review_required` còn dễ nhầm; checkbox `needs_review` dễ bị sót.
5. **Một thay đổi cụ thể giúp annotator mới ít hỏi hơn?** Thêm dòng quy định dứt khoát trong bảng Mục 5: "Lề đường ngoài vạch liền và khu vực xe đỗ sau bó vỉa luôn luôn IGNORE (không vẽ polygon nào)".

## 2. Owner phân loại

Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai được xếp vào một hướng xử lý.

| Feedback / decision sai | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý (accept + revise / reject with evidence / add escalation rule) | Bằng chứng |
|---|---|---|---|
| BDD19: Gán lề cao tốc thành `sidewalk` và `alternative_drivable` | guideline gap | accept + revise (bổ sung quy tắc dải lề cao tốc ngoài vạch trắng liền: IGNORE, không vẽ drivable hay sidewalk) | BDD19 d3, d4 trong `transfer_score.csv` |
| BDD18: Vẽ `alternative_drivable` lên hàng xe đỗ bên phải và bỏ sót `sidewalk` | guideline gap | accept + revise (bổ sung chỉ dẫn ban đêm: hàng xe đỗ bên lề là non-drivable, dải vỉa hè có cột đèn bắt buộc gán `sidewalk`) | BDD18 d3, d4 trong `transfer_score.csv` |
| BDD12: Vẽ `uncertain_area` lấn sang làn ngược chiều bên trái vạch vàng kép | execution error | reject with evidence (Mục 1, mục 5 và mục 10 đã cấm tuyệt đối vẽ polygon drivable sang làn đối diện qua vạch vàng kép) | BDD12 d3 trong `transfer_score.csv` |
| BDD15: Vẽ `alternative_drivable` vượt qua bó vỉa đỏ-trắng vào khu xe đỗ | guideline gap | accept + revise (khẳng định bó vỉa là ranh giới cứng, toàn bộ phần sau bó vỉa là IGNORE) | BDD15 d3 trong `transfer_score.csv` |
| BDD12: Vẽ `uncertain_area` trùm lên vỉa hè / sân cây xăng | guideline gap | accept + revise (quy định rõ sân cây xăng / bãi đỗ tư nhân sau vỉa hè là IGNORE, vỉa hè gán `sidewalk`) | BDD12 d4 trong `transfer_score.csv` |
