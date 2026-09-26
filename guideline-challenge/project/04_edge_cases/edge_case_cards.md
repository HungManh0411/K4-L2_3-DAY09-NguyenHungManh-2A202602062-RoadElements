# Edge-case library

Tối thiểu **8 card**, khuyến nghị 10–12. Một edge case tốt là case mà hai annotator hợp lý có thể làm khác nhau nếu
guideline chưa rõ. Tám ảnh dễ có label rõ ràng không được tính là edge-case library.

Cần có đủ độ đa dạng: occlusion / truncation / small-far · ambiguous semantics · conflicting road elements · **một case
critical-risk** · **một case guideline cho phép escalation**.

File này là kho nội bộ của nhóm, **không gửi cho peer**. Card dùng ảnh example/calibration thì chép rule + ví dụ sang
`02_guideline.md` (mục 7 và 9) để peer đọc được. Card về ảnh blind chỉ nằm ở đây, và decision của nó phải có trong
`gold_decisions.csv` trước `make freeze`.

`make status` đếm số dòng `CASE ID:` đã điền (đã thay placeholder). Copy khối dưới cho mỗi case.

Card viết theo ontology trong `03_ontology_and_cvat_setup.md` / `03_cvat_labels.json`: polygon `direct_drivable`,
`alternative_drivable`, `uncertain_area` (đều có checkbox `needs_review`), polygon `sidewalk`, tag `review_required` +
`reason`. Split theo `sample_pack.csv`: EC01–EC05 là ảnh **blind** (decision nằm trong `gold_decisions.csv`), EC06–EC12
là ảnh example/calibration. Dòng "Guideline gap" ghi chỗ guideline v1 chưa trả lời được, cần xử lý ở v2.

---

CASE ID: EC01
Sample: BDD12 (blind)
Scene: Đại lộ đô thị ban ngày, ba làn cùng chiều, vạch đi bộ ngay trước xe; vạch vàng kép bên trái ngăn làn ngược chiều có taxi; bên phải là vỉa hè liền sân cây xăng, hai người đứng trên vỉa hè.
Observation: Xe ego ở làn giữa sau xe van trắng; làn trái có SUV đen, làn phải có taxi vàng; vỉa hè bên phải hạ thấp, liền mạch với sân cây xăng.
Decision: LABEL (làn giữa, hai làn bên, vỉa hè) · IGNORE (làn ngược chiều, xe phía trước)
Expected: `direct_drivable` làn giữa phủ trùm vạch đi bộ; `alternative_drivable` x2 (làn trái, làn phải), mỗi làn một polygon; không polygon drivable bên trái vạch vàng kép; dải vỉa hè nơi hai người đứng → `sidewalk`; polygon bo quanh SUV và van. (Gold BDD12 d1–d5)
Rationale: Người đi bộ đứng đúng trên vùng dễ bị vẽ nhầm — kéo drivable lên đó là failure critical trong `01_problem_statement.md`; lấn làn ngược chiều gây quỹ đạo đối đầu.
Common mistake: Cắt polygon tại vạch đi bộ; gộp ba làn thành một polygon; kéo `alternative_drivable` làn phải qua vỉa hè vào sân cây xăng.
Diversity: normal · crosswalk · critical (vỉa hè có người, làn ngược chiều)
Guideline gap: Sân cây xăng / bãi đỗ tư nhân sau vỉa hè chưa có rule (không phải làn, không phải vỉa hè) → v2 ghi IGNORE. Gold không chấm vùng này.

---

CASE ID: EC02
Sample: BDD15 (blind)
Scene: Đường đô thị nhiều làn cùng chiều; bên phải là bó vỉa sơn đỏ-trắng, sau đó là khu xe đỗ và vỉa hè có cây; bên trái là gờ bê tông và rào kim loại; bóng tấm che nắng ở mép trên ảnh.
Observation: Xe đỗ (xe bạc, xe xanh) nằm **sau** bó vỉa, không trên lòng đường; xe ego ở làn giữa hai vạch trắng đứt.
Decision: LABEL (làn ego, làn phải) · IGNORE (khu xe đỗ sau bó vỉa, rào trái)
Expected: `direct_drivable` làn chứa mũi xe ego; `alternative_drivable` làn phải, mép phải bám chân bó vỉa đỏ-trắng (≤ 3 px); không polygon drivable nào vượt qua bó vỉa; mép trái dừng tại chân rào. (Gold BDD15 d1–d5)
Rationale: Bó vỉa là ranh giới vật lý với không gian đỗ xe / người đi bộ; lấn qua là lập quỹ đạo lên vỉa hè.
Common mistake: Áp rule "bo theo mép ngoài xe đỗ" và kéo polygon qua bó vỉa tới thân xe; tô khu xe đỗ thành `sidewalk`.
Diversity: critical · conflict (rule xe đỗ vs rule bó vỉa)

