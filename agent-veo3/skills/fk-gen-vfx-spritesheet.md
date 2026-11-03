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

## 1. Quy Tắc Màu Nền Linh Hoạt (Không Cố Định Tím/Xanh Lá)

- **KHÔNG bắt buộc dùng nền Tím (`#FF00FF`) hay Xanh lá (`#00FF00`)**.
- Được phép chọn **bất kỳ màu nền phẳng đơn sắc nào** (ví dụ: Đen tuyền `#000000`, Than chì tối `#141414`, Xanh đêm `#0A0E1A`, Đỏ rượu sẫm `#2A0510`, v.v.) **MIỄN LÀ**:
  1. Màu nền đó **không trùng với bất kỳ màu chi tiết nào của hiệu ứng**.
  2. Nền phẳng 100% không viền ô, không kẻ lưới (tuyệt đối không dùng từ `"cell"` trong prompt).
  3. Các tấm trong cùng 1 chuỗi nối tiếp dùng chung cùng 1 mã màu nền.

---

## 2. Bộ Mẫu Prompt Chuẩn Cho Chuỗi Cổng Dịch Chuyển 3 Tấm (Mặt Đất - Nền Đen `#000000` Hoặc Tùy Chọn)

### Tấm 1: Pha Khởi Tạo & Mở Cổng (`Sheet 1 of 3: Portal Ignition & Opening To Full Size — Silky Motion-Blur Shadow Edition`)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 sequential hand-drawn animation frames of PHASE 1 ONLY (IGNITION AND OPENING EXPANSION) of a ground-level dimensional warp portal, floating freely on a single continuous unbroken flat pitch-black #000000 canvas, neatly arranged in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL BACKGROUND & ZERO-BORDER CONSTRAINTS:
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, no gradient, no floor texture, no horizon line.
- Strictly NO grid lines, NO borders, NO divider lines, NO box outlines, NO text, NO numbers. All 16 frames float freely in open space with 30% empty black margin around every frame.

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & 4:1 FLAT ELLIPSE LOCK:
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor): every single frame MUST be a VERY FLAT PANCAKE HORIZONTAL ELLIPSE (strictly 4:1 width-to-height ratio) lying flat on the invisible floor. STRICTLY FORBIDDEN to draw a round 1:1 front-facing circle in any frame.

CRITICAL SILKY MOTION-BLUR SHADOW & ETHEREAL MIST IN ALL 16 FRAMES (NO HARD CHALKY RINGS!):
- EVERY SINGLE FRAME from Frame 1 through Frame 16 (strictly including Frames 6, 12, 15, and 16!) MUST feature a rich, soft, semi-transparent cyan-blue MOTION-BLUR AFTERIMAGE SHADOW TRAIL and a Swirling Diffused Ethereal Mist Glow rising and curving smoothly with the vortex rotation!
- STRICTLY FORBIDDEN to drop or lose the soft glowing mist shadow in Frames 15 and 16! STRICTLY FORBIDDEN to draw hard-edged solid chalky white rings! Every spiral ribbon has soft airbrushed feathered edges blending smoothly into its trailing translucent cyan-blue motion-blur shadow, giving the spinning vortex a supple, silky, liquid-smooth feel.

CRITICAL KINETIC FLUIDITY & CONTINUITY HANDSHAKE (END AT 100% OPEN IN FRAME 16 WITH FULL MISTY GLOW!):
- Frame-by-frame progression (16 frames of smooth silky opening ONLY):
  - Frames 1 to 4 (Crack & Misty Spark Ignition): A horizontal electric spark cracks open into a small swirling 4:1 flat horizontal ellipse wrapped in soft translucent cyan motion-blur mist as the silky spiral ribbons sweep clockwise from 12 o'clock to 3 o'clock.
  - Frames 5 to 8 (Accelerating Silky Swirl Expansion): The flat 4:1 horizontal vortex spins clockwise (sweeping 3 o'clock to 6 o'clock), growing to 55% width with soft feathered plasma ribbons, trailing motion-blur ghost shadows, and swirling upward mist veil.
  - Frames 9 to 12 (Surging Vortex Widening): The flat 4:1 horizontal ellipse expands from 65% to 88% width (sweeping 6 o'clock to 9 o'clock) with continuous soft volumetric mist glow and curving motion-blur afterimage trails in every frame.
  - Frames 13 to 16 (Reaching 100% Full Open Aperture - KEEP FULL SOFT MOTION-BLUR MIST GLOW IN FRAMES 15 & 16!): The flat 4:1 horizontal ground portal reaches 100% full width in Frames 13-16 while maintaining the EXACT SAME rich, soft rising ethereal mist glow and silky motion-blur shadow trails as Frames 7-14 (strictly NO bare hard rings in Frames 15-16!).
