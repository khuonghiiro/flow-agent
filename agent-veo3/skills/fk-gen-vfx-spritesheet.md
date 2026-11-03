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

## 1. Quy Tắc Chuẩn: Ưu Tiên Lưới `4×4` (16 Frames) Kết Hợp Tỉ Lệ `4:3` / `1:1` / `3:4` & "Frame Thu Nhỏ Gọn Trong Lòng BBox"

### 1.1. Luôn Ưu Tiên Lưới `4×4` (16 Frames) & 3 Tỉ Lệ Cân Đối (`4:3`, `1:1`, `3:4`)
- **Luôn ưu tiên lưới `4×4` (4 hàng × 4 cột = 16 frames)** vì `3×3` (9 frames) quá ít frame cho hoạt ảnh mượt.
- **Ưu tiên 3 tỉ lệ khung hình cân đối `4:3`, `1:1`, `3:4` khi đi với lưới `4×4`**:
  1. **Hiệu ứng dạng ngang (`4:3` — `IMAGE_ASPECT_RATIO_LANDSCAPE_FOUR_THREE`)**: Cổng dịch chuyển mặt đất / trên trời (elip ngang kèm lớp bóng mờ dư ảnh và làn sương hào quang bốc lên trên), Sóng xung kích mặt đất, Vệt chém ngang. Dùng `4:3` giúp mỗi ô trong lưới `4×4` vừa có bề ngang rộng cho cổng elip, vừa có đủ chiều cao cho lớp bóng mờ sương khói phía trên!
  2. **Hiệu ứng tròn đều đối xứng tâm 360° (`1:1` — `IMAGE_ASPECT_RATIO_SQUARE`)**: Quả cầu năng lượng xoay Loop (`Energy Orb`), Khiên ma pháp cầu, Vòng ma pháp / Cổng chính diện `0°`, Vụ nổ tỏa tròn.
  3. **Hiệu ứng dạng dọc (`3:4` — `IMAGE_ASPECT_RATIO_PORTRAIT_THREE_FOUR`)**: Tia sét giáng thẳng đứng, Cột lốc xoáy điện/lửa (`Tornado Loop`), Ngọn lửa phép thuật cháy bập bùng, Cổng dịch chuyển dựng đứng bên Trái/Phải (`1:4`). Dùng `3:4` giúp chia 4 cột (`4×4`) không bị quá hẹp ngang như `9:16`.

### 1.2. Nguyên Lý "Frame Thu Nhỏ Gọn Trong Lòng BBox (`Miniaturized 40%–45% Scale Inside 1/4×1/4 BBox`)"
- **Coi mỗi hiệu ứng trong ô BBox `1/4 × 1/4` như một phiên bản thu nhỏ (`miniature scaled-down sprite`)**:
  - Người dùng sẽ cắt 16 ô BBox (`1/4 × 1/4`) ra rồi **scale phóng to lên** khi sử dụng.
  - Vì vậy, **TUYỆT ĐỐI CẤM viết `"100% full width"` / `"fills the space"`**! Ngay cả ở trạng thái mở tối đa (Frame 16 Tấm 1 hoặc cả 16 frames Tấm 2 Loop), tổng thể hiệu ứng **BAO GỒM CẢ ĐỈNH BÓNG MỜ SƯƠNG KHÓI VÀ MÉP NGOÀI CÙNG chỉ được chiếm tối đa `40%–45%` diện tích của ô BBox `1/4 × 1/4`**, chừa **`55%–60%` khoảng đệm màu nền trống rộng rãi xung quanh (`wide empty background corridors between all 4 rows and 4 columns`)** để không có bất kỳ chi tiết hay bóng mờ nào thừa ra ngoài BBox!

