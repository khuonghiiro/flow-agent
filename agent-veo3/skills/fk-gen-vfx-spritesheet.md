---
name: vfx-animation-spritesheet
description: Tạo chuỗi ảnh 16-Frame VFX Animation Sprite Sheet (Multi-Sheet Sequential Chain: Mở -> Xoáy lặp -> Đóng, hoặc Giáng đòn -> Bùng nổ) trên Google Flow với chuyển động xoay/biến hình mượt mà qua từng frame, khóa phối cảnh 5 hướng mắt người và chọn màu nền phẳng không trùng chi tiết.
---

# Kỹ Năng Tạo Chuỗi VFX Animation Sprite Sheet Mượt Mà & Nối Tiếp (`/fk-gen-vfx-spritesheet`)

Kỹ năng này kết hợp trực tiếp với rule `.agents/rules/vfx_animation_spritesheet.md` để tạo **Chuỗi 16-Frame VFX Sprite Sheet Nối Tiếp (Multi-Sheet Sequential Chain)** trên Google Flow (`POST http://127.0.0.1:8100/flow/generate-image`), khắc phục triệt để 2 lỗi:
1. **Lỗi chuyển động đơ/cứng ngắc (Static Scaling)**: Bắt buộc mỗi frame kế tiếp phải có **bước xoay góc (`+22.5°/frame`)**, **vệt mờ chuyển động cong (`swirling motion-blur trails`)**, và **tia sáng nhảy múa cao thấp lệch pha (`flickering alternating rays`)**.
2. **Lỗi thiếu nối tiếp / nhồi nhét quá nhiều pha vào 1 tấm**: Chia hiệu ứng thành **Chuỗi nhiều tấm ảnh 16 frames nối tiếp nhau (Multi-Sheet Chain)**:
   - **Với Cổng Dịch Chuyển / Vòng Ma Pháp (3 Tấm nối tiếp)**:
     - **Tấm 1 (`Sheet 1 - Opening`)**: 16 frames từ vết nứt nhỏ → bung xoáy mở rộng dần → kết thúc ở Frame 16 là **Cổng đang mở 100%** (KHÔNG đóng lại!).
     - **Tấm 2 (`Sheet 2 - Active Continuous Spinning Loop`)**: Cả 16 frames đều giữ **kích thước mở 100% đồng nhất** và chỉ tập trung **xoay cuộn tròn 360° liên tục** (`+22.5°` mỗi frame, cánh xoáy quét `12h -> 3h -> 6h -> 9h -> 12h`), giúp lặp vô tận cực mượt không bao giờ bị khựng.
     - **Tấm 3 (`Sheet 3 - Collapse & Closure`)**: Nối tiếp từ Tấm 2 — Frame 1 đang mở to 100% → xoáy hút ngược thu nhỏ dần qua Frames 2–12 → khép thành tia chớp và tan biến ở Frame 16.
   - **Với Hiệu Ứng Tác Động Liên Hoàn như Sét Đánh → Nổ Mặt Đất (2 Tấm nối tiếp)**:
     - **Tấm 1 (`Sheet 1 - Lightning Descent & Contact`)**: Tia sét giáng từ trên cao xuống qua từng frame và chạm đất tạo lõi cầu điện nén cực đại ở Frame 15–16.
     - **Tấm 2 (`Sheet 2 - Ground Impact Explosion & Shockwave`)**: Nối tiếp ngay từ lõi cầu điện ở Frame 16 Tấm 1 → bùng nổ thành vòm điện quang & vòng sóng xung kích elip ngang lan rộng trên mặt đất rồi phân rã tan biến.

---

## 1. Quy Tắc Chọn Tỉ Lệ Khung Ảnh (`16:9` / `9:16` / `1:1`), Lưới (`4×4` hoặc `3×3`) & Màu Nền Linh Hoạt

