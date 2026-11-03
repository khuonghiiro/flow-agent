# Quy Tắc Chuẩn: Tạo Animation Sprite Sheet Làm Tài Nguyên Phim (VFX Assets)

Tài liệu này là quy tắc bắt buộc áp dụng khi nhận yêu cầu tạo ảnh **Animation Frame / Sprite Sheet / Asset VFX** phục vụ làm phim hoạt hình 2D, game engine hoặc compositing hậu kỳ.

---

## 1. Định dạng tiêu chuẩn: Lưới 1:1 với 16 Frames (Standard 4x4 Grid)

- **Tỉ lệ ảnh tổng thể**: `1:1` (`IMAGE_ASPECT_RATIO_SQUARE`).
- **Bố cục lưới**: Chính xác 16 frames riêng biệt, xếp thành 4 hàng × 4 cột (thứ tự đọc: từ trái sang phải, từ trên xuống dưới).
- **Phân bổ không gian**: Mỗi frame chiếm 1 vùng không gian 1/4 × 1/4 của bức ảnh, trôi nổi tự do trên nền phẳng liền mạch.

---

## 2. Quy Tắc Chọn Màu Nền Linh Hoạt Không Trùng Chi Tiết (Flexible Non-Overlapping Solid Background)

> 💡 **NGUYÊN TẮC CỐT LÕI**: Màu nền **KHÔNG bắt buộc phải là Chroma Green `#00FF00` hay Magenta `#FF00FF`**. Bạn được phép chọn **bất kỳ màu nền phẳng đơn sắc nào** (Đen tuyền `#000000`, Xám than `#1A1A1A`, Xanh đêm `#080C18`, Nâu sẫm, Đỏ đô, Xanh lá, Tím, v.v.), **MIỄN LÀ màu nền đó tuyệt đối KHÔNG TRÙNG hoặc gần giống với bất kỳ màu sắc/chi tiết nào của hiệu ứng**.

### Gợi Ý Chọn Màu Nền Tương Phản Tối Ưu:

| Chủng Loại & Màu Sắc Chi Tiết Hiệu Ứng | Màu Nền Phẳng Đề Xuất (Tự Do Linh Hoạt) | Điều Kiện Cấm (Không Trùng Màu Chi Tiết) |
|---|---|---|
| **Hiệu ứng phát sáng tự thân, Hào quang, Cổng năng lượng, Sét, Phép thuật (Cyan, Xanh dương, Vàng kim, Trắng, Tím, Lửa)** | **Đen tuyền (`#000000`)**, **Than chì tối (`#121212`)**, hoặc màu đơn sắc đối lập bất kỳ (`#2A0800`, `#00FF00`, `#FF00FF`) | - Nếu hiệu ứng có khói đen/xám tối → Không dùng nền Đen/Xám.<br>- Nếu hiệu ứng có tia tím/hồng → Không dùng nền Tím/Hồng.<br>- Nếu hiệu ứng có màu xanh lá → Không dùng nền Xanh lá. |
| **Hiệu ứng Lửa, Vụ nổ nhiệt, Nham thạch (Đỏ, Cam, Vàng, Khói xám)** | **Xanh dương đậm (`#001A4D`)**, **Xanh ngọc tối (`#002B36`)**, hoặc **Xanh lá (`#00FF00`)** | Cấm dùng nền Đỏ, Cam, Hồng, Tím hoặc Xám trùng màu khói. |
| **Hiệu ứng Băng, Nước, Sét xanh, Cổng không gian Cyan (Xanh lam, Cyan, Trắng)** | **Đen tuyền (`#000000`)**, **Đỏ rượu tối (`#2B0015`)**, **Tím (`#FF00FF`)**, hoặc **Vàng đất sẫm** | Cấm dùng nền Xanh dương, Xanh ngọc, Trắng. |

### 3 Nguyên tắc bất biến về màu nền:
1. **Nền phẳng tuyệt đối (100% Solid Flat Uniform Canvas)**: Chỉ duy nhất 1 mã màu đồng nhất trên toàn bộ ảnh, không gradient, không bóng đổ, không vignette.
2. **Không trùng màu chi tiết (Zero Color Overlap)**: Màu nền phải tách biệt 100% với bảng màu của hiệu ứng để dễ dàng tách nền (keying / luma matte / screen blending).
3. **Đồng nhất màu nền trong cùng một chuỗi hiệu ứng nối tiếp**: Nếu một hiệu ứng gồm nhiều tấm ảnh nối tiếp nhau (Tấm 1 -> Tấm 2 -> Tấm 3), tất cả các tấm trong chuỗi đó phải dùng chung **cùng 1 mã màu nền**.