---

CASE ID: EC03
Sample: BDD18 (blind)
Scene: Đường phố ban đêm, vạch đi bộ ngay trước xe, vạch trắng liền giữa đường, xe đỗ kín hai bên, vỉa hè phải có cột đèn và đèn tín hiệu đi bộ; ánh đèn đường loá.
Observation: Mặt đường, vạch đi bộ, hàng xe đỗ và cột đèn vẫn nhìn thấy; phía xa và vùng tối hai bên mờ.
Decision: LABEL (làn ego, vỉa hè phải) · không ESCALATE cả ảnh (có thể tick `needs_review`)
Expected: `direct_drivable` bên phải vạch trắng liền, phủ trùm vạch đi bộ, mép phải bo theo mép ngoài hàng xe đỗ; `sidewalk` quanh cột đèn; **không** gắn tag `review_required`; không polygon drivable trên xe đỗ hay vỉa hè. (Gold BDD18 d1–d5)
Rationale: Ảnh tối nhưng vẫn đủ bằng chứng; bỏ trống cả ảnh làm downstream mất dữ liệu đêm. Tag chỉ dành cho khi toàn ảnh không dùng được (mục 7.4).
Common mistake: Gắn tag `review_required` `reason=zero_visibility` rồi không vẽ gì; kéo polygon vào vùng tối không nhìn thấy.
Diversity: low_visibility · escalation (phân biệt `needs_review` và tag) · critical (vỉa hè)
Guideline gap: Làn cùng chiều bên trái vạch trắng liền không phải `alternative` (mục 4.2 loại vạch liền) và cũng không phải làn ngược chiều → chưa có rule. Gold không chấm làn này; v2 cần quyết định (IGNORE hoặc `uncertain_area`).

---

CASE ID: EC04
Sample: BDD19 (blind)
Scene: Cao tốc chiều chiều, ba làn cùng chiều; bên phải vạch trắng liền là lề bê tông rộng, loang lổ, rồi cỏ và cây.
Observation: Lề bê tông cùng cao độ và rộng gần bằng một làn, trông như lối đi nhưng không có người đi bộ hay dấu hiệu vỉa hè.
Decision: LABEL (làn ego, hai làn bên trái) · IGNORE (lề bê tông, cỏ)
Expected: `direct_drivable` làn phải cùng, mép phải bám mép trong vạch trắng liền (≤ 3 px); `alternative_drivable` x2 cho hai làn bên trái; lề bê tông **không** là drivable và **không** là `sidewalk`. (Gold BDD19 d1–d5)
Rationale: Downstream chỉ cần làn hợp pháp; gán lề cao tốc thành `sidewalk` làm sai thống kê không gian người đi bộ, gán thành drivable làm planner coi lề là làn.
Common mistake: Tô lề bê tông thành `sidewalk` vì "không phải đường"; hoặc gộp lề vào `direct_drivable`.
Diversity: ambiguity (shoulder vs pedestrian path) · negative (không có vỉa hè)
Guideline gap: Lề đường (shoulder) chưa được nhắc trong mục 5 → v2 thêm dòng "Lề sau vạch trắng liền: IGNORE".

---

CASE ID: EC05
Sample: BDD23 (blind)
Scene: Phố dân cư sau tuyết, mặt đường ướt, xe đỗ kín hai bên, người đi xe đạp ở giữa đường phía trước; tuyết còn trên vỉa hè trái; giọt nước trên kính.
Observation: Mặt đường nhìn rõ, không có tuyết trên làn chạy; vỉa hè trái lộ ra giữa gốc cây và hàng xe đỗ.
Decision: LABEL (làn chạy, vỉa hè trái) · IGNORE (xe đỗ, người đi xe đạp) · không ESCALATE
Expected: `direct_drivable` x1 giữa hai hàng xe đỗ, bo quanh người đi xe đạp; `sidewalk` ở phần vỉa hè trái nhìn thấy; không gắn tag `review_required`; không polygon drivable trên vỉa hè có tuyết. (Gold BDD23 d1–d5)
Rationale: Tuyết < 70% mặt đường nên không đủ điều kiện escalate (mục 6); người đi xe đạp là vật cản động phải chừa ra.
Common mistake: Gắn tag `severe_weather_snow_rain` chỉ vì thấy tuyết; vẽ polygon phủ qua người đi xe đạp; coi tuyết trên vỉa hè là mặt đường.
Diversity: critical · occlusion (xe đỗ, xe đạp) · low_visibility (tuyết, giọt mưa)

