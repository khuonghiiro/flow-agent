# Quy Tắc Chuẩn: Tạo Animation Sprite Sheet Làm Tài Nguyên Phim (VFX Assets)

Tài liệu này là quy tắc bắt buộc áp dụng khi nhận yêu cầu tạo ảnh **Animation Frame / Sprite Sheet / Asset VFX** phục vụ làm phim hoạt hình 2D, game engine hoặc compositing hậu kỳ.

---

## 1. Định dạng tiêu chuẩn: Lưới 1:1 với 16 Frames (Standard 4x4 Grid)

- **Tỉ lệ ảnh tổng thể**: `1:1` (`IMAGE_ASPECT_RATIO_SQUARE`).
- **Bố cục lưới**: Chính xác 16 frames riêng biệt, xếp thành 4 hàng × 4 cột (thứ tự đọc: từ trái sang phải, từ trên xuống dưới).
- **Phân bổ không gian**: Mỗi frame chiếm 1 ô ảo 1/4 × 1/4 của bức ảnh.

---

## 2. Quy Tắc Chọn Màu Nền Thông Minh (Adaptive Chroma Background Selection)

> ⚠️ **LỖI CỐT LÕI CẦN TRÁNH**: Màu nền bị **tương đồng hoặc hòa lẫn với chi tiết trong frame** (ví dụ: hiệu ứng phát sáng lấp lánh có quầng tán sắc tím/hồng mà lại dùng nền Magenta `#FF00FF` dẫn đến khi tách phông sẽ bị ăn lẹm chi tiết, hoặc dính viền hồng lem nhem quanh tia sáng).

### Bảng Ma Trận Chọn Màu Nền Theo Chủng Loại Hiệu Ứng:

| Loại Hiệu Ứng | Bảng Màu Hiệu Ứng | Màu Nền Tối Ưu Được Chọn | Lý Do Kỹ Thuật Tách Phông |
|---|---|---|---|
| **Ánh sáng quang học, Tia Lấp Lánh (Sparkle, Lens Flare, Glint, Twinkle)** | Trắng, Vàng kim (Gold), Cyan, Xanh ngọc | **Pure Chroma Green `#00FF00`** HOẶC **Pure Pitch Black `#000000`** | - Nền Green `#00FF00`: Tương phản tuyệt đối với tia sáng vàng/trắng/cyan, không bị dính sắc tố đỏ/tím.<br>- Nền Black `#000000`: Chuẩn VFX Additive Blending (`Screen` / `Add`), tự động trong suốt trong Premiere/AE/Unity, giữ trọn vẹn hào quang mờ và bụi sáng mịn 100%. |
| **Lửa, Dung nham, Năng lượng nhiệt** | Đỏ, Cam, Vàng, Hồng | **Pure Chroma Green `#00FF00`** hoặc **Pure Deep Blue `#0000FF`** | Tuyệt đối **CẤM dùng Magenta `#FF00FF`** vì Magenta chứa kênh Đỏ (R=255) sẽ ăn đứt viền lửa khi keying. |
| **Sét điện, Băng giá, Nước, Phép thuật xanh** | Xanh dương, Cyan, Trắng tinh | **Pure Magenta `#FF00FF`** hoặc **Pure Chroma Green `#00FF00`** | Magenta tách biệt hoàn toàn với dải màu Cyan/Blue. |
| **Chất độc, Axit, Cây cỏ, Phép thuật mộc** | Xanh lá cây, Lục bảo | **Pure Magenta `#FF00FF`** | Tuyệt đối **CẤM dùng Green `#00FF00`** vì sẽ làm biến mất chi tiết hiệu ứng. |