---

## 3. Quy Tắc Tạo Độ Mềm Dẻo Động Học & "Hiệu Ứng Bóng Mờ Chuyển Động" (SILKY MOTION-BLUR AFTERIMAGE & FLUID KINETIC RULES)

> ⚠️ **3 LỖI KHIẾN VÒNG XOÁY BỊ CỨNG NGẮC, KHÔ KHỐC HOẶC ĐƠ GIẬT**:
> 1. **Lỗi "Thiếu Hiệu Ứng Bóng Mờ Chuyển Động" (`Missing Soft Motion-Blur Shadow / Hard Chalky Lines`)**: Nếu chỉ vẽ các nét xoáy sáng đặc, sắc cạnh mà **thiếu lớp bóng mờ bán trong suốt (`soft translucent motion-blur shadow / feathered afterimage trail / diffused misty glow`)** hoặc tự nhiên làm mất lớp bóng mờ ở một số frame (ví dụ Frame 6, 15, 16 tự nhiên mất hào quang mờ chỉ còn cái vòng trắng cứng), thì khi xoay nhìn sẽ **rất cứng, khô khốc, thiếu độ mềm dẻo uyển chuyển**!
> 2. **Lỗi "Bóng Mờ Đứng Yên Không Cuốn Theo Xoáy" (`Frozen Static Backdrop Shadow`)**: Có vẽ lớp bóng mờ hào quang phía sau nhưng lại vẽ nó đứng im thẳng đơ như bức tường tĩnh ở cả 16 frames, không uốn lượn theo chiều xoay.
> 3. **Lỗi "Kẹt Góc Xoay Giữa Các Frame Cùng Hàng" (`Stuck / Clustered Angles Across Frames`)**: Nhiều frame liên tiếp có vệt sáng kẹt ở cùng 1 góc rồi nhảy cóc sang góc khác.

Để 16 frames ghép lại thành chuyển động **vừa xoay mượt mà, vừa mềm dẻo uyển chuyển như lụa/nước phát sáng**, prompt **BẮT BUỘC** phải áp dụng **5 Quy Luật "Bóng Mờ Động Học Mềm Dẻo" (`5 Laws of Silky Motion-Blur Fluidity`)**:

1. **BẮT BUỘC Có Lớp "Bóng Mờ Dư Ảnh & Hào Quang Sương Mờ" Ở CẢ 16/16 FRAMES (`Mandatory Soft Motion-Blur Afterimage & Diffused Mist Glow in All 16 Frames`)**:
   - Mọi dải năng lượng xoáy **BẮT BUỘC phải có cấu trúc chuyển sắc 3 lớp mềm mại (Soft 3-Stage Feathering)**:
     1. **Lõi lụa sáng (`Silky Bright Core`)**: Nét uốn mềm, vuốt nhọn hai đầu, không bao giờ vẽ thành vành khung trắng đặc cứng đờ.
     2. **Đuôi bóng mờ dư ảnh chuyển động (`Soft Translucent Motion-Blur Afterimage / Ghosting Smear Trail`)**: Ngay phía sau mỗi cánh xoáy sáng phải kéo dài một **dải bóng mờ bán trong suốt màu xanh lam/cyan trầm (`semi-transparent deep cyan-blue motion-blur shadow trail`)** nhòe mềm theo quỹ đạo xoay, giúp nối liền khoảng cách thị giác giữa frame trước và frame sau.
     3. **Hào quang sương mờ cuốn xoáy (`Swirling Diffused Volumetric Mist`)**: Phía trên và bao quanh vòng xoáy phải luôn có lớp **bóng mờ hào quang khuếch tán mềm mịn như sương khói phát sáng (`soft airbrushed volumetric glow and translucent rising mist`)** hiện diện **đồng nhất xuyên suốt từ Frame 1 đến Frame 16** (tuyệt đối CẤM để các frame cuối như Frame 15–16 bị mất lớp bóng mờ này!).
2. **Lớp Bóng Mờ Phải Uốn Cong Mềm Dẻo Theo Lực Xoáy (`Curved Swirling Shadow — Never Static Vertical, Never Rigid`)**:
   - Lớp bóng mờ và làn sương sáng bốc lên từ cổng **không được dựng thẳng đứng bất động**, mà phải **nghiêng lượn, uốn cong chữ S và cuộn xoắn mềm mại theo đúng chiều gió lốc của vòng xoáy ở từng frame (`softly curving, leaning, and swirling clockwise with the vortex momentum like silk veil in water`)**.