---

CASE ID: EC06
Sample: BDD01 (example)
Scene: Cao tốc 4+ làn, barrier bê tông hai bên, vùng sọc chéo (gore) ở lối rẽ ra bên phải.
Observation: Vùng sọc chéo cùng cao độ với mặt đường, không có vật cản vật lý; không có vỉa hè.
Decision: LABEL (các làn) · IGNORE (vùng sọc chéo, barrier)
Expected: Một `direct_drivable` và mỗi làn bên một `alternative_drivable`; mép phải dừng ở viền vùng sọc chéo; không vẽ lên barrier; không có `sidewalk`.
Rationale: Mục 5: dải phân cách mềm IGNORE.
Common mistake: Vẽ phủ vùng sọc chéo; tô barrier bê tông thành `sidewalk`.
Diversity: conflict (vạch sơn vs mặt đường liền) · negative (không có vỉa hè)
Guideline gap: Mục 9 mô tả BDD01 là "3 làn" — ảnh thật có ≥ 4 làn và vùng sọc chéo; cần sửa ví dụ.

---

CASE ID: EC07
Sample: BDD04 (calibration)
Scene: Phố dân cư dốc, hai chiều vạch vàng kép; bên phải là vỉa hè bê tông rộng với nhiều dốc lối xe vào garage, xe máy đỗ sát mép.
Observation: Tại các dốc garage, bó vỉa gần như bằng mặt đường.
Decision: LABEL (làn ego, vỉa hè kể cả dốc garage) · IGNORE (làn ngược chiều)
Expected: `direct_drivable` bên phải vạch vàng kép, mép phải tại chân bó vỉa; toàn bộ dải bê tông bên phải, kể cả dốc garage → `sidewalk`; không polygon drivable bên trái vạch vàng kép.
Rationale: Dốc garage vẫn nằm trên vỉa hè — không gian người đi bộ.
Common mistake: Kéo `direct_drivable` lên dốc garage; hoặc áp mục 1 "curb cut → IGNORE" và để trống đoạn dốc.
Diversity: critical · ambiguity (bó vỉa hạ cốt)
Guideline gap: Mục 1 chỉ IGNORE "curb cut / pedestrian ramp"; chưa nói dốc lối **xe** vào cắt qua vỉa hè → v2: dốc lối xe vào trên vỉa hè → `sidewalk`.

---

CASE ID: EC08
Sample: BDD10 (calibration)
Scene: Phố dân cư hai chiều, tim đường vàng **đứt**, làn xe đạp giữa vạch trắng và hàng xe đỗ hai bên; vỉa hè phía sau xe đỗ.
Observation: Làn ngược chiều chỉ tách bằng vạch vàng đứt; vỉa hè chỉ lộ ra từng đoạn giữa các xe.
Decision: LABEL (làn ego) · IGNORE (làn ngược chiều, làn xe đạp, vỉa hè bị che)
Expected: `direct_drivable` giữa tim đường vàng đứt và vạch trắng làn xe đạp bên phải; không `sidewalk` ở đoạn bị xe đỗ che.
Rationale: Không chuyển làn hợp pháp sang làn xe đạp; làn ngược chiều không phải `alternative_drivable`.
Common mistake: Gắn làn ngược chiều là `alternative_drivable` vì vạch đứt; phủ làn xe đạp; vẽ `sidewalk` xuyên qua hàng xe đỗ.
Diversity: ambiguity · conflict · occlusion
Guideline gap: Mục 1 chỉ IGNORE làn ngược chiều "bị ngăn bởi vạch vàng liền", mục 5 lại IGNORE vô điều kiện; làn xe đạp chưa có rule → v2: làn ngược chiều luôn IGNORE; làn xe đạp IGNORE.

---