### Nguyên tắc bất biến về màu nền:
1. **Nền phẳng tuyệt đối (Pure Solid Flat)**: Chỉ duy nhất 1 mã màu đồng nhất, không gradient, không bóng đổ (drop shadow), không vignette, không vết loang.
2. **Khóa tràn màu (Zero Color Spill / Zero Bloom Bleed)**: Hiệu ứng phát sáng không được làm lem màu hào quang hòa trộn vào nền. Ranh giới giữa ánh sáng và màu nền phải trong trẻo, sắc nét.
3. **Cấm dùng màu nền trên chủ thể**: Nếu chọn nền Green `#00FF00`, tuyệt đối không dùng sắc xanh lá trên hiệu ứng. Nếu chọn nền Black `#000000`, hiệu ứng phải là ánh sáng tự thân rực rỡ, không vẽ khói đen hay bóng xám.

---

## 3. Quy Tắc Giới Hạn Bounding Box & Đệm An Toàn (STRICT BBOX CONTAINMENT)

1. **Hiệu ứng phải nằm trọn vẹn 100% bên trong Bounding Box của từng ô**:
   - Ngay cả ở frame có năng lượng bùng nổ lớn nhất (vòng shockwave lan rộng nhất, tia sáng lóe to nhất), toàn bộ hình ảnh phải **thu nhỏ vừa vặn (scaled down)** để nằm gọn trong tâm ô.
   - **Khoảng đệm an toàn tối thiểu 20-25% (Safety Margin / Padding)**: Xung quanh 4 phía (trên, dưới, trái, phải) của mỗi frame luôn luôn phải có khoảng trống màu nền bao bọc, tuyệt đối không có hạt hay tia sáng nào chạm tới hoặc tiến sát ranh giới ô.
2. **CẤM TUYỆT ĐỐI hiện tượng cắt mép (Zero Edge-Clipping)**:
   - Các vòng shockwave, tia sao, cánh hào quang phải tròn trịa, tự nhiên, không bao giờ được có đường cắt thẳng cụt cạnh do chạm mép ô.
   - Không được để hiệu ứng của ô này lem sang ô bên cạnh.

---

## 4. Quy Tắc CẤM Viền Khung & Phối Cảnh Tiếp Đất (ZERO BORDERS)

1. **TUYỆT ĐỐI CẤM MỌI DẠNG ĐƯỜNG KẺ & VIỀN Ô (NO BORDERS / NO GRID LINES)**:
   - `Strictly NO grid lines, NO black frame borders, NO box outlines, NO cell dividers, NO separator lines, NO numbers, NO text`.
   - Toàn bộ khoảng cách giữa các frame là **màu nền thuần chất, liền mạch và liên tục**.
2. **TUYỆT ĐỐI CẤM ĐƯỜNG KẺ MẶT ĐẤT (NO GROUND LINE / NO HORIZON)**:
   - Cấm vẽ đường chân trời, vạch kẻ ngang đáy, sàn nhà, cỏ, đá hay vết nứt cố định.
   - **Minh họa vật lý tiếp đất theo phối cảnh elip (Perspective Ground Contact Ellipse)**: Khi hiệu ứng có tương tác mặt đất, vòng shockwave hoặc tia tiếp đất phải được vẽ theo dạng **hình elip dẹp nằm ngang theo luật phối cảnh 3D**, thể hiện rõ chiều sâu vật lý mà không cần vẽ vạch kẻ đất.

---

## 5. Quy Tắc Nhân Quả Thời Gian 16 Frames (Strict Temporal Causality)

- **Frames 1 - 3 (Khởi đầu)**: Xuất hiện điểm sáng/năng lượng ban đầu, vùng tiếp xúc hoàn toàn sạch sẽ.
- **Frames 4 - 6 (Mở rộng)**: Tia sáng/năng lượng nở dần, đạt độ rõ nét.
- **Frames 7 - 9 (Cực đại - Peak Brilliance/Impact)**: Đạt đỉnh điểm chói sáng/bùng nổ, vẫn nằm trọn trong vùng an toàn (safe area).
- **Frames 10 - 12 (Phân rã - Fracture/Decay)**: Thu nhỏ, đứt gãy thành các hạt bụi sáng/tia nhỏ tản ra ngoài.
- **Frames 13 - 16 (Tàn lụi & Tan biến - Clean Fadeout)**: Mờ dần và tan biến sạch sẽ 100% vào nền, không để lại đốm bẩn hay vệt đen.
