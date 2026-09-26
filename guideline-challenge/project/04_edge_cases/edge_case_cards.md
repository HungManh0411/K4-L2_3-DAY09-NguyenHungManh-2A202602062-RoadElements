# Edge-case library

Tối thiểu **8 card**, khuyến nghị 10–12. Một edge case tốt là case mà hai annotator hợp lý có thể làm khác nhau nếu
guideline chưa rõ. Tám ảnh dễ có label rõ ràng không được tính là edge-case library.

Cần có đủ độ đa dạng: occlusion / truncation / small-far · ambiguous semantics · conflicting road elements · **một case
critical-risk** · **một case guideline cho phép escalation**.

File này là kho nội bộ của nhóm, **không gửi cho peer**. Card dùng ảnh example/calibration thì chép rule + ví dụ sang
`02_guideline.md` (mục 7 và 9) để peer đọc được. Card về ảnh blind chỉ nằm ở đây, và decision của nó phải có trong
`gold_decisions.csv` trước `make freeze`.

`make status` đếm số dòng `CASE ID:` đã điền (đã thay placeholder). Copy khối dưới cho mỗi case.

Card viết theo guideline v1 (`drivable_area` + `areaType` / `needs_review`, `sidewalk`, tag `review_required` +
`reason`). EC01–EC05 là ảnh **blind**, decision tương ứng nằm trong `gold_decisions.csv`. EC06–EC11 là ảnh
example/calibration. Dòng "Guideline gap" ghi chỗ v1 chưa trả lời được, cần xử lý ở v2.

---

CASE ID: EC01
Sample: BDD11 (blind)
Scene: Khu dân cư ban ngày, xe ego sắp qua giao lộ có vạch đi bộ; sau giao lộ là đường hai chiều vạch vàng kép, xe đỗ kín hai bên, người đi bộ ở góc vỉa hè phải.
Observation: Vạch đi bộ ngay trước xe và ở phố cắt ngang bên phải; vỉa hè hai góc phố nhìn rõ, có bó vỉa.
Decision: LABEL (làn ego, vỉa hè) · IGNORE (làn ngược chiều)
Expected: `drivable_area` `areaType=direct` phủ trùm vạch đi bộ; không có `drivable_area` bên trái vạch vàng kép; mép polygon bo theo mép ngoài hàng xe đỗ; `sidewalk` ở góc phố phải, không chồng lên `drivable_area`. (Gold BDD11 d1–d4)
Rationale: Downstream cần free space liên tục qua vạch đi bộ và biết chính xác nơi bắt đầu không gian người đi bộ; lấn làn ngược chiều gây quỹ đạo đối đầu.
Common mistake: Cắt polygon tại vạch đi bộ; vẽ luồn dưới gầm xe đỗ; `sidewalk` chồng lên `drivable_area`.
Diversity: normal · crosswalk · critical (làn ngược chiều)

---

CASE ID: EC02
Sample: BDD04 (blind)
Scene: Phố dân cư dốc, hai chiều vạch vàng kép; bên phải là vỉa hè bê tông rộng với nhiều dốc lối xe vào garage, xe máy đỗ sát mép.
Observation: Ranh giới nhựa – bê tông bên phải rõ, nhưng tại các dốc garage bó vỉa gần như bằng mặt đường.
Decision: LABEL (làn ego, vỉa hè kể cả đoạn dốc garage) · IGNORE (làn ngược chiều)
Expected: Một `drivable_area` `areaType=direct` bên phải vạch vàng kép; toàn bộ dải bê tông bên phải, kể cả dốc garage → `sidewalk`; cạnh chung tại chân bó vỉa (≤ 3 px); không có `drivable_area` bên trái vạch vàng kép. (Gold BDD04 d1–d4)
Rationale: Dốc garage vẫn nằm trên vỉa hè — không gian người đi bộ. Vẽ nó thành `drivable_area` là failure critical trong `01_problem_statement.md`.
Common mistake: Thấy dốc liền mặt đường nên kéo `drivable_area` lên dốc; hoặc áp mục 1 "curb cut → IGNORE" và để trống đoạn dốc.
Diversity: critical · ambiguity (bó vỉa hạ cốt)
Guideline gap: Mục 1 chỉ IGNORE "curb cut / pedestrian ramp" cho người đi bộ; chưa nói dốc lối **xe** vào cắt qua vỉa hè. v2 cần: dốc lối xe vào nằm trên vỉa hè → `sidewalk`, không bao giờ là `drivable_area`.

