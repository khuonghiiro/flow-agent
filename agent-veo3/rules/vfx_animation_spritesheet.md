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

## 4. Quy Tắc CẤM Viền Khung & Cấm Vẽ Mặt Đất Giả (ZERO BORDERS & ZERO PROPS)

1. **TUYỆT ĐỐI CẤM MỌI DẠNG ĐƯỜNG KẺ & VIỀN Ô (NO BORDERS / NO GRID LINES)**:
   - `Strictly NO grid lines, NO black lines, NO borders, NO divider lines, NO box outlines, NO separator lines, NO numbers, NO text. All 16 frames float freely in open space on one single seamless unbroken flat background`.
   - **CẤM DÙNG TỪ `"cell"` / `"each cell"` / `"per cell"` / `"grid box"` TRONG PROMPT**: Việc dùng từ `"cell"` trong prompt tiếng Anh sẽ kích hoạt AI vẽ khung kẻ ô bàn cờ! Bắt buộc dùng `"around every isolated frame"` hoặc `"floating freely in open space with 30% empty background margin"`.
   - **Giới hạn độ dài tia sáng phản chiếu (`Short Compact Tapering Rays`)**: Mọi tia sáng phụt lên (Mặt đất), giáng xuống (Trên trời), hay bắn ngang (Bên trái/Bên phải) phải được mô tả là `"short, compact tapering light rays fading out quickly inside the safe margin"` để không bao giờ kéo dài chạm vào frame bên cạnh.
   - Toàn bộ khoảng cách giữa các frame là **màu nền thuần chất, liền mạch và liên tục**.
2. **TUYỆT ĐỐI CẤM ĐƯỜNG KẺ MẶT ĐẤT HOẶC BỐI CẢNH (NO GROUND LINE / NO HORIZON / NO PROPS)**:
   - Cấm vẽ đường chân trời, vạch kẻ ngang đáy, sàn nhà, cỏ, đá, tường hay vết nứt địa hình cố định.
   - Hướng không gian của hiệu ứng phải được thể hiện **100% thông qua hình học thấu thị (Perspective Foreshortening) và phản chiếu ánh sáng theo góc nhìn mắt con người**.

---

## 5. Quy Tắc Phối Cảnh & Phản Chiếu Theo Góc Nhìn Mắt Người Cho 5 Hướng Không Gian (HUMAN EYE-LEVEL PERSPECTIVE & OPTICAL REFLECTION RULES)

> ⚠️ **LỖI NGHIÊM TRỌNG CẦN TRIỆT TIÊU ("PERSPECTIVE DRIFT" GIỮA CÁC FRAME)**:
> Khi tạo hiệu ứng có hướng không gian (như cổng dịch chuyển ở mặt đất, trên trời, bên trái, bên phải), AI rất hay vẽ đúng hình elip ở Frame 1–5 và 13–16 (khi hiệu ứng còn nhỏ), nhưng **đến Frame 6–12 (khi hiệu ứng mở cực đại / Full Aperture) lại bị trôi góc nhìn thành HÌNH TRÒN CHÍNH DIỆN (`1:1 front-facing circle`)**!
> **Nguyên nhân**: Do trong prompt dùng các từ như `"circular swirl"`, `"expands into a circle"`, hoặc quên khóa tỉ lệ nén phối cảnh (`foreshortened aspect ratio`) ở các frame giữa (Frames 6–12).

Để AI hình dung chuẩn xác theo **góc nhìn mắt con người (Human Eye-Level Optical Perspective)** và **phản chiếu ánh sáng/chiều sâu** của vật thể trong không gian 3D chiếu lên mặt phẳng 2D, **BẮT BUỘC** áp dụng đúng ma trận 5 hướng nhìn dưới đây:

### 5.1. Bảng Ma Trận Hình Học Phối Cảnh & Phản Chiếu Theo Mắt Người (5 Hướng Chuẩn)