### 1.3. Chọn Màu Nền Linh Hoạt Không Trùng Chi Tiết
- **KHÔNG bắt buộc dùng nền Tím (`#FF00FF`) hay Xanh lá (`#00FF00`)**.
- Được phép chọn **bất kỳ màu nền phẳng đơn sắc nào** (ví dụ: Đen tuyền `#000000`, Than chì tối `#141414`, Xanh đêm `#0A0E1A`, Đỏ rượu sẫm `#2A0510`, v.v.) **MIỄN LÀ**:
  1. Màu nền đó **không trùng với bất kỳ màu chi tiết nào của hiệu ứng**.
  2. Nền phẳng 100% không viền ô, không kẻ lưới, không bàn cờ (`strictly NO checkerboard pattern`, tuyệt đối không dùng từ `"cell"` trong prompt).
  3. Các tấm trong cùng 1 chuỗi nối tiếp dùng chung cùng 1 mã màu nền.

---

## 2. Bộ Mẫu Prompt Chuẩn Cho Chuỗi Cổng Dịch Chuyển Mặt Đất 3 Tấm — Lưới `4×4` (16 Frames), Tỉ Lệ `4:3` (`IMAGE_ASPECT_RATIO_LANDSCAPE_FOUR_THREE`), Thu Nhỏ Gọn Trong BBox

### Tấm 1: Pha Khởi Tạo & Mở Cổng (`Sheet 1 of 3: Portal Ignition & Opening — 4:3 Landscape, 4x4 = 16 Miniature Frames`)
```text
A borderless 4:3 landscape animation sprite sheet containing exactly 16 isolated miniature hand-drawn 2D anime VFX animation frames of PHASE 1 ONLY (IGNITION AND OPENING EXPANSION) of a ground-level dimensional warp portal, floating freely in open space on one single continuous unbroken flat pitch-black #000000 canvas, arranged in 4 rows of 4 columns (16 frames total), ordered left-to-right then top-to-bottom.

CRITICAL MINIATURE IN-BBOX CONTAINMENT & ZERO-CLIPPING RULE (MAX 42% BBOX SCALE!):
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, strictly NO checkerboard pattern, NO grid lines, NO borders, NO divider lines, NO box outlines, NO text.
- MINIATURE SCALED-DOWN SPRITES INSIDE EACH 1/4 x 1/4 SLOT: Treat every single frame as a zoomed-out miniature! Even at maximum opening in Frame 16, each portal INCLUDING the highest tip of its rising mist shadow and outermost motion-blur glow MUST occupy at most 42% of its 1/4 x 1/4 space, surrounded by wide empty pitch-black #000000 corridors (58% empty black padding all around) so ZERO mist, ZERO shadow, and ZERO sparks ever touch, cross, or get clipped by the invisible bounding box!

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & SILKY MOTION-BLUR SHADOW IN ALL 16 FRAMES:
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor): every single frame is a compact flat horizontal swirling whirlpool (3.5:1 width-to-height ratio) lying level on the invisible floor.
- Every single frame from Frame 1 to Frame 16 features soft feathered cyan-blue spiral plasma ribbons, trailing soft MOTION-BLUR AFTERIMAGE SHADOW TRAILS, and a low, compact Swirling Diffused Ethereal Mist Veil rising gently above the vortex and fading 100% into pure black #000000 well inside its own bounding box!
- Frame-by-frame progression (16 miniature frames of smooth silky opening ONLY):
  - Frames 1 to 4 (Tiny Spark & Misty Ignition): A tiny horizontal spark cracks open into a miniature swirling flat horizontal ellipse wrapped in soft cyan motion-blur mist (growing from 10% to 18% of the slot width) as silky spiral ribbons sweep clockwise from 12 o'clock to 3 o'clock.
  - Frames 5 to 8 (Compact Silky Swirl Expansion): The miniature flat horizontal vortex spins clockwise (sweeping 3 o'clock to 6 o'clock), growing smoothly from 22% to 28% of the slot width with soft feathered plasma ribbons and self-contained rising mist.
  - Frames 9 to 12 (Growing Miniature Vortex): The flat horizontal ellipse expands smoothly from 32% to 38% of the slot width (sweeping 6 o'clock to 9 o'clock) with continuous soft volumetric mist glow and curving motion-blur afterimage trails completely inside its black margin.
  - Frames 13 to 16 (Reaching Compact Mature Size at 42% BBox Scale): The flat horizontal ground portal reaches its compact full mature size (strictly capped at 42% of its 1/4 x 1/4 slot width in Frames 13-16 so wide empty black space remains on all 4 sides!) while maintaining full soft rising ethereal mist glow and silky motion-blur shadow trails.
```