### 1.1. Chọn Tỉ Lệ Khung Hình (`16:9`, `4:3`, `1:1`, `3:4`, `9:16`) & Lưới (`3×3 = 9 Frames` hoặc `4×4 = 16 Frames`) Theo Tổng Thể (Lõi + Bóng Mờ)
- **Nguyên tắc chống cắt bóng mờ (`Zero Shadow/Mist Clipping`)**:
  - Khi chọn tỉ lệ ảnh, phải xét **TỔNG THỂ HÌNH KHỐI = Phần Lõi Chính + Lớp Bóng Mờ / Sương Hào Quang Lan Tỏa Xung Quanh**.
  - **Ưu tiên lưới `3 hàng × 3 cột = 9 frames` (`3×3 Grid`)** cho các hoạt ảnh **Animate Loop** vì mỗi ô frame rộng gấp `1.78 lần` so với `4×4`, giúp phần bóng mờ có không gian tan hết về `0%` vào nền phẳng ngay bên trong lòng frame, tuyệt đối không bị cắt phẳng ngang ở đỉnh (`NO flat-topped mist cut-off`) và không chạm mép ảnh!
  - **CẤM viết `"100% full width"` / `"100% size"`** trong prompt (vì AI sẽ vẽ tràn sát mép ô frame làm phần bóng mờ bị cắt cụt). Thay bằng: `"compact full mature size (the entire effect INCLUDING its outermost mist and motion-blur shadow occupies at most 55% of its frame space, fading smoothly to 0% into pure flat background well inside the frame boundary with 45% empty background padding all around)"`.
- **Bảng ánh xạ Tổng Thể Hình Thái (Lõi + Bóng Mờ) → Tỉ Lệ Khung Ảnh (`aspect_ratio`)**:
  1. **Hiệu ứng kéo rộng ngang dẹt, bóng mờ ôm sát mặt ngang (`16:9` — `IMAGE_ASPECT_RATIO_LANDSCAPE`)**: Cổng dịch chuyển mặt đất/bầu trời elip ngang `4:1` (bóng mờ dư ảnh ôm sát vành ngang, không bốc cột sương cao), Sóng xung kích mặt đất, Vệt chém kiếm ngang.
  2. **Hiệu ứng ngang nhưng có cột bóng mờ / sương hào quang bốc lên trên (`4:3` — `IMAGE_ASPECT_RATIO_LANDSCAPE_FOUR_THREE`)**: Cổng mặt đất có làn sương hào quang bốc lên phía trên (dùng `4:3` thay vì `16:9` để có thêm chiều cao cho phần bóng mờ bốc lên, **tránh tuyệt đối lỗi bóng mờ chạm trần frame bị cắt cụt ngang**!), Đám lửa cháy ngang bốc khói, Vụ nổ vòm mặt đất.
  3. **Hiệu ứng tròn đều đối xứng tâm 360° (`1:1` — `IMAGE_ASPECT_RATIO_SQUARE`)**: Quả cầu năng lượng xoay Loop (`Energy Orb`), Khiên ma pháp cầu, Cổng chính diện `0°`.
  4. **Hiệu ứng vươn cao vừa phải (`3:4` — `IMAGE_ASPECT_RATIO_PORTRAIT_THREE_FOUR`)**: Ngọn lửa phép thuật cháy bập bùng tại chỗ Loop (`Magic Flame Loop`), Hào quang nhân vật.
  5. **Hiệu ứng kéo cao thẳng đứng (`9:16` — `IMAGE_ASPECT_RATIO_PORTRAIT`)**: Sấm sét thẳng đứng, Cột lốc xoáy điện cao (`Lightning Tornado Loop`), Cột sáng, Cổng dựng đứng bên Trái/Phải.

### 1.2. Chọn Màu Nền Linh Hoạt Không Trùng Chi Tiết
- **KHÔNG bắt buộc dùng nền Tím (`#FF00FF`) hay Xanh lá (`#00FF00`)**.
- Được phép chọn **bất kỳ màu nền phẳng đơn sắc nào** (ví dụ: Đen tuyền `#000000`, Than chì tối `#141414`, Xanh đêm `#0A0E1A`, Đỏ rượu sẫm `#2A0510`, v.v.) **MIỄN LÀ**:
  1. Màu nền đó **không trùng với bất kỳ màu chi tiết nào của hiệu ứng**.
  2. Nền phẳng 100% không viền ô, không kẻ lưới, không bàn cờ (`strictly NO checkerboard pattern`, tuyệt đối không dùng từ `"cell"` trong prompt).
  3. Các tấm trong cùng 1 chuỗi nối tiếp dùng chung cùng 1 mã màu nền.

---

## 2. Bộ Mẫu Prompt Chuẩn Cho Chuỗi Cổng Dịch Chuyển Mặt Đất 3 Tấm — Tỉ Lệ `16:9` (`IMAGE_ASPECT_RATIO_LANDSCAPE`)