| Hướng Yêu Cầu | Góc Mắt Người Nhìn & Phối Cảnh (Human Eye Viewpoint) | Hình Dạng & Tỉ Lệ Khóa Cứng Xuyên Suốt 16 Frames (Foreshortening Lock) | Quy Luật Phản Chiếu, Viền Xa-Gần & Hướng Phóng Năng Lượng (Optical Reflection, Rim Depth & Emission Vector) | Từ Ngữ CẤM Dùng Trong Prompt |
|---|---|---|---|---|
| **1. Hướng Mặt Đất / Dưới Sàn (`GROUND / FLOOR PLANE`)** | Mắt người đứng nhìn chếch xuống mặt sàn vô hình ở góc nghiêng thấp `~15°–20°` (High-angle oblique floor view). | **Elip Dẹp Nằm Ngang (`Pancake-Flat Horizontal Ellipse`)**.<br>- Trục ngang rộng gấp **3.5 đến 4 lần** trục dọc (`W : H = 4 : 1`).<br>- **Bắt buộc giữ nguyên độ dẹp `4:1` ở TẤT CẢ 16 frames** (kể cả Frames 6–12 khi mở lớn nhất). Khi nở to, elip chỉ bè rộng sang 2 bên, **TUYỆT ĐỐI KHÔNG cao phồng lên thành hình tròn**. | - **Viền gần mắt (Cạnh dưới elip)**: Dày hơn, sáng rực hơn (intense foreground specular highlight).<br>- **Viền xa mắt (Cạnh trên elip)**: Mảnh hơn do xa mắt, lộ rõ phần thành trong phía xa (recessed inner back wall) tạo độ hút sâu xuống mặt sàn.<br>- **Phản chiếu & Tia phụt (Normal Vector)**: Cột sáng, tia hào quang, vòng phản chiếu phụ và hạt năng lượng **phụt thẳng đứng từ dưới LÊN TRÊN (`strictly vertical 90° upward rays rising from the flat floor ellipse`)**. Đáy dưới phẳng lì áp sát mặt đất vô hình. | CẤM dùng: `circular`, `circle`, `round swirl`, `spherical`, `front-facing`. |
| **2. Hướng Trên Trời / Trần Nhà (`SKY / OVERHEAD / CEILING PLANE`)** | Mắt người đứng dưới đất ngước nhìn chếch lên bầu trời / trần vô hình ở góc `~15°–20°` (Low-angle worm's-eye upward view). | **Elip Dẹp Nằm Ngang (`Pancake-Flat Horizontal Ellipse`)** đặt ở nửa trên của vùng an toàn ô.<br>- Tỉ lệ Rộng : Cao = `3.5 : 1` đến `4 : 1` khóa cứng xuyên suốt 16 frames.<br>- Không bao giờ biến thành hình tròn 1:1 ở các frame giữa. | - **Góc nhìn ngước từ dưới lên lòng cổng (Underside Vault View)**: Mắt người nhìn thấy **đáy/vòm trong phía trên** của cổng trời.<br>- **Viền dưới (gần mắt hơn)** sáng rõ, vòng xoáy hút ngược lên phía trên.<br>- **Phản chiếu & Tia giáng (Normal Vector)**: Tia sáng, luồng hào quang, tia sét và hạt năng lượng **giáng thẳng đứng từ lòng elip XUỐNG DƯỚI (`strictly vertical 90° downward-projecting light beams & falling particles descending from the upper ellipse`)**. | CẤM dùng: `circular`, `circle`, `round`, `upward rays`, `front-facing`. |
| **3. Hướng Bên Trái (`LEFT-SIDE VERTICAL PLANE`)** | Mặt phẳng hiệu ứng **dựng đứng vuông góc với mặt đất ở phía bên trái**, mắt người nhìn nghiêng vào mặt phẳng dọc đó (Side-angled perspective looking at a vertical plane on the left, facing right `3 o'clock` hoặc facing left `9 o'clock`). | **Elip Hẹp Dựng Đứng (`Narrow Standing Vertical Ellipse`)**.<br>- Trục dọc cao gấp **3.5 đến 4 lần** trục ngang (`W : H = 1 : 4`, cao và hẹp ngang).<br>- **Khóa cứng tỉ lệ hẹp ngang `1:4` xuyên suốt cả 16 frames**. Khi đạt cực đại (Frames 7–9), cổng mở cao theo chiều dọc nhưng vẫn hẹp ngang, **không bao giờ phình ngang thành hình tròn**. | - **Mặt cổng hướng sang Phải (Left-to-Right Portal/Shield)**: Cạnh viền gần mắt người dày và chói sáng hơn; cạnh viền xa mảnh hơn, để lộ thành trong của hố xoáy hướng về bên phải (3 giờ).<br>- **Phản chiếu & Luồng phóng (Normal Vector)**: Các tia sáng, luồng xung kích và hạt năng lượng **bắn ngang theo phương ngang từ Trái sang Phải (`strictly horizontal rays/streams shooting left-to-right perpendicular to the vertical ellipse`)** (hoặc từ Phải sang Trái nếu mặt cổng quay về hướng 9 giờ). | CẤM dùng: `circular`, `circle`, `round`, `horizontal ellipse`, `front-facing`. |
| **4. Hướng Bên Phải (`RIGHT-SIDE VERTICAL PLANE`)** | Mặt phẳng hiệu ứng **dựng đứng vuông góc với mặt đất ở phía bên phải**, mắt người nhìn nghiêng vào mặt phẳng dọc bên phải (Side-angled perspective looking at a vertical plane on the right, mặt hướng sang Trái `9 o'clock` hoặc sang Phải `3 o'clock`). | **Elip Hẹp Dựng Đứng (`Narrow Standing Vertical Ellipse`)**.<br>- Trục dọc cao gấp **3.5 đến 4 lần** trục ngang (`W : H = 1 : 4`, cao và hẹp ngang).<br>- **Khóa cứng tỉ lệ `1:4` xuyên suốt cả 16 frames**, tuyệt đối không biến thành hình tròn 1:1 ở các frame giữa. | - **Mặt cổng hướng sang Trái (Right-to-Left Portal/Shield)**: Cạnh viền gần mắt người dày và sáng gắt hơn; thành trong của hố xoáy lõm sâu về bên phải, mặt mở hướng về bên trái (9 giờ).<br>- **Phản chiếu & Luồng phóng (Normal Vector)**: Các tia sáng, sóng xung kích và hạt năng lượng **phóng ngang từ Phải sang Trái (`strictly horizontal rays/streams shooting right-to-left perpendicular to the vertical ellipse`)** (hoặc sang phải nếu mặt cổng hướng 3 giờ). | CẤM dùng: `circular`, `circle`, `round`, `horizontal ellipse`, `front-facing`. |
| **5. Hướng Chính Diện (`FRONT-FACING / DIRECT 0° VIEW`)** | Mặt phẳng hiệu ứng **đối diện trực tiếp 100% với mắt người** (vuông góc hoàn toàn với trục nhìn camera `0° Orthographic Front View`). | **Hình Tròn Đối Xứng Tâm Hoàn Hảo (`Perfect 1:1 Front-Facing Circle`)**.<br>- Tỉ lệ Rộng : Cao = `1 : 1` đồng nhất xuyên suốt 16 frames. | - **Chiều sâu đồng tâm (Concentric Tunnel Depth)**: Tâm xoáy nằm chính giữa, hút thẳng vào chiều sâu màn hình (trục Z).<br>- **Phản chiếu & Tỏa sáng**: Độ dày viền đều nhau 360°, các tia sáng, tua năng lượng và sóng xung kích **tỏa tròn đều 360° ra mọi phía (`360-degree radial symmetry`)**. | CẤM dùng: `flat ellipse`, `foreshortened`, `tilted`. |

---

### 5.2. Ba Luật Bắt Buộc Khi Viết Prompt Để Chống Trôi Góc Nhìn Ở Frames 6–12 (Anti-Perspective-Drift Laws)

1. **LUẬT 1 — Khóa Tỉ Lệ Hình Học Ngay Đầu Prompt & Lặp Lại Ở Mọi Cụm Frame (`PER-STAGE GEOMETRY LOCK`)**:
   - Không bao giờ chỉ viết quy tắc phối cảnh ở đầu prompt rồi bỏ trống ở phần `Temporal Animation Progression`.
   - Trong mô tả của **từng giai đoạn** (`Frames 1-3`, `Frames 4-6`, `Frames 7-9`, `Frames 10-12`, `Frames 13-16`), **BẮT BUỘC phải ghi rõ tỉ lệ elip và mệnh lệnh cấm xoay về chính diện**:
     - *Ví dụ đúng cho Hướng Mặt Đất ở Frames 7–9*:
       `Frames 7 to 9 (Full Aperture): The ground portal reaches its widest horizontal span while remaining STRICTLY LOCKED as a pancake-flat foreshortened horizontal ellipse (4:1 width-to-height ratio, flat on the invisible floor, NEVER a round circle or front-facing swirl), with vertical cyan light rays rising straight upward.`
     - *Ví dụ SAI (gây lỗi Frame 6–12 quay về chính diện)*:
       `Frames 4 to 6: The ellipse expands into a circular swirl... Frames 7 to 9: The warp gate reaches maximum size...`

2. **LUẬT 2 — Mô Tả Rõ Phản Chiếu Cạnh Gần / Cạnh Xa Theo Mắt Người (`HUMAN-EYE FOREGROUND/BACKGROUND RIM CUES`)**:
   - Để AI vẽ ra đúng chiều sâu 3D như mắt người nhìn ngoài đời thực, prompt phải mô tả sự khác biệt quang học giữa cạnh gần mắt và cạnh xa mắt:
     - **Mặt Đất**: `"Viewed from a low grazing human eye angle (~15° above floor): the front/bottom rim of the flat horizontal ellipse is thicker and brighter cyan, while the back/top rim is thinner and reveals the recessed inner vortex wall."`
     - **Trên Trời**: `"Viewed from a human looking steeply upward at the sky: we see the underside of the flat horizontal overhead ellipse, with the lower/near rim brighter and vertical light beams pouring straight down from inside the vortex."`
     - **Bên Trái / Bên Phải**: `"Viewed from human eye level looking at a vertical plane standing perpendicular to the floor on the left/right: the portal is compressed horizontally into a narrow 1:4 vertical standing ellipse, showing the recessed inner rim depth and horizontal energy beams shooting across."`

3. **LUẬT 3 — Khóa Hướng Pháp Tuyến Của Tia Phản Chiếu / Hạt Phụ (`NORMAL VECTOR EMISSION LOCK`)**:
   - Mọi tia sáng (light rays), cột năng lượng (pillars), bụi sáng (sparks/particles) thoát ra từ bề mặt hiệu ứng phải đi theo hướng vuông góc (`90° normal vector`) với mặt phẳng đó:
     - **Mặt đất** → Bắn thẳng lên trời (`90° vertical upward rays`).
     - **Trên trời** → Giáng thẳng xuống đất (`90° vertical downward rays`).
     - **Bên trái (nhìn sang phải)** → Phóng ngang từ trái qua phải (`horizontal left-to-right rays`).
     - **Bên phải (nhìn sang trái)** → Phóng ngang từ phải qua trái (`horizontal right-to-left rays`).
     - **Chính diện** → Tỏa tròn 360° (`radial 360° outward burst`).

---

## 6. Quy Tắc Nhân Quả Thời Gian 16 Frames (Strict Temporal Causality)

- **Frames 1 - 3 (Khởi đầu - Ignition/Opening)**: Xuất hiện khe sáng/điểm năng lượng ban đầu đúng theo trục phối cảnh đã khóa (ví dụ: khe sáng ngang dẹp cho Mặt đất/Trên trời; khe sáng dọc hẹp cho Trái/Phải; điểm sáng tròn cho Chính diện).
- **Frames 4 - 6 (Mở rộng - Foreshortened Expansion)**: Hiệu ứng mở rộng kích thước **nhưng giữ nguyên 100% tỉ lệ nén phối cảnh (`4:1` hoặc `1:4` hoặc `1:1`)**, tuyệt đối không tự ý xoay mặt phẳng về hướng camera.
- **Frames 7 - 9 (Cực đại - Peak Brilliance/Full Aperture)**: Đạt đỉnh điểm năng lượng và kích thước lớn nhất trong vùng an toàn (safe area 25% margin), **vẫn khóa cứng hình dạng phối cảnh (elip ngang dẹp / elip dọc hẹp / tròn chính diện)** kèm các tia phản chiếu/cột sáng phóng theo đúng vector pháp tuyến.
- **Frames 10 - 12 (Thu hẹp / Phân rã - Contraction/Decay)**: Thu nhỏ dần hoặc đứt gãy thành các tia/hạt năng lượng nhưng **vẫn giữ đúng mặt phẳng phối cảnh**, không bị tròn hóa.
- **Frames 13 - 16 (Tàn lụi & Tan biến - Clean Fadeout)**: Khép lại thành vệt sáng mảnh đúng trục phối cảnh rồi tan biến sạch sẽ 100% vào màu nền thuần khiết ở Frame 16.

---

## 7. Quy Trình Kiểm Tra Nghiệm Thu Ảnh (Mandatory Visual Verification Checklist)

Sau khi gửi prompt lên Flow và tải ảnh về, AI **BẮT BUỘC phải mở ảnh lên kiểm tra từng frame (từ Frame 1 đến Frame 16)** theo 4 tiêu chí nghiệm thu:
1. **Kiểm tra Khóa Phối Cảnh ở Frames 6–12 (Mid-Sequence Perspective Check)**:
   - Nếu yêu cầu **Mặt đất / Trên trời**: Các frame 6, 7, 8, 9, 10, 11, 12 có bị phồng dọc thành hình tròn chính diện không? Nếu bất kỳ frame nào ở giữa bị biến thành hình tròn `1:1` (nhìn chính diện) → **ĐÁNH RỚT (FAIL)**, phải siết lại từ khóa `"pancake-flat 4:1 horizontal ellipse in EVERY single frame including frames 6 to 12, ZERO circular shape"` và tạo lại!
   - Nếu yêu cầu **Bên trái / Bên phải**: Các frame 6–12 có giữ đúng hình **elip hẹp dựng đứng (`1:4 vertical ellipse`)** cùng hướng phóng tia sáng ngang không? Nếu bị biến thành hình tròn chính diện → **ĐÁNH RỚT (FAIL)** và tạo lại!
   - Nếu yêu cầu **Chính diện**: Cả 16 frames phải là hình tròn `1:1` đồng tâm đối xứng hoàn hảo.
2. **Kiểm tra Phản Chiếu & Hướng Tia Năng Lượng (Eye-Level Reflection & Rays)**:
   - Mặt đất: Tia sáng phụt **lên trên**; viền dưới sáng dày hơn viền trên.
   - Trên trời: Tia sáng giáng **xuống dưới**; nhìn thấy lòng cổng từ dưới lên.
   - Trái / Phải: Tia sáng phóng **ngang** vuông góc với mặt elip dựng đứng.
3. **Kiểm tra Nền Chroma & Không Tràn Màu (Chroma Purity)**: Nền phẳng 100%, không viền ô, không vạch kẻ đất.
4. **Kiểm tra Bounding Box**: Không frame nào bị cắt cụt mép (zero clipping).