---

CASE ID: EC03
Sample: BDD12 (blind)
Scene: Đại lộ đô thị, vạch đi bộ ngay trước xe, vạch vàng bên trái ngăn làn ngược chiều có taxi; bên phải là vỉa hè liền sân cây xăng, hai người đứng trên vỉa hè.
Observation: Vỉa hè bên phải hạ cốt, liền mạch với sân cây xăng; ranh giới vỉa hè – sân cây xăng không có gờ rõ.
Decision: LABEL (mặt đường chiều đi, dải vỉa hè) · IGNORE (làn ngược chiều, sân cây xăng)
Expected: `drivable_area` phủ trùm vạch đi bộ, mép trái bám mép phải vạch vàng; dải vỉa hè nơi hai người đứng → `sidewalk`; sân cây xăng không vẽ; không có `drivable_area` trên vỉa hè hay sân cây xăng. (Gold BDD12 d1–d5)
Rationale: Người đi bộ đứng đúng trên vùng dễ bị vẽ nhầm — lỗi critical trực tiếp.
Common mistake: Coi lối vào cây xăng là mặt đường và kéo `drivable_area` qua vỉa hè; hoặc vẽ cả sân cây xăng thành `sidewalk`.
Diversity: critical · conflict (lối xe vào cắt qua vỉa hè) · crosswalk
Guideline gap: như EC02; thêm: sân cây xăng / bãi đỗ tư nhân chưa có trong mục 5 → v2 ghi IGNORE.

---

CASE ID: EC04
Sample: BDD20 (blind)
Scene: Đường khu dân cư, vạch vàng kép, vạch trắng liền hai bên, xe đỗ và cọc tiêu bên phải, dải cỏ rồi vỉa hè; giá đỡ điện thoại che đáy ảnh, decal che góc trên phải.
Observation: Đáy ảnh bị giá đỡ che một phần mặt đường; giữa mặt đường và vỉa hè có dải cỏ.
Decision: LABEL (làn ego, vỉa hè) · IGNORE (làn ngược chiều, dải cỏ, vùng bị giá đỡ che)
Expected: `drivable_area` `areaType=direct` giữa vạch vàng kép và vạch trắng liền bên phải; polygon dừng tại rìa giá đỡ; `sidewalk` chỉ phủ dải vỉa hè, không phủ dải cỏ. (Gold BDD20 d1–d4)
Rationale: Vùng bị che không có bằng chứng; downstream không được nhận free space bịa ra. Dải cỏ không phải vỉa hè.
Common mistake: Vẽ phủ lên giá đỡ; gộp dải cỏ vào `sidewalk`; kéo `drivable_area` qua vạch trắng tới hàng xe đỗ.
Diversity: occlusion (vật che trong xe) · critical (làn ngược chiều)
Guideline gap: Dải giữa vạch trắng liền và hàng xe đỗ (làn đỗ / lề) chưa có rule: mục 3 nói bo theo mép xe đỗ, định nghĩa `alternative` lại loại vạch liền. v2 cần chọn một.

---

