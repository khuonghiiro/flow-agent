# Quy Tắc Tạo Ảnh Frame Animate / Sprite Sheet Làm Tài Nguyên Phim (VFX Assets)

Tài liệu này là quy tắc bắt buộc áp dụng khi nhận yêu cầu tạo ảnh **Animation Frame / Sprite Sheet / Asset VFX** phục vụ làm phim, game 2D hoặc cắt ghép hậu kỳ (compositing).

---

## 1. Tỉ lệ khung hình & Bố cục lưới linh hoạt theo hướng chuyển động (Aspect Ratio & Grid Topology)

Tuyệt đối không mặc định dùng tỉ lệ `1:1` cho mọi loại hoạt ảnh. Phải chọn tỉ lệ ảnh và bố cục hàng/cột sao cho **từng ô con (cell)** khớp với quỹ đạo và hình dáng của đối tượng:

| Loại chuyển động & Đối tượng | Tỉ lệ ảnh tổng thể | Bố cục lưới đề xuất | Tỉ lệ từng ô con (Cell) |
|---|---|---|---|
| **Chuyển động dọc / Dài cao** (Tia sét giáng từ trời, cột lửa, tia laser vệ tinh, thác nước, giọt rơi) | `16:9` (Landscape) hoặc `9:16` (Portrait) | **16:9**: 2 hàng x 4 cột (8 frames) hoặc 2 hàng x 6 cột (12 frames)<br>**9:16**: 4 hàng x 2 cột (8 frames) | Khung hình chữ nhật dọc cao, cho phép tia sét/cột năng lượng vươn dài hùng vĩ mà không bị lùn ép |
| **Chuyển động ngang / Quét dài** (Kiếm khí chém ngang, đạn pháo, phi tiêu, lốc xoáy lướt ngang) | `16:9` (Landscape) | 4 hàng x 2 cột hoặc 3 hàng x 3 cột | Khung hình chữ nhật rộng bề ngang |
| **Tỏa tròn / Đối xứng tâm** (Đốm lửa trại, vụ nổ tròn, khiên bảo vệ ma thuật, vòng xoáy portal) | `1:1` (Square) | 4 hàng x 4 cột (16 frames) hoặc 3 hàng x 3 cột (9 frames) | Khung vuông cân xứng 1:1 |

---

## 2. Quy tắc cô lập nền (Chroma Key & Isolation)

Mục đích của asset là để diễn hoạt và chèn vào cảnh quay bất kỳ:
1. **TUYỆT ĐỐI KHÔNG VẼ ĐƯỜNG KẺ MẶT ĐẤT (NO GROUND LINE / NO HORIZON)**:
   - Không được vẽ đường chân trời, vạch kẻ ngang, mặt cỏ, sàn đá hay bất kỳ ranh giới tiếp xúc nào.
   - Nếu có va chạm (impact), chỉ vẽ **năng lượng va chạm phát sáng** (sóng xung kích năng lượng - shockwave ring, tia điện phụ - electric arcs, tia lửa bắn - sparks, bụi khói phát sáng). Khi tách nền, chỉ các hạt năng lượng này hiển thị trên mặt đất của cảnh phim thực tế.
2. **Không có lưới chia ô hay số đếm**:
   - `No grid lines, no cell borders, no frame divider lines, no numbers, no text labels`.
3. **Nền phẳng tuyệt đối (Chroma Key Flat Background)**:
   - Nền chuẩn màu: Pure solid flat magenta `#FF00FF` (hoặc pure bright lime green `#00FF00` nếu chủ thể có màu tím/hồng).
   - `No gradient, no vignette, no drop shadow, no ambient occlusion, no light bloom or glow bleeding onto the background`.
4. **Khoảng cách đệm giữa các ô (Margin & Clearance)**:
   - Mỗi hình vẽ phải được căn giữa ô của chính nó với khoảng trống an toàn rộng rãi (`generous empty space between frames`).
   - Mọi tia lửa, mảnh vỡ, tàn tro phải nằm trọn vẹn bên trong ô, không được chạm mép hay lem sang ô bên cạnh.

---

## 3. Tính logic về thời gian và sự kiện (Temporal Progression & Consistency)

Tất cả các frame phải tuân thủ nghiêm ngặt tính nhân quả theo thời gian:
1. **Giai đoạn trước va chạm (Anticipation / Traveling phase)**:
   - Ở các frame đầu khi tia năng lượng/vật thể đang di chuyển từ trên xuống hoặc chưa tới đích: **Mặt đáy của frame phải hoàn toàn sạch sẽ, chỉ có màu nền phẳng**.
   - Tuyệt đối **không vẽ trước** vết nứt, vết cháy, hay tia nổ ở dưới đất khi vật thể chưa chạm tới đất.
2. **Giai đoạn va chạm bùng nổ (Peak Impact frame)**:
   - Tia sét/vật thể chạm tới mặt phẳng tiếp xúc ➔ bùng phát năng lượng chói sáng cực đại, tia lửa điện và vòng shockwave lan tỏa.
3. **Giai đoạn phân rã & tan biến (Dissipation / Fade phase)**:
   - Năng lượng nứt nhỏ, mờ dần, các tia lửa tản rộng và biến mất hoàn toàn vào hư không (`dissipate cleanly to clear background`).
   - Không để lại vết nhơ đen hoặc vệt bẩn vĩnh viễn ở các frame cuối (trừ khi yêu cầu rõ là decal cháy).
4. **Đồng nhất góc nhìn (Camera & Scale Consistency)**:
   - Giữ nguyên 100% tiêu cự, góc máy camera, tỉ lệ phóng to/thu nhỏ (scale) và vị trí tâm va chạm qua tất cả các frame.

---

## 4. Cấu trúc Prompt chuẩn mực khi tạo Frame Animate

Mỗi prompt tạo Animation Sprite Sheet phải gồm đầy đủ 5 phần rõ ràng:
1. **Format & Grid Specification**: Tỉ lệ ảnh (`16:9` / `9:16` / `1:1`), số lượng frame chính xác, số hàng x số cột, khoảng cách đệm, không lưới, không vạch kẻ.
2. **Background & Color Isolation**: Nền màu chuẩn chroma key `#FF00FF`, không gradient, không kẻ đất, cấm dùng màu nền trên chủ thể.
3. **Subject & Art Style**: Phong cách vẽ (2D hand-drawn, cel shading, anime VFX), bảng màu chủ thể, viền nét sạch (clean sharp outlines).
4. **Temporal Progression (Mô tả chi tiết từng nhóm frame)**:
   - Frame bắt đầu: chưa chạm đất, đáy hoàn toàn sạch.
   - Frame va chạm: bùng nổ năng lượng, tia điện hồ quang tỏa tròn.
   - Frame phân rã: năng lượng tắt dần, tia lửa tan biến sạch sẽ.
5. **Strict Prohibitions**:
   - `CRITICAL: Do NOT draw any horizontal ground line, floor line, horizon separator, or surface border.`
   - `CRITICAL: Do NOT draw pre-existing impact crater or scorch marks before the strike occurs.`