CASE ID: EC09
Sample: BDD11 (calibration)
Scene: Khu dân cư, xe ego sắp qua giao lộ có vạch đi bộ; sau giao lộ là vạch vàng kép, xe đỗ hai bên, người đi bộ bước xuống vạch đi bộ ở góc phải.
Observation: Người đi bộ đang ở mép vạch đi bộ, sát góc vỉa hè.
Decision: LABEL (làn ego, vỉa hè góc phố) · IGNORE (người đi bộ, làn ngược chiều)
Expected: `direct_drivable` phủ trùm vạch đi bộ nhưng bo quanh chân người đi bộ; `sidewalk` ở hai góc phố, không chồng lên drivable.
Rationale: Mục 3 Visible Surface: người đi bộ được chừa ra; vỉa hè góc phố là nơi người đi bộ chờ.
Common mistake: Vẽ trùm qua người đi bộ (rule amodal cũ); tô cả vạch đi bộ thành `sidewalk`.
Diversity: critical (người đi bộ) · crosswalk

---

CASE ID: EC10
Sample: BDD20 (calibration)
Scene: Đường khu dân cư, vạch vàng kép, vạch trắng liền hai bên, xe đỗ và cọc tiêu bên phải, dải cỏ rồi vỉa hè; giá đỡ điện thoại che đáy ảnh.
Observation: Giữa vạch trắng liền và hàng xe đỗ là dải đỗ xe; giữa mặt đường và vỉa hè có dải cỏ.
Decision: LABEL (làn ego, vỉa hè) · IGNORE (làn ngược chiều, dải cỏ, vùng sau giá đỡ)
Expected: `direct_drivable` giữa vạch vàng kép và vạch trắng liền phải, dừng tại rìa giá đỡ; `sidewalk` chỉ phủ dải vỉa hè, không phủ dải cỏ.
Rationale: Dải cỏ không phải vỉa hè; vùng bị che không có bằng chứng.
Common mistake: Gộp dải cỏ vào `sidewalk`; kéo `direct_drivable` qua vạch trắng liền tới hàng xe đỗ.
Diversity: occlusion (vật che trong xe) · conflict
Guideline gap: Mục 3 Spatial Rule nói xe ego đỗ trên làn đỗ thì làn đỗ là direct, nhưng chưa nói làn đỗ khi xe ego **đang chạy** ở làn chính → v2 cần: làn đỗ sau vạch trắng liền → IGNORE.

---

CASE ID: EC11
Sample: BDD24 (calibration)
Scene: Phố đô thị sau tuyết, đống tuyết lớn giữa lòng đường bên phải, rào sắt và tuyết trên vỉa hè trái, người đi bộ trên vỉa hè phải.
Observation: Tuyết không phủ kín đường (< 70%) nhưng đống tuyết chắn một phần làn.
Decision: LABEL (mặt đường trống, vỉa hè) · IGNORE (đống tuyết)
Expected: Polygon drivable dừng tại chân đống tuyết; vỉa hè nhìn thấy → `sidewalk`; không gắn tag `review_required`.
Rationale: Đống tuyết là vật cản cố định, tương tự hàng cọc tiêu (mục 5); đường vẫn nhìn thấy rõ.
Common mistake: Gắn tag `severe_weather_snow_rain` cho cả ảnh; vẽ phủ qua đống tuyết.
Diversity: low_visibility · occlusion (vật cản trên đường)
Guideline gap: Mục 6 chỉ nói tuyết phủ mặt đường, chưa nói đống tuyết là vật cản → thêm vào v2.

---

CASE ID: EC12
Sample: BDD17 (calibration)
Scene: Phố đô thị trời mưa, giọt nước trên kính, giá đỡ điện thoại che góc trái dưới; vỉa hè phải có xe đạp dựng.
Observation: Mặt đường và bó vỉa phải vẫn đọc được qua giọt mưa; góc trái bị che kín.
Decision: LABEL (vùng nhìn thấy) · IGNORE (vùng sau giá đỡ)
Expected: Polygon drivable và `sidewalk` phải vẽ bình thường qua giọt mưa, dừng tại rìa giá đỡ; không `needs_review`, không tag.
Rationale: Mục 6: giọt mưa không đổi quyết định nếu ranh giới vẫn thấy.
Common mistake: Tick `needs_review` hoặc gắn tag chỉ vì trời mưa.
Diversity: occlusion · low_visibility

---