3. **Cấm Vẽ Vành Khung Elip Đặc Cứng (`NO Hard-Edged Solid Chalky Ring`)**:
   - Tuyệt đối không vẽ một đường viền elip trắng đặc khép kín cứng nhắc như cái đĩa nhựa. Toàn bộ vòng xoáy phải mềm mại như những dải lụa khói ánh sáng đan quyện vào nhau với biên mờ mịn (`soft feathered airbrushed edges`).
4. **Chỉ Định Góc Quay Đều Đặn Từng Frame (`Explicit 22.5° Clockwise Step Per Frame`)**:
   - Các dải lụa sáng và đuôi bóng mờ phía sau chúng phải nhích đều `22.5°` theo chiều kim đồng hồ ở mỗi frame kế tiếp (`Frame 1 at 0°, Frame 2 at 22.5°, Frame 3 at 45°... Frame 16 at 337.5°`), không để 2 frame liên tiếp bị trùng góc.
5. **Biến Đổi Hình Thái Liên Tục Đối Với Hiệu Ứng Nổ / Sét (`Continuous Morphological Evolution with Soft Glow Afterimage`)**:
   - Ngay cả với tia sét hay vụ nổ, xung quanh lõi sét/lõi nổ cũng phải có **quầng bóng mờ hào quang mềm (`soft diffused plasma bloom & fading afterimage shadow`)** để chuyển động nổ và giật sét có độ êm và chiều sâu quang học.

---

## 4. Quy Tắc Chuỗi Hoạt Ảnh Nối Tiếp Đa Tấm (MULTI-SHEET SEQUENTIAL CHAINING ARCHITECTURE)

> ⚠️ **LỖI CỐT LÕI KHI NHỒI NHÉT VÀO 1 TẤM DUY NHẤT**:
> Một tấm Sprite Sheet chỉ có **16 frames** (tương đương ~0.5 – 0.6 giây ở 24–30fps). Nếu nhồi nhét cả **Mở cổng + Xoáy duy trì + Đóng cổng** (hoặc **Sét đánh xuống + Vụ nổ mặt đất + Khói tàn**) vào chung 1 tấm 16 frames thì mỗi giai đoạn chỉ có 3–4 frames → chuyển động bị **nhảy bước đột ngột (steppy), thiếu hụt giai đoạn duy trì, và bị ngắt cụt**!

Vì vậy, khi tạo hiệu ứng có vòng đời đầy đủ hoặc có chuỗi nhân quả liên hoàn, **BẮT BUỘC phải chia thành CHUỖI CÁC TẤM SPRITE SHEET NỐI TIẾP NHAU (`Multi-Sheet Sequential Chain`)**, trong đó **Frame 16 của Tấm trước khớp nối trực tiếp với Frame 1 của Tấm sau**:

### 4.1. Mẫu Chuỗi 1: Hiệu Ứng Cổng Dịch Chuyển / Vòng Ma Pháp / Khiên Năng Lượng (Chuỗi 3 Tấm Nối Tiếp — 48 Frames)