```

### Tấm 2: Pha Duy Trì Vòng Xoáy Liên Tục (`Sheet 2 of 3: Continuous Active Spinning Loop at 100% Full Size — Silky Motion-Blur Shadow Edition`)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 sequential hand-drawn 2D anime VFX animation frames of PHASE 2 (CONTINUOUS SPINNING VORTEX LOOP AT CONSTANT FULL SIZE) of a ground-level dimensional warp portal, floating freely on a single continuous unbroken flat pitch-black #000000 canvas, neatly arranged in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL BACKGROUND & ZERO-BORDER CONSTRAINTS:
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, no grid lines, no borders, no divider lines, no box outlines, no text. 30% empty black safety margin around every frame.

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & 4:1 FLAT HORIZONTAL VORTEX LOCK:
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor): every single frame from Frame 1 to Frame 16 is a flat horizontal swirling whirlpool (3.5:1 width-to-height ratio) lying level and flat on the invisible floor.

CRITICAL SILKY MOTION-BLUR SHADOW & ETHEREAL MIST IN ALL 16 FRAMES (NO HARD RINGS, NO EMPTY RINGS!):
- EVERY SINGLE FRAME from Frame 1 through Frame 16 (strictly including Frame 1 and Frame 16!) MUST be a rich, full, multi-layered swirling whirlpool with soft feathered plasma ribbons, trailing semi-transparent cyan-blue MOTION-BLUR AFTERIMAGE SHADOW TRAILS, and a soft Swirling Diffused Ethereal Mist Veil rising above the vortex!
- STRICTLY FORBIDDEN to draw bare empty rings in Frame 1 or Frame 16! STRICTLY FORBIDDEN to draw a hard-edged solid white outer ring outline! Every spiral arm has soft airbrushed feathered edges that blend into dreamy translucent cyan-blue motion-blur smoke and mist.

CRITICAL CONTINUOUS CLOCKWISE SWIRLING FLOW ACROSS ALL 16 FRAMES:
- All 16 frames stay at the exact same 100% full open width while the silky feathered spiral arms, their soft motion-blur ghost shadows, and the rising misty vapor veil swirl continuously clockwise:
  - Frames 1 to 4: Full swirling horizontal vortex at 100% size wrapped in soft translucent cyan motion-blur mist as the silky spiral ribbons and feathered ghost trails sweep clockwise from 12 o'clock to 3 o'clock.
  - Frames 5 to 8: The full-size flat horizontal vortex continues spinning clockwise (sweeping 3 o'clock to 6 o'clock) with soft feathered plasma ribbons, trailing motion-blur ghost shadows, and swirling upward mist veil.
  - Frames 9 to 12: The full-size flat horizontal vortex spins clockwise (sweeping 6 o'clock to 9 o'clock) with continuous soft volumetric mist glow and curving motion-blur afterimage trails.
  - Frames 13 to 16: The full-size flat horizontal vortex completes its clockwise rotation (sweeping 9 o'clock to 12 o'clock) while maintaining the EXACT SAME rich internal spiral ribbons, soft rising ethereal mist glow, and silky motion-blur shadow trails in Frame 15 and Frame 16 (strictly NO empty ring in Frame 16!).
```