CASE ID: EC05
Sample: BDD26 (blind)
Scene: Đường phố ban đêm, đèn đường và đèn hậu loá; bên trái có đảo / góc vỉa hè với cột đèn; xe đỗ bên phải.
Observation: Làn phía trước thấy vạch trắng đứt; mép trái và ranh giới đảo chỉ thấy lờ mờ; vỉa hè phải bị xe đỗ và bóng tối che.
Decision: LABEL + ESCALATE cấp object (`needs_review`) · IGNORE (đảo có cột đèn)
Expected: `drivable_area` `areaType=direct` ở làn trước, `needs_review=true`; **không** gắn tag `review_required`; không vẽ `drivable_area` lên đảo có cột đèn. (Gold BDD26 d1–d4)
Rationale: Mặt đường vẫn thấy nên vẫn label, nhưng ranh giới không chắc → QA phải xem lại. Tag cả ảnh chỉ dành cho khi toàn ảnh không dùng được (mục 7.4).
Common mistake: Gắn tag `review_required` `reason=zero_visibility` và bỏ trống ảnh; vẽ tự tin mà không tick `needs_review`; đoán vị trí vỉa hè trong bóng tối.
Diversity: escalation · low_visibility · ambiguity

---

CASE ID: EC06
Sample: BDD01 (example)
Scene: Cao tốc 4+ làn, barrier bê tông hai bên, vùng sọc chéo (gore) ở lối rẽ ra bên phải.
Observation: Vùng sọc chéo cùng cao độ với mặt đường, không có vật cản vật lý; không có vỉa hè.
Decision: LABEL (các làn) · IGNORE (vùng sọc chéo, barrier)
Expected: Mỗi làn một polygon `direct` / `alternative`; mép phải dừng ở viền vùng sọc chéo; không vẽ lên barrier; không có `sidewalk`.
Rationale: Mục 5: dải phân cách mềm IGNORE.
Common mistake: Vẽ phủ vùng sọc chéo; vẽ barrier bê tông thành `sidewalk`.
Diversity: conflict (vạch sơn vs mặt đường liền) · negative (không có vỉa hè)
Guideline gap: Mục 9 mô tả BDD01 là "3 làn" — ảnh thật có ≥ 4 làn và vùng sọc chéo; cần sửa ví dụ.

---

CASE ID: EC07
Sample: BDD03 (example)
Scene: Cao tốc, vạch vàng liền ở lề **trái**, bên ngoài là dải lề sẫm màu, cỏ, guardrail; lòng đường chiều kia ở xa bên trái.
Observation: Vạch vàng ở đây là vạch lề trái của cao tốc chia chiều, không phải tim đường hai chiều.
Decision: LABEL (các làn) · IGNORE (lề, cỏ, guardrail, lòng đường chiều kia)
Expected: Polygon làn trái dừng tại mép trong vạch vàng; không vẽ dải lề và cỏ; không có `sidewalk`.
Rationale: Lề không phải làn chuyển sang hợp pháp (định nghĩa `alternative` mục 4.1).
Common mistake: Coi vạch vàng này là tim đường nên bỏ trống làn trái; hoặc vẽ lề như `alternative`.
Diversity: ambiguity (ý nghĩa vạch vàng) · conflict

---

CASE ID: EC08
Sample: BDD10 (example)
Scene: Phố dân cư hai chiều, tim đường vàng **đứt**, làn xe đạp giữa vạch trắng và hàng xe đỗ hai bên; vỉa hè phía sau xe đỗ.
Observation: Làn ngược chiều chỉ tách bằng vạch vàng đứt; làn xe đạp nằm giữa làn xe và xe đỗ; vỉa hè chỉ lộ ra từng đoạn giữa các xe.
Decision: LABEL (làn ego) · IGNORE (làn ngược chiều, làn xe đạp, vỉa hè bị che)
Expected: `drivable_area` `areaType=direct` giữa tim đường vàng đứt và vạch trắng làn xe đạp bên phải; không vẽ `sidewalk` ở đoạn bị xe đỗ che.
Rationale: Không chuyển làn hợp pháp sang làn xe đạp; làn ngược chiều không phải `alternative`.
Common mistake: Gắn làn ngược chiều là `alternative` vì vạch đứt; kéo polygon tới mép xe đỗ, phủ làn xe đạp; vẽ `sidewalk` xuyên qua hàng xe đỗ.
Diversity: ambiguity · conflict · occlusion
Guideline gap: Mục 1 chỉ IGNORE làn ngược chiều "bị ngăn bởi vạch vàng liền", mục 5 lại IGNORE vô điều kiện; làn xe đạp chưa có rule; `sidewalk` bị xe đỗ che chưa có rule. v2 cần: làn ngược chiều luôn IGNORE; làn xe đạp → IGNORE, mép tại vạch trắng; `sidewalk` chỉ vẽ phần nhìn thấy.