### Tấm 1: Pha Khởi Tạo & Mở Cổng (`Sheet 1 of 3: Portal Ignition & Opening To Full Size — 16:9 Landscape, Silky Motion-Blur Shadow`)
```text
A borderless widescreen 16:9 animation sprite sheet containing exactly 16 sequential hand-drawn 2D anime VFX animation frames of PHASE 1 ONLY (IGNITION AND OPENING EXPANSION) of a ground-level dimensional warp portal, floating freely on a single continuous unbroken flat pitch-black #000000 canvas, neatly arranged in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL BACKGROUND & ZERO-BORDER CONSTRAINTS:
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, strictly NO checkerboard pattern, no gradient, no floor texture, no horizon line.
- Strictly NO grid lines, NO borders, NO divider lines, NO box outlines, NO text, NO numbers. All 16 frames float freely in open space with 30% empty black margin around every frame.

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & WIDE HORIZONTAL VORTEX LOCK:
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor): every single frame MUST be a wide flat horizontal swirling whirlpool (3.5:1 width-to-height ratio) lying flat on the invisible floor inside its widescreen 16:9 frame space. STRICTLY FORBIDDEN to draw a round 1:1 front-facing circle in any frame.

CRITICAL SILKY MOTION-BLUR SHADOW & ETHEREAL MIST IN ALL 16 FRAMES (NO HARD CHALKY RINGS!):
- EVERY SINGLE FRAME from Frame 1 through Frame 16 (strictly including Frames 6, 12, 15, and 16!) MUST feature soft feathered plasma ribbons, trailing soft cyan-blue MOTION-BLUR AFTERIMAGE SHADOW TRAILS, and a Swirling Diffused Ethereal Mist Glow rising and curving smoothly with the vortex rotation!
- STRICTLY FORBIDDEN to drop or lose the soft glowing mist shadow in Frames 15 and 16! STRICTLY FORBIDDEN to draw bare empty rings or hard-edged solid chalky white outer ring outlines!

CRITICAL KINETIC FLUIDITY & CONTINUITY HANDSHAKE (END AT 100% OPEN IN FRAME 16 WITH FULL MISTY GLOW!):
- Frame-by-frame progression (16 frames of smooth silky opening ONLY):
  - Frames 1 to 4 (Crack & Misty Spark Ignition): A horizontal electric spark cracks open into a small swirling flat horizontal ellipse wrapped in soft glowing cyan motion-blur mist as the silky spiral ribbons sweep clockwise from 12 o'clock to 3 o'clock.
  - Frames 5 to 8 (Accelerating Silky Swirl Expansion): The flat horizontal vortex spins clockwise (sweeping 3 o'clock to 6 o'clock), growing to 55% width with soft feathered plasma ribbons, trailing motion-blur ghost shadows, and swirling upward mist veil.
  - Frames 9 to 12 (Surging Vortex Widening): The flat horizontal ellipse expands from 65% to 88% width (sweeping 6 o'clock to 9 o'clock) with continuous soft volumetric mist glow and curving motion-blur afterimage trails in every frame.
  - Frames 13 to 16 (Reaching 100% Full Open Aperture - KEEP FULL SOFT MOTION-BLUR MIST GLOW IN FRAMES 15 & 16!): The flat horizontal ground portal reaches 100% full width in Frames 13-16 while maintaining the EXACT SAME rich, soft rising ethereal mist glow and silky motion-blur shadow trails as Frames 7-14 (strictly NO bare hard rings in Frames 15-16!).
```