| Thứ Tự Tấm (Sheet) | Vai Trò Trong Chuỗi Hoạt Ảnh | Diễn Biến Chi Tiết Xuyên Suốt 16 Frames Của Tấm Đó | Quy Tắc Khớp Nối Giữa Các Tấm (Continuity Handshake) |
|---|---|---|---|
| **Tấm 1 (`Sheet 1: Opening / Spawn`)** | **Pha Khởi Tạo & Bung Mở Cổng** (16 frames chỉ dành riêng cho việc mở cổng mượt mà) | - **Frames 1–4**: Điểm sáng nứt ra thành khe năng lượng nhỏ, bắt đầu xoay và bắn tia.<br>- **Frames 5–12**: Vòng xoáy mở rộng dần từng nấc nhỏ mượt mà (vừa nở rộng vừa xoay góc `+22.5°/frame`, các dải plasma vặn xoắn mạnh).<br>- **Frames 13–16**: Cổng đạt **100% kích thước mở cực đại (Full Aperture)**, vòng xoáy cuộn tròn ổn định. | **TUYỆT ĐỐI KHÔNG đóng cổng ở Tấm 1!** Frame 16 kết thúc ở trạng thái **Cổng đang mở to 100%** để truyền thẳng sang Frame 1 của Tấm 2. |
| **Tấm 2 (`Sheet 2: Active Spinning Loop`)** | **Pha Duy Trì Xoáy Liên Tục Vô Tận** (16 frames Seamless Loop ở kích thước mở 100%) | - **Tất cả 16 frames (Frames 1–16)** đều giữ nguyên **kích thước mở 100% (Full Aperture)** từ Frame 16 của Tấm 1 (không phóng to, không thu nhỏ!).<br>- Cánh xoáy plasma bên trong, các lưỡi năng lượng quanh viền và các tia sáng **xoay tròn liên tục 1 vòng 360° qua 16 frames** (`Frame 1 = 0°`, `Frame 5 = 90°`, `Frame 9 = 180°`, `Frame 13 = 270°`, `Frame 16 = 337.5°` nối khít về `Frame 1`). | Khớp hoàn hảo với cuối Tấm 1 và đầu Tấm 3. Có thể lặp (loop) Tấm 2 vô hạn lần trong phim/game mà không bao giờ bị khựng hay ngắt nhịp! |
| **Tấm 3 (`Sheet 3: Collapse / Closure`)** | **Pha Thu Hẹp & Đóng Cổng Tan Biến** (16 frames chỉ dành riêng cho việc đóng cổng mượt mà) | - **Frame 1**: Bắt đầu ngay ở trạng thái **Cổng đang mở to 100% (Full Aperture)** nối tiếp từ Tấm 2.<br>- **Frames 2–11**: Vòng xoáy hút ngược vào tâm, tốc độ xoay cuốn nhanh dần, kích thước thu hẹp mượt mà từng nấc nhỏ.<br>- **Frames 12–15**: Khép sập lại thành vệt chớp sáng lóe lên và bung các hạt bụi sáng tản ra.<br>- **Frame 16**: Tan biến hoàn toàn 100% về màu nền phẳng. | Nối tiếp mượt mà ngay sau khi kết thúc vòng lặp của Tấm 2. |

### 4.2. Mẫu Chuỗi 2: Hiệu Ứng Tác Động Liên Hoàn Nhân Quả (Ví dụ: Sét Đánh Xuống Đất → Nổ Xung Kích Mặt Đất — Chuỗi 2 Tấm Nối Tiếp)

| Thứ Tự Tấm (Sheet) | Vai Trò Trong Chuỗi Hoạt Ảnh | Diễn Biến Chi Tiết Xuyên Suốt 16 Frames Của Tấm Đó | Quy Tắc Khớp Nối Giữa Các Tấm (Continuity Handshake) |
|---|---|---|---|
| **Tấm 1 (`Sheet 1: Strike / Descent & Contact`)** | **Pha Giáng Đòn & Chạm Mặt Đất** (16 frames) | - **Frames 1–8**: Tia sét xé toạc từ trên cao lao xuống nhanh dần qua từng frame (phần đáy vẫn trống).<br>- **Frames 9–12**: Đầu tia sét chạm điểm tiếp đất, lóe lên tia lửa điện đầu tiên.<br>- **Frames 13–16**: Luồng sét chính dồn toàn bộ năng lượng xuống điểm chạm, nén thành một **quả cầu plasma điện quang chói lòa trên mặt đất (elip ngang)** chuẩn bị phát nổ. | **Frame 16 của Tấm 1** kết thúc ở trạng thái **Lõi cầu sét nén cực đại tại điểm tiếp đất** để truyền sang Tấm 2 phát nổ. |
| **Tấm 2 (`Sheet 2: Ground Explosion & Shockwave Decay`)** | **Pha Bùng Nổ Mặt Đất, Sóng Xung Kích & Dư Chấn** (16 frames) | - **Frames 1–4**: Nối tiếp ngay từ lõi cầu sét ở cuối Tấm 1 → phát nổ bung thành vòm hồ quang điện và vòng sóng xung kích elip ngang (`4:1`) đầu tiên.<br>- **Frames 5–10**: Vòng sóng xung kích elip ngang lan rộng ra 2 bên trên mặt sàn vô hình, các nhánh sét con nhảy múa dữ dội bắn ngược lên trên.<br>- **Frames 11–15**: Vòm nổ phân rã thành các tia hồ quang đứt đoạn và hạt điện tích lơ lửng.<br>- **Frame 16**: Tan biến sạch sẽ 100% vào màu nền. | Nối tiếp trực tiếp cú đánh của Tấm 1, tạo thành chuỗi *"Sét đánh → Nổ tung mặt đất"* hoàn chỉnh 32 frames. |

---

