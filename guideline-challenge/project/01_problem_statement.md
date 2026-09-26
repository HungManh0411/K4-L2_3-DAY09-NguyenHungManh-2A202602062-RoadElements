# Problem statement + downstream contract

Tối đa nửa trang, viết **trước khi mở CVAT**. Đây là bằng chứng của gate G1 (topic lock). Thay mọi placeholder
mới là xong.

## Bài toán

Xác định vùng drivable area tại ranh giới giữa mặt đường, lề đường, vỉa hè và lối đi bộ, nơi ranh giới vật lý hoặc
mục đích sử dụng không rõ trong ảnh. Khó khăn chính là không nhầm phần xe có thể đi vào với không gian dành cho người
đi bộ, đồng thời không suy đoán khi ảnh thiếu bằng chứng.

## Downstream contract

1. **Downstream task / model / user là ai?** Mô hình perception và hệ thống lập kế hoạch đường đi cần bản đồ pixel
	các vùng xe có thể đi vào để ước lượng free space phía trước xe.
2. **Output annotation nào thực sự cần?** Polygon theo pixel cho `drivable_area`; polygon riêng cho phần chuyển tiếp
	nhìn thấy rõ nhưng không dành cho xe (`non_drivable_transition`); vùng không đủ bằng chứng được đánh dấu
	`uncertain_area` và gắn tag ảnh `review_required`. Không dùng attribute.
3. **Failure nào gây hậu quả lớn nhất?** Gán vỉa hè hoặc lối đi bộ thành `drivable_area`, khiến hệ thống có thể lập
	đường đi vào không gian của người đi bộ. Đây là lỗi `critical`.
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?** Annotator vẽ `uncertain_area`, gắn
	`review_required` cho ảnh và chuyển QA owner xem xét; không tự suy luận từ hình dạng bề mặt đơn thuần.

## Scope

- **Trong scope (bắt buộc label):** Mặt đường xe chạy và phần lề liền kề chỉ khi có bằng chứng nhìn thấy được rằng
  xe có thể đi vào; các đoạn tiếp giáp vỉa hè/lối đi bộ nơi cần quyết định ranh giới drivable. Gán polygon
  `drivable_area`, `non_drivable_transition` hoặc `uncertain_area` theo bằng chứng.
- **Ngoài scope (ignore):** Vùng không thuộc khu vực chuyển tiếp đang xét như mặt tiền, bãi đỗ riêng và phần cảnh
  ngoài vùng đường/lề/vỉa hè. Không cần vẽ nền ngoài scope.
- **Geometry tolerance:** Polygon bám theo ranh giới mặt đường/curb nhìn thấy; sai lệch tối đa 3 px ở ảnh gốc
  1280×720. Nếu ranh giới không quan sát được đủ rõ, đánh dấu vùng bất định thay vì nội suy.

## Output chấm được

Blind test chấm inclusion/class (`drivable_area` hay `non_drivable_transition`), `uncertain_area`/`review_required`
cho quyết định UNKNOWN/ESCALATE, cùng geometry polygon theo tolerance đã nêu. Vùng ngoài scope được bỏ qua theo
rule IGNORE; các loại nhãn và tag phải hiện diện trong export CVAT.

## Dữ liệu và giới hạn

Chỉ dùng 26 ảnh BDD100K trong `data/bdd100k/` (1280×720), chủ yếu là highway và city street; phần lớn ảnh ban ngày.
Sẽ rà ảnh để chọn 3–5 example, 5–8 calibration và 4–5 blind, không dùng trùng ảnh giữa các split. Catalog không
đảm bảo mọi ảnh đều có ranh giới vỉa hè/lề đường phù hợp; chỉ đưa ảnh vào split nếu kiểm tra trực quan xác nhận nó
thử được rule liên quan. Số lượng ảnh nhỏ nên kết quả không đại diện cho mọi điều kiện đường sá.