### Tấm 2: Pha Duy Trì Vòng Xoáy Liên Tục (`Sheet 2 of 3: Continuous Active Spinning Loop — 4:3 Landscape, 4x4 = 16 Miniature Frames`)
```text
A borderless 4:3 landscape animation sprite sheet containing exactly 16 isolated miniature hand-drawn 2D anime VFX animation frames of PHASE 2 (CONTINUOUS 360-DEGREE SPINNING VORTEX LOOP) of a ground-level dimensional warp portal, floating freely in open space on one single continuous unbroken flat pitch-black #000000 canvas, arranged in 4 rows of 4 columns (16 frames total), ordered left-to-right then top-to-bottom.

CRITICAL MINIATURE IN-BBOX CONTAINMENT & ZERO-CLIPPING RULE (MAX 42% BBOX SCALE!):
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, strictly NO checkerboard pattern, NO grid lines, NO borders, NO divider lines, NO box outlines, NO text.
- MINIATURE SCALED-DOWN SPRITES INSIDE EACH 1/4 x 1/4 SLOT: Every single one of the 16 portals INCLUDING the uppermost tip of its rising mist shadow and outermost motion-blur glow MUST be drawn as a compact miniature occupying at most 42% of its 1/4 x 1/4 space, separated by wide empty pitch-black #000000 corridors (at least 58% empty black padding all around) so the soft mist and shadow fade 100% into pure black well inside the bounding box — strictly NO flat-topped mist cut-off and NO edge clipping!

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & SILKY MOTION-BLUR SHADOW IN ALL 16 FRAMES:
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor): all 16 miniature frames are flat horizontal swirling whirlpools (3.5:1 width-to-height ratio) lying level on the invisible floor at the exact same compact 42% bounding-box scale.
- Every single frame from Frame 1 to Frame 16 features soft feathered cyan-blue spiral plasma ribbons, trailing soft MOTION-BLUR AFTERIMAGE SHADOW TRAILS, and a compact, softly rounded Swirling Diffused Ethereal Mist Veil that fades completely to pure black #000000 well below the top of its slot:
  - Frames 1 to 4: Compact miniature horizontal vortex spinning clockwise (sweeping 12 o'clock to 3 o'clock) with silky motion-blur shadow trails and unclipped soft rising mist surrounded by wide black space.
  - Frames 5 to 8: Compact miniature horizontal vortex continuing clockwise spin (sweeping 3 o'clock to 6 o'clock) with soft feathered plasma ribbons, trailing motion-blur ghost shadows, and unclipped mist veil.
  - Frames 9 to 12: Compact miniature horizontal vortex spinning clockwise (sweeping 6 o'clock to 9 o'clock) with continuous soft volumetric mist glow and curving motion-blur afterimage trails.
  - Frames 13 to 16: Compact miniature horizontal vortex completing its 360-degree clockwise rotation (sweeping 9 o'clock to 12 o'clock) with identical compact 42% scale and rich unclipped soft mist in all frames including Frame 16.
```