## 5. Quy Tắc Giới Hạn Bounding Box, Đệm An Toàn & CẤM Viền Khung (STRICT BBOX & ZERO BORDERS)

1. **Hiệu ứng nằm trọn vẹn 100% bên trong vùng an toàn của từng frame**:
   - **Khoảng đệm an toàn tối thiểu 25-30% (Safety Margin / Padding)** quanh mỗi frame.
   - **CẤM TUYỆT ĐỐI hiện tượng cắt mép (Zero Edge-Clipping)**: Tia sáng phản chiếu phải là tia ngắn gọn (`short, compact tapering rays fading out smoothly`) để không chạm vào frame bên cạnh.
2. **TUYỆT ĐỐI CẤM MỌI DẠNG ĐƯỜNG KẺ & VIỀN Ô (NO BORDERS / NO GRID LINES)**:
   - `Strictly NO grid lines, NO black lines, NO borders, NO divider lines, NO box outlines, NO separator lines, NO numbers, NO text. All 16 frames float freely in open space on one single seamless unbroken flat background`.
   - **CẤM DÙNG TỪ `"cell"` / `"each cell"` / `"per cell"` / `"grid box"` TRONG PROMPT**: Từ `"cell"` kích hoạt AI vẽ khung kẻ ô bàn cờ! Bắt buộc dùng `"around every isolated frame"` hoặc `"floating freely in open space"`.
3. **TUYỆT ĐỐI CẤM ĐƯỜNG KẺ MẶT ĐẤT HOẶC BỐI CẢNH (NO GROUND LINE / NO HORIZON / NO PROPS)**:
   - Cấm vẽ đường chân trời, vạch kẻ ngang đáy, sàn nhà, cỏ, đá, tường.

---

## 6. Quy Tắc Phối Cảnh & Phản Chiếu Theo Góc Nhìn Mắt Người Cho 5 Hướng Không Gian (HUMAN EYE-LEVEL PERSPECTIVE & OPTICAL REFLECTION RULES)

> ⚠️ **CHỐNG TRÔI GÓC NHÌN ("ANTI-PERSPECTIVE DRIFT")**:
> Ở tất cả các tấm (Tấm 1 Mở, Tấm 2 Duy trì xoáy, Tấm 3 Đóng) và ở tất cả 16 frames trong mỗi tấm, **BẮT BUỘC giữ nguyên tỉ lệ nén phối cảnh theo góc nhìn mắt người**, tuyệt đối không để các frame mở to tự xoay thành hình tròn chính diện!