---

CASE ID: EC09
Sample: BDD15 (example)
Scene: Đường đô thị nhiều làn cùng chiều; bên phải bó vỉa sơn đỏ-trắng, sau đó là dải đỗ xe và vỉa hè có cây; bên trái rào kim loại.
Observation: Xe đỗ nằm **sau** bó vỉa, không trên lòng đường.
Decision: LABEL (các làn, vỉa hè) · IGNORE (dải đỗ xe, rào)
Expected: Polygon làn phải dừng tại chân bó vỉa đỏ-trắng; dải đỗ xe không vẽ; vỉa hè nhìn thấy → `sidewalk`; mép trái dừng tại chân rào.
Rationale: Bó vỉa là ranh giới vật lý; dải đỗ không phải làn xe chạy.
Common mistake: Áp rule "bo theo mép xe đỗ" và kéo polygon qua bó vỉa tới thân xe; hoặc vẽ dải đỗ xe thành `sidewalk`.
Diversity: conflict (rule xe đỗ vs rule bó vỉa)

---

CASE ID: EC10
Sample: BDD24 (calibration)
Scene: Phố đô thị sau tuyết, đống tuyết lớn giữa lòng đường bên phải, rào sắt và tuyết trên vỉa hè trái, người đi bộ trên vỉa hè phải.
Observation: Tuyết không phủ kín đường (< 70%) nhưng đống tuyết chắn một phần làn; vỉa hè hai bên vẫn thấy.
Decision: LABEL (mặt đường trống, vỉa hè) · IGNORE (đống tuyết)
Expected: `drivable_area` dừng tại chân đống tuyết; vỉa hè nhìn thấy → `sidewalk` (kể cả phần có tuyết); không gắn tag `review_required`.
Rationale: Đống tuyết là vật cản cố định, tương tự hàng cọc tiêu (mục 5 dòng cuối); đường vẫn nhìn thấy rõ.
Common mistake: Gắn tag `reason=severe_weather_snow_rain` cho cả ảnh dù đường vẫn thấy; vẽ phủ qua đống tuyết.
Diversity: low_visibility · occlusion (vật cản trên đường)
Guideline gap: Mục 6 chỉ nói tuyết phủ mặt đường, chưa nói đống tuyết là vật cản → thêm vào v2.

---

CASE ID: EC11
Sample: BDD17 (calibration)
Scene: Phố đô thị trời mưa, giọt nước trên kính, giá đỡ điện thoại che góc trái dưới; vỉa hè phải có xe đạp dựng.
Observation: Mặt đường và bó vỉa phải vẫn đọc được qua giọt mưa; góc trái bị che kín.
Decision: LABEL (vùng nhìn thấy) · IGNORE (vùng sau giá đỡ)
Expected: `drivable_area` và `sidewalk` phải vẽ bình thường qua giọt mưa, dừng tại rìa giá đỡ; không `needs_review`, không tag.
Rationale: Mục 6: giọt mưa không đổi quyết định nếu ranh giới vẫn thấy.
Common mistake: Tick `needs_review` hoặc gắn tag chỉ vì trời mưa.
Diversity: occlusion · low_visibility

---