### Tấm 3: Pha Thu Hẹp & Đóng Cổng (`Sheet 3 of 3: Vortex Collapse & Dissipation — 4:3 Landscape, 4x4 = 16 Miniature Frames`)
```text
A borderless 4:3 landscape animation sprite sheet containing exactly 16 isolated miniature hand-drawn 2D anime VFX animation frames of PHASE 3 ONLY (VORTEX CONTRACTION AND CLOSURE) of a ground-level dimensional warp portal, floating freely in open space on one single continuous unbroken flat pitch-black #000000 canvas, arranged in 4 rows of 4 columns (16 frames total), ordered left-to-right then top-to-bottom.

CRITICAL MINIATURE IN-BBOX CONTAINMENT & ZERO-CLIPPING RULE (MAX 42% BBOX SCALE!):
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, strictly NO checkerboard pattern, NO grid lines, NO borders, NO divider lines, NO box outlines, NO text.
- Every single sprite INCLUDING its outermost rising mist and motion-blur shadow is a compact miniature occupying at most 42% of its 1/4 x 1/4 slot in Frame 1 and shrinking smaller from Frame 2 to Frame 16, surrounded by wide empty pitch-black #000000 corridors so ZERO details ever touch or cross the bounding box!

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & SILKY MOTION-BLUR CLOSURE (16 MINIATURE FRAMES):
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor): every active frame is a compact flat horizontal swirling whirlpool (3.5:1 width-to-height ratio) with soft feathered plasma ribbons, trailing cyan-blue MOTION-BLUR AFTERIMAGE SHADOW TRAILS, and soft rising mist:
  - Frames 1 to 4 (Compact Start & Initial Silky Suction): Frame 1 starts at the compact 42% bounding-box scale wrapped in unclipped soft cyan motion-blur mist; across Frames 2 to 4 the flat horizontal vortex spins clockwise and pulls inward to 32% scale.
  - Frames 5 to 8 (Accelerating Misty Contraction): The miniature flat horizontal vortex narrows smoothly from 28% down to 18% scale as silky spiral ribbons and soft motion-blur smoke curl tightly inward.
  - Frames 9 to 12 (Soft Implosion into Swirling Mist): The flat horizontal vortex shrinks from 14% down to a tiny glowing horizontal wisp of swirling cyan mist and soft motion-blur trails centered in black space.
  - Frames 13 to 16 (Dreamy Vapor Dissipation & Clean Fadeout): The remaining tiny soft glowing cyan mist and faint motes gently disperse across Frames 13 to 15, leaving Frame 16 completely 100% empty pitch-black #000000.
```

---

## 3. Bộ Mẫu Prompt Chuẩn Cho Hiệu Ứng Dọc (`3:4` — `IMAGE_ASPECT_RATIO_PORTRAIT_THREE_FOUR`) & Tròn Đều (`1:1` — `IMAGE_ASPECT_RATIO_SQUARE`) Với Lưới `4×4` (16 Frames Thu Nhỏ Trong BBox)

### Mẫu Dọc `3:4` (`4×4 = 16 Frames`): Cột Lốc Xoáy Sấm Sét / Tia Sét Thẳng Đứng Thu Nhỏ Trong BBox
```text
A borderless 3:4 vertical portrait animation sprite sheet containing exactly 16 isolated miniature hand-drawn 2D anime VFX animation frames of a CONTINUOUS ACTIVE LOOP OF A VERTICAL CRACKLING LIGHTNING TORNADO PILLAR, floating freely in open space on one single continuous unbroken flat pitch-black #000000 canvas, arranged in 4 rows of 4 columns (16 frames total), ordered left-to-right then top-to-bottom.

CRITICAL MINIATURE IN-BBOX CONTAINMENT & ZERO-CLIPPING RULE (MAX 42% BBOX SCALE!):
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, strictly NO white lines, NO grid lines, NO comic panel borders, NO divider lines, NO box outlines.
- MINIATURE SCALED-DOWN SPRITES INSIDE EACH 1/4 x 1/4 SLOT: Every single vertical lightning pillar INCLUDING its highest top spark, lowest base ring, and outermost soft cyan-blue plasma motion-blur shadow MUST be drawn as a compact miniature occupying at most 42% of its 1/4 x 1/4 slot, surrounded by wide empty pitch-black #000000 corridors (58% empty black padding on top, bottom, left, right) so ZERO glow and ZERO sparks ever cross or touch the bounding box!

CRITICAL SILKY MOTION-BLUR VERTICAL VORTEX LOOP ACROSS ALL 16 MINIATURE FRAMES:
- All 16 miniature frames maintain the exact same compact height centered in their 3:4 slots while helical white-cyan lightning strands and soft glowing cyan-blue motion-blur shadow trails spiral continuously upward and clockwise through a full 360-degree seamless cycle (22.5 degrees per frame from Frame 1 to Frame 16).
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