| Hướng Yêu Cầu | Góc Mắt Người Nhìn & Phối Cảnh (Human Eye Viewpoint) | Hình Dạng & Tỉ Lệ Khóa Cứng Xuyên Suốt 16 Frames (Foreshortening Lock) | Quy Luật Phản Chiếu, Viền Xa-Gần & Hướng Phóng Năng Lượng (Optical Reflection, Rim Depth & Emission Vector) | Từ Ngữ CẤM Dùng Trong Prompt |
|---|---|---|---|---|
| **1. Hướng Mặt Đất / Dưới Sàn (`GROUND / FLOOR PLANE`)** | Mắt người đứng nhìn chếch xuống mặt sàn vô hình ở góc nghiêng thấp `~15°–20°`. | **Elip Dẹp Nằm Ngang (`Pancake-Flat Horizontal Ellipse`, `W : H = 4 : 1`)**.<br>Khóa cứng tỉ lệ `4:1` ở cả 16 frames của mọi tấm. | - **Viền dưới (gần mắt)**: Dày và sáng rực hơn.<br>- **Viền trên (xa mắt)**: Mảnh hơn, lộ thành trong phía xa (recessed inner back wall).<br>- **Tia phản chiếu & Hạt xoáy**: Phụt **thẳng đứng từ dưới LÊN TRÊN (`90° vertical upward dancing rays & rising sparks`)**. | CẤM: `circular`, `circle`, `round`, `front-facing`. |
| **2. Hướng Trên Trời / Trần Nhà (`SKY / OVERHEAD PLANE`)** | Mắt người đứng dưới đất ngước nhìn chếch lên bầu trời ở góc `~15°–20°`. | **Elip Dẹp Nằm Ngang (`Pancake-Flat Horizontal Ellipse`, `W : H = 4 : 1`)**. | - **Nhìn ngước vào lòng vòm trên (Underside Vault View)**: Thấy đáy xoáy lõm lên trên.<br>- **Tia phản chiếu & Hạt xoáy**: Giáng **thẳng đứng từ trên XUỐNG DƯỚI (`90° vertical downward rays & falling sparks`)** bên dưới đĩa elip. | CẤM: `circular`, `circle`, `round`, `upward rays`, `front-facing`. |
| **3. Hướng Bên Trái (`LEFT-SIDE VERTICAL PLANE`)** | Mặt phẳng cổng **dựng đứng vuông góc mặt đất ở bên trái**, mặt mở hướng sang Phải (`3 o'clock`), mắt người nhìn nghiêng vào mặt phẳng dọc đó. | **Elip Hẹp Dựng Đứng (`Narrow Standing Vertical Ellipse`, `W : H = 1 : 4`)**. | - **Viền trái (gần mắt)**: Sáng dày hơn; thành trong mở về bên phải.<br>- **Tia phản chiếu & Hạt xoáy**: Phóng **ngang từ Trái sang Phải (`horizontal left-to-right dancing rays & streaming sparks`)**. | CẤM: `circular`, `circle`, `round`, `horizontal ellipse`, `front-facing`. |
| **4. Hướng Bên Phải (`RIGHT-SIDE VERTICAL PLANE`)** | Mặt phẳng cổng **dựng đứng vuông góc mặt đất ở bên phải**, mặt mở hướng sang Trái (`9 o'clock`), mắt người nhìn nghiêng vào mặt phẳng dọc đó. | **Elip Hẹp Dựng Đứng (`Narrow Standing Vertical Ellipse`, `W : H = 1 : 4`)**. | - **Viền phải (gần mắt)**: Sáng dày hơn; thành trong mở về bên trái.<br>- **Tia phản chiếu & Hạt xoáy**: Phóng **ngang từ Phải sang Trái (`horizontal right-to-left dancing rays & streaming sparks`)**. | CẤM: `circular`, `circle`, `round`, `horizontal ellipse`, `front-facing`. |
| **5. Hướng Chính Diện (`FRONT-FACING / DIRECT 0° VIEW`)** | Mặt phẳng hiệu ứng **đối diện trực tiếp 100% với mắt người (`0° Front View`)**. | **Hình Tròn Đối Xứng Tâm (`Perfect 1:1 Circle`, `W : H = 1 : 1`)**. | - Tâm xoáy hút thẳng vào trục Z màn hình.<br>- Cánh xoáy và tua năng lượng **xoay tròn & tỏa đều 360° (`360° radial rotational symmetry`)**. | CẤM: `flat ellipse`, `foreshortened`, `tilted`. |

---

## 7. Quy Trình Kiểm Tra Nghiệm Thu Ảnh (Mandatory Visual Verification Checklist)

Sau khi gửi prompt lên Flow và tải ảnh về, AI **BẮT BUỘC mở ảnh lên kiểm tra từng frame** theo 5 tiêu chí:
1. **Độ mượt chuyển động giữa các frame (`Kinetic Fluidity`)**: Các cánh xoáy, lưỡi plasma, tia sáng và hạt năng lượng có thực sự xoay góc / dịch chuyển vị trí từ frame này sang frame kế tiếp không, hay chỉ là 1 ảnh tĩnh bị phóng to thu nhỏ? Nếu bị tĩnh đơ → phải tăng cường mô tả góc xoay từng cụm frame (`12h->3h->6h->9h`) và tạo lại!
2. **Đúng vai trò trong Chuỗi Nối Tiếp (`Multi-Sheet Continuity`)**:
   - Tấm Mở (`Sheet 1`): Mở dần từ nhỏ đến to, kết thúc ở Frame 16 là đang mở 100% (không bị đóng lại).
   - Tấm Duy trì (`Sheet 2`): Cả 16 frames đều mở to đều nhau và xoay vòng liên tục.
   - Tấm Đóng (`Sheet 3`): Bắt đầu từ mở to ở Frame 1 rồi thu nhỏ dần và tắt hẳn ở Frame 16.
   - Chuỗi Tác động (như Sét đánh → Nổ): Tấm 1 giáng xuống chạm đất, Tấm 2 bùng nổ sóng xung kích từ điểm chạm.
3. **Khóa Phối Cảnh 5 Hướng Mắt Người**: Tuyệt đối không trôi góc nhìn ở các frame giữa.
4. **Màu nền phẳng & Không trùng màu chi tiết**: Nền đồng nhất 100%, không đường kẻ lưới (`zero grid lines`).
5. **Bounding Box**: Không bị cắt cụt mép (`zero clipping`).