### Tấm 2: Pha Duy Trì Vòng Xoáy Liên Tục (`Sheet 2 of 3: Continuous Active Spinning Loop at 100% Full Size — 16:9 Landscape, Silky Motion-Blur Shadow`)
```text
A borderless widescreen 16:9 animation sprite sheet containing exactly 16 sequential hand-drawn 2D anime VFX animation frames of PHASE 2 (CONTINUOUS SPINNING VORTEX LOOP AT CONSTANT FULL SIZE) of a ground-level dimensional warp portal, floating freely on a single continuous unbroken flat pitch-black #000000 canvas, neatly arranged in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL BACKGROUND & ZERO-BORDER CONSTRAINTS:
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, strictly NO checkerboard pattern, no grid lines, no borders, no divider lines, no box outlines, no text. 30% empty black safety margin around every frame.

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & WIDE HORIZONTAL VORTEX LOCK:
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor): every single frame from Frame 1 to Frame 16 is a wide flat horizontal swirling whirlpool (3.5:1 width-to-height ratio) lying level and flat on the invisible floor inside its widescreen 16:9 frame space.

CRITICAL SILKY MOTION-BLUR SHADOW & ETHEREAL MIST IN ALL 16 FRAMES (NO HARD RINGS, NO EMPTY RINGS!):
- EVERY SINGLE FRAME from Frame 1 through Frame 16 (strictly including Frame 1 and Frame 16!) MUST be a rich, full, multi-layered swirling whirlpool with soft feathered plasma ribbons, trailing soft cyan-blue MOTION-BLUR AFTERIMAGE SHADOW TRAILS, and a soft Swirling Diffused Ethereal Mist Veil rising above the vortex!
- STRICTLY FORBIDDEN to draw bare empty rings in Frame 1 or Frame 16! STRICTLY FORBIDDEN to draw a hard-edged solid white outer ring outline! Every spiral arm has soft airbrushed feathered edges that blend into dreamy cyan-blue motion-blur smoke and mist.

CRITICAL CONTINUOUS CLOCKWISE SWIRLING FLOW ACROSS ALL 16 FRAMES:
- All 16 frames stay at the exact same 100% full open width while the silky feathered spiral arms, their soft motion-blur ghost shadows, and the rising misty vapor veil swirl continuously clockwise:
  - Frames 1 to 4: Full swirling horizontal vortex at 100% size wrapped in soft glowing cyan motion-blur mist as the silky spiral ribbons and feathered ghost trails sweep clockwise from 12 o'clock to 3 o'clock.
  - Frames 5 to 8: The full-size flat horizontal vortex continues spinning clockwise (sweeping 3 o'clock to 6 o'clock) with soft feathered plasma ribbons, trailing motion-blur ghost shadows, and swirling upward mist veil.
  - Frames 9 to 12: The full-size flat horizontal vortex spins clockwise (sweeping 6 o'clock to 9 o'clock) with continuous soft volumetric mist glow and curving motion-blur afterimage trails.
  - Frames 13 to 16: The full-size flat horizontal vortex completes its clockwise rotation (sweeping 9 o'clock to 12 o'clock) while maintaining the EXACT SAME rich internal spiral ribbons, soft rising ethereal mist glow, and silky motion-blur shadow trails in Frame 15 and Frame 16 (strictly NO empty ring in Frame 16!).
```

### Tấm 3: Pha Thu Hẹp & Đóng Cổng (`Sheet 3 of 3: Vortex Collapse & Dissipation From Full Size — 16:9 Landscape, Silky Motion-Blur Shadow`)
```text
A borderless widescreen 16:9 animation sprite sheet containing exactly 16 sequential hand-drawn 2D anime VFX animation frames of PHASE 3 ONLY (VORTEX CONTRACTION AND CLOSURE STARTING FROM 100% FULL OPEN SIZE IN FRAME 1) of a ground-level dimensional warp portal, floating freely on a single continuous unbroken flat pitch-black #000000 canvas, neatly arranged in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL BACKGROUND & ZERO-BORDER CONSTRAINTS:
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, strictly NO checkerboard pattern, no grid lines, no borders, no divider lines, no box outlines, no text. 30% empty black safety margin around every frame.

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & WIDE HORIZONTAL VORTEX LOCK:
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor): every active frame is a wide flat horizontal swirling whirlpool (3.5:1 width-to-height ratio) lying level and flat on the invisible floor inside its widescreen 16:9 frame space.

CRITICAL SILKY MOTION-BLUR SHADOW & ETHEREAL MIST (NO HARD RINGS!):
- Every active frame features soft feathered plasma ribbons, trailing soft cyan-blue MOTION-BLUR AFTERIMAGE SHADOW TRAILS, and a soft Swirling Diffused Mist Veil rising above the vortex! STRICTLY FORBIDDEN to draw hard-edged solid chalky white rings!

CRITICAL CONTINUITY HANDSHAKE (START AT 100% FULL OPEN IN FRAME 1, THEN SPIN INWARD & DISSOLVE):
- Frame-by-frame progression (16 frames of smooth silky closing ONLY):
  - Frames 1 to 4 (Full Open Start & Initial Silky Suction): Frame 1 starts at 100% full open width wrapped in soft glowing cyan motion-blur mist; across Frames 2 to 4 the flat horizontal vortex spins clockwise and pulls inward to 75% width with soft feathered ghost trails.
  - Frames 5 to 8 (Accelerating Misty Contraction): The flat horizontal vortex narrows smoothly from 65% to 40% width as the silky spiral ribbons and soft motion-blur smoke curl tightly inward toward the center.
  - Frames 9 to 12 (Soft Implosion into Swirling Mist): The flat horizontal vortex shrinks from 30% down to a small glowing horizontal wisp of swirling cyan mist and soft motion-blur trails.
  - Frames 13 to 16 (Dreamy Vapor Dissipation & Clean Fadeout): The remaining soft glowing cyan mist and faint glowing motes gently disperse and fade out across Frames 13 to 15, leaving Frame 16 completely 100% empty pitch-black #000000.
```

---