### Tấm 3: Pha Thu Hẹp & Đóng Cổng (`Sheet 3 of 3: Vortex Collapse & Dissipation From Full Size`)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 sequential hand-drawn animation frames of PHASE 3 ONLY (IMPLOSION, CONTRACTION AND CLOSURE STARTING FROM 100% FULL SIZE) of a ground-level dimensional warp portal, floating freely on a single continuous unbroken flat pitch-black #000000 canvas, neatly arranged in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL BACKGROUND & ZERO-BORDER CONSTRAINTS:
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, no grid lines, no borders, no divider lines, no box outlines, no text. 30% empty black safety margin around every frame.

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & 4:1 FLAT ELLIPSE LOCK:
- In all active frames, the portal remains strictly locked as a 4:1 PANCAKE-FLAT HORIZONTAL ELLIPSE lying flat on an invisible floor (NEVER a round 1:1 circle).

CRITICAL CONTINUITY HANDSHAKE (START AT 100% FULL OPEN IN FRAME 1, THEN COLLAPSE):
- Frame 1 MUST start at 100% FULL OPEN WIDTH (continuing directly from Sheet 2), then progressively spin inward and collapse across the 16 frames:
  - Frames 1 to 4 (Initial Inward Suction from Full Size): Frame 1 is fully open (100% width) and spinning rapidly; across Frames 2 to 4, the flat 4:1 horizontal vortex begins pulling inward (to 80% width) with inward-curling plasma ribbons and rising sparks being sucked toward the core.
  - Frames 5 to 8 (Accelerating Vortex Contraction): The spinning flat 4:1 horizontal ellipse rapidly narrows from 70% down to 40% width as the spiral arms whip tightly around the center and upward rays shorten.
  - Frames 9 to 12 (Implosion & Snap Shut): The flat horizontal vortex collapses into a tight intense horizontal energy slit (Frames 9-10) and snaps shut with a horizontal burst of scattering cyan electric sparks (Frames 11-12).
  - Frames 13 to 16 (Spark Dissipation & Clean Fadeout): Lingering horizontal electric sparks and tiny glowing motes scatter and fade out (Frames 13-15), leaving Frame 16 completely 100% empty pitch-black #000000.
```

---

## 3. Bộ Mẫu Prompt Chuẩn Cho Chuỗi Nhân Quả: Sét Đánh Xuống Đất → Nổ Xung Kích Mặt Đất (2 Tấm Nối Tiếp)

### Tấm 1: Tia Sét Giáng Xuống & Chạm Đất (`Sheet 1 of 2: Lightning Bolt Sky Descent & Ground Contact`)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 sequential hand-drawn animation frames of PHASE 1 (JAGGED LIGHTNING BOLT DESCENDING FROM SKY AND STRIKING THE GROUND) on a single continuous unbroken flat pitch-black #000000 canvas, neatly arranged in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL BACKGROUND & ZERO-BORDER CONSTRAINTS:
- Background: 100% solid flat uniform pure pitch-black #000000 everywhere, no grid lines, no borders, no divider lines, no horizon line. 30% empty black safety margin around every frame.

CRITICAL KINETIC PROGRESSION & CONTINUITY HANDSHAKE TO SHEET 2:
- Every frame has freshly drawn jagged lightning geometry (no static copies):
  - Frames 1 to 4 (High Sky Emergence): A bright white-cyan electric leader arc crackles at the top and snakes downward through the upper space; bottom area is 100% empty black.
  - Frames 5 to 8 (Rapid Downward Bolt Extension): The jagged lightning bolt streaks further downward toward the lower third with branching side-arcs; still no ground contact yet.
  - Frames 9 to 12 (First Ground Contact & Flash): At Frame 9, the tip of the bolt hits the invisible floor plane, igniting a flat 4:1 horizontal elliptical contact ring and initial upward electric sparks.
  - Frames 13 to 16 (Peak Surge Charging the Impact Core): Massive surging energy pours down the main lightning column into a blinding white-cyan compressed plasma core sitting on a flat 4:1 horizontal floor ellipse at Frame 16, primed to detonate in Sheet 2!
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