## 3. Bộ Mẫu Prompt Chuẩn Cho Tia Sét Thẳng Đứng — Tỉ Lệ `9:16` (`IMAGE_ASPECT_RATIO_PORTRAIT`) Với Lưới `3 Hàng × 3 Cột = 9 Frames`

### Tấm 1: Tia Sét Giáng Thẳng Đứng Từ Trời Xuống Đất (`Sheet 1: Vertical Lightning Strike — 9:16 Portrait, 3x3 Grid = 9 Frames`)
```text
A borderless vertical 9:16 portrait animation sprite sheet containing exactly 9 isolated hand-drawn 2D anime VFX animation frames of a TALL VERTICAL LIGHTNING BOLT STRIKE, floating freely in open space on one single continuous unbroken flat pitch-black #000000 canvas, arranged in 3 rows of 3 columns (9 frames total), ordered left-to-right then top-to-bottom.

CRITICAL ZERO-BORDER & ISOLATED FLOATING CONSTRAINTS (NO WHITE LINES!):
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, strictly NO white lines, NO grid lines, NO comic panel borders, NO divider lines, NO box outlines, NO horizon line.
- Every single lightning bolt MUST be completely self-contained and floating in open pitch-black space with at least 25% empty black margin on top, bottom, left, and right (the lightning NEVER touches any edge!).

CRITICAL VERTICAL 9:16 PROPORTION & SOFT PLASMA GLOW AFTERIMAGE (9 FRAMES):
- Each isolated vertical lightning frame has a soft diffused cyan-blue plasma bloom and electric afterimage glow:
  - Frames 1 to 3 (Upper Spark & Rapid Downward Extension): Frame 1 begins as a compact floating electric spark cluster; Frame 2 zigzags downward into a short vertical bolt; Frame 3 extends further downward with branching side-arcs and soft cyan plasma mist, floating freely in black space.
  - Frames 4 to 6 (Floating Ground-Ring Contact & Blazing Vertical Bolt): In Frame 4 the bottom tip hits a small floating flat horizontal electric ring; in Frames 5 and 6 a self-contained vertical lightning bolt crackles into the floating horizontal electric ring with whipping branches and soft glowing cyan mist.
  - Frames 7 to 9 (Overcharged Plasma Core & Fading Afterimage): Across Frames 7 to 9 the upper bolt dissolves downward into a glowing cyan-white plasma sphere sitting on a floating flat horizontal electric shockwave ring with soft fading mist.
```

### Tấm 2: Vụ Nổ Điện Quang & Sóng Xung Kích Mặt Đất (`Sheet 2 of 2: Ground Shockwave Explosion & Electric Aftershock`)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 sequential hand-drawn animation frames of PHASE 2 (GROUND-LEVEL ELECTRIC SHOCKWAVE EXPLOSION AND AFTERSHOCK DISSIPATION CONTINUING DIRECTLY FROM A LIGHTNING STRIKE) on a single continuous unbroken flat pitch-black #000000 canvas, neatly arranged in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL BACKGROUND & ZERO-BORDER CONSTRAINTS:
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, no grid lines, no borders, no divider lines, no ground line. 30% empty black safety margin around every frame.

CRITICAL HUMAN-EYE GROUND PERSPECTIVE (4:1 FLAT HORIZONTAL SHOCKWAVE ELLIPSE):
- Viewed from a low grazing human eye angle (~15 degrees above an invisible floor): all ground shockwave rings are strictly compressed 4:1 flat horizontal ellipses, while electric arcs and plasma plumes erupt vertically UPWARD above the flat ring.

CRITICAL KINETIC EXPLOSION PROGRESSION (16 FRAMES):
- Frames 1 to 4 (Core Detonation): Continuing directly from Sheet 1's charged impact core, the blinding cyan-white plasma sphere violently erupts upward into a dome of crackling lightning arcs while a sharp 4:1 flat horizontal shockwave ring blasts outward along the invisible floor.
- Frames 5 to 8 (Expanding Ground Shockwave & Arc Eruption): The flat 4:1 horizontal shockwave ring expands wider across the floor plane as whip-like electric tendrils and glowing sparks shoot upward and outward in dynamic arcs.
- Frames 9 to 12 (Fracturing Electric Dome & Secondary Ripples): The central blast breaks apart into dancing residual lightning arcs and scattering electric embers floating above the fading horizontal shockwave ellipse.
- Frames 13 to 16 (Spark Decay & Clean Fadeout): Remaining tiny cyan electric sparks flicker out and dissolve completely, leaving Frame 16 100% empty pitch-black #000000.
```
