---
name: vfx-animation-spritesheet
description: Tạo ảnh 16-Frame VFX Animation Sprite Sheet (lưới 4x4 tỉ lệ 1:1) trên Google Flow với khóa phối cảnh mắt người chuẩn xác cho 5 hướng (Mặt đất, Trên trời, Bên trái, Bên phải, Chính diện), tự động tải ảnh về và kiểm tra nghiệm thu.
---

# Kỹ Năng Tạo VFX Animation Sprite Sheet 16 Frames Theo 5 Hướng Mắt Người (`/fk-gen-vfx-spritesheet`)

Kỹ năng này kết hợp trực tiếp với rule `.agents/rules/vfx_animation_spritesheet.md` để tạo **16-Frame VFX Sprite Sheet (4×4 Grid, 1:1 Square)** trên Google Flow (`POST http://127.0.0.1:8100/flow/generate-image`), sau đó **bắt buộc tải ảnh về kiểm tra trực quan (visual verification)** xem toàn bộ 16 frames (đặc biệt là các frame giữa 6–12) có giữ đúng góc nhìn phối cảnh mắt người, không kẻ khung viền và không chạm mép hay không.

---

## 1. Nguyên Tắc Cốt Lõi: Hình Dung Theo Góc Mắt Con Người & Phản Chiếu Quang Học

Khi hiệu ứng nằm trên nền Chroma phẳng (không có sàn đất hay tường thật), mắt người nhận biết hướng của hiệu ứng dựa trên **Độ nén thấu thị (Foreshortening Ratio)**, **Độ dày/sáng của viền gần so với viền xa (Near/Far Rim Reflection)** và **Hướng phóng tia sáng vuông góc với mặt phẳng (Normal Emission Vector)**:

1. **Hướng Mặt Đất (`GROUND / FLOOR PLANE`)**:
   - **Hình học khóa cứng 16/16 frames**: Hình **Elip Dẹp Nằm Ngang (`Pancake-Flat Horizontal Ellipse`, tỉ lệ Rộng:Cao = `4:1`)**.
   - **Phản chiếu mắt người**: Nhìn từ trên chếch xuống sàn (~15°–20°). Viền dưới (gần mắt) dày và sáng rực hơn viền trên (xa mắt); thấy thành trong phía xa của hố xoáy; các tia sáng/cột năng lượng ngắn gọn **phụt thẳng đứng từ dưới LÊN TRÊN (`90° vertical upward rays`)**.
   - **CẤM TUYỆT ĐỐI** dùng từ `circular`, `circle`, `round` khiến Frame 6–12 bị dựng đứng thành hình tròn chính diện.

2. **Hướng Trên Trời (`SKY / OVERHEAD / CEILING PLANE`)**:
   - **Hình học khóa cứng 16/16 frames**: Hình **Elip Dẹp Nằm Ngang (`Pancake-Flat Horizontal Ellipse`, tỉ lệ Rộng:Cao = `4:1`)**.
   - **Phản chiếu mắt người**: Nhìn từ dưới đất ngước lên đáy cổng trên trời (~15°–20°). Thấy lòng vòm bên trong từ dưới nhìn lên; các tia sáng/hạt năng lượng ngắn gọn **giáng thẳng đứng từ trên XUỐNG DƯỚI (`90° vertical downward rays`)** bên dưới đĩa elip.

3. **Hướng Bên Trái (`LEFT-SIDE VERTICAL PLANE` — Cổng dựng đứng bên trái, hướng sang phải)**:
   - **Hình học khóa cứng 16/16 frames**: Hình **Elip Hẹp Dựng Đứng (`Narrow Standing Vertical Ellipse`, tỉ lệ Rộng:Cao = `1:4`, cao gấp 4 lần rộng)**.
   - **Phản chiếu mắt người**: Mặt phẳng cổng dựng đứng vuông góc mặt đất ở bên trái, mặt xoáy nhìn sang bên phải (hướng 3 giờ). Viền trái (gần mắt) sáng dày hơn, thấy lòng thành trong mở sang bên phải; tia sáng ngắn gọn **phóng ngang từ Trái sang Phải (`horizontal left-to-right emission`)**.

4. **Hướng Bên Phải (`RIGHT-SIDE VERTICAL PLANE` — Cổng dựng đứng bên phải, hướng sang trái)**:
   - **Hình học khóa cứng 16/16 frames**: Hình **Elip Hẹp Dựng Đứng (`Narrow Standing Vertical Ellipse`, tỉ lệ Rộng:Cao = `1:4`, cao gấp 4 lần rộng)**.
   - **Phản chiếu mắt người**: Mặt phẳng cổng dựng đứng vuông góc mặt đất ở bên phải, mặt xoáy nhìn sang bên trái (hướng 9 giờ). Viền phải (gần mắt) sáng dày hơn, thấy lòng thành trong mở sang bên trái; tia sáng ngắn gọn **phóng ngang từ Phải sang Trái (`horizontal right-to-left emission`)**.

5. **Hướng Chính Diện (`FRONT-FACING / DIRECT 0° VIEW`)**:
   - **Hình học khóa cứng 16/16 frames**: **Hình Tròn Đối Xứng Tâm Hoàn Hảo (`Perfect 1:1 Front-Facing Circle`, tỉ lệ `1:1`)**.
   - **Phản chiếu mắt người**: Nhìn thẳng trực diện 0° vào tâm hố xoáy; chiều sâu hút thẳng vào màn hình; tua năng lượng ngắn gọn **tỏa tròn đều 360° (`radial symmetry`)**.

> ⚠️ **LƯU Ý CHỐNG VẼ ĐƯỜNG KẺ Ô (`ANTI-GRID-LINE LOCK`)**:
> Tuyệt đối **KHÔNG dùng từ `"cell"`, `"each cell"`, `"per cell"`, `"grid box"`** trong prompt tiếng Anh vì từ `"cell"` sẽ khiến AI vẽ đường kẻ khung đen/hồng chia 16 ô! Hãy luôn dùng: `"All 16 frames float freely in open space on one single seamless unbroken #FF00FF magenta background with a minimum 30% empty magenta safety margin around every frame."`

---

## 2. Mẫu Prompt Chuẩn Cho 5 Hướng (Đã Khóa Chống Trôi Góc Nhìn Frames 6–12 & Chống Viền Lưới)

### A. Mẫu Prompt Hướng Mặt Đất (`GROUND PLANE`)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 separate animation frames of an opening and closing ground-level dimensional warp portal, floating on a continuous unbroken flat magenta #FF00FF canvas, neatly spaced in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL COLOR SEPARATION & ISOLATION:
- Background: 100% solid flat pure magenta #FF00FF everywhere, completely uniform across the entire image, no gradient, no drop shadow.
- ZERO-SPILL: Absolutely NO magenta or pink inside the cyan vortex.
- PURE VFX ONLY: No props, no terrain, no ground lines, no horizon.

ZERO-BORDER & BOUNDING BOX CONSTRAINTS:
- Strictly NO grid lines, NO black lines, NO borders, NO divider lines, NO box outlines, NO text, NO numbers. All 16 frames float freely on one single seamless unbroken #FF00FF magenta background with a minimum 25% empty magenta safety margin around every frame.

CRITICAL HUMAN-EYE GROUND PERSPECTIVE & 16-FRAME FLAT ELLIPSE LOCK:
- Viewed from a fixed low grazing human eye angle (~15 degrees above an invisible floor).
- In ALL 16 FRAMES (strictly including peak frames 6, 7, 8, 9, 10, 11, 12), the portal MUST remain a VERY FLAT PANCAKE HORIZONTAL ELLIPSE (strictly 4:1 width-to-height ratio, wide horizontally and very compressed/flat vertically) lying flat on the invisible floor.
- STRICTLY FORBIDDEN to draw a round 1:1 circle or front-facing swirl in ANY frame! Even at maximum size in frames 6 to 12, it ONLY stretches wider horizontally as a flat 4:1 saucer disc and NEVER tilts up toward the camera.
- HUMAN-EYE OPTICAL REFLECTION: The bottom/front rim (closest to viewer's eye) is thicker and brighter glowing white-cyan, while the top/far rim is thinner and reveals the recessed inner back wall of the vortex. Compact luminous cyan light rays shoot strictly vertically UPWARD (90 degrees straight up) from the flat horizontal ring.

Subject & Style: dynamic 2D hand-drawn anime cartoon VFX, spinning plasma whirlpool, luminous cyan rim, deep electric blue vortex core, short rising vertical white-cyan light rays.

Temporal Animation Progression (16 frames - ALL locked to 4:1 flat horizontal ellipse):
- Frames 1 to 3 (Opening): A thin glowing horizontal slit opens flat on the invisible floor and widens into a small 4:1 flat horizontal ellipse emitting short upward vertical light rays.
- Frames 4 to 6 (Horizontal Expansion): The flat horizontal ellipse stretches wider left-to-right (strictly maintaining its compressed 4:1 pancake-flat horizontal ratio, ZERO circular shape), with short vertical light rays rising straight up.
- Frames 7 to 9 (Full Ground Aperture): The ground portal reaches maximum horizontal width while remaining a strictly flattened 4:1 pancake horizontal ellipse lying flat on the floor (NEVER a front-facing circle), spinning with deep recessed inner rim reflection and short vertical upward light beams.
- Frames 10 to 12 (Horizontal Contraction): The flat 4:1 horizontal elliptical vortex shrinks narrower in width while staying completely flat on the horizontal floor plane, upward rays fading.
- Frames 13 to 16 (Closure): Collapses back into a thin horizontal spark line and dissolves 100% into pure magenta.
```

### B. Mẫu Prompt Hướng Trên Trời (`SKY / OVERHEAD PLANE`)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 separate animation frames of an opening and closing sky-high overhead dimensional warp portal, floating on a continuous unbroken flat magenta #FF00FF canvas, neatly spaced in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL COLOR SEPARATION & ISOLATION:
- Background: 100% solid flat pure magenta #FF00FF everywhere, completely uniform across the entire image, no gradient, no drop shadow.
- ZERO-SPILL: Absolutely NO magenta or pink inside the cyan vortex.
- PURE VFX ONLY: No clouds, no sky, no props, no terrain, no horizon.

ZERO-BORDER & BOUNDING BOX CONSTRAINTS:
- Strictly NO grid lines, NO black lines, NO borders, NO divider lines, NO box outlines, NO text, NO numbers. All 16 frames float freely in open space on one single seamless unbroken #FF00FF magenta background. Every frame (including its light rays) is compact and small with at least 30% empty magenta safety margin on all four sides so nothing ever touches or nears the neighboring frames.

CRITICAL HUMAN-EYE LOOKING-UP SKY PERSPECTIVE & 16-FRAME FLAT ELLIPSE LOCK:
- Viewed from a human standing on the ground looking steeply UPWARD (~15 degrees grazing angle) at the underside of a floating overhead portal.
- In ALL 16 FRAMES (strictly including peak frames 6, 7, 8, 9, 10, 11, 12), the overhead portal ring MUST remain a VERY FLAT PANCAKE HORIZONTAL ELLIPSE (strictly 4:1 width-to-height ratio, wide horizontally and very compressed/flat vertically).
- STRICTLY FORBIDDEN to draw a round 1:1 front-facing circle in ANY frame!
- HUMAN-EYE OPTICAL REFLECTION: Looking up into the recessed dark-blue underside of the flat horizontal ring, the upper/far rim arches over the recessed vortex core, and SHORT, COMPACT tapering cyan light rays shine strictly vertically DOWNWARD (90 degrees straight down) underneath the flat horizontal ellipse, fading out quickly so they stay compact and well inside the empty magenta space.

Subject & Style: dynamic 2D hand-drawn anime cartoon VFX, spinning plasma whirlpool, luminous cyan rim, deep electric blue recessed underside vortex, short compact downward-pointing white-cyan light rays.

Temporal Animation Progression (16 frames - ALL locked to 4:1 flat horizontal overhead ellipse with short downward rays):
- Frames 1 to 3 (Opening): A thin glowing horizontal slit opens and widens into a small 4:1 flat horizontal ellipse emitting short faint downward vertical light rays underneath.
- Frames 4 to 6 (Horizontal Expansion): The flat 4:1 horizontal ellipse stretches wider left-to-right (strictly maintaining its compressed 4:1 pancake-flat horizontal ratio, ZERO circular shape), with short tapering vertical light rays shining straight down from its underside.
- Frames 7 to 9 (Full Sky Aperture): The overhead portal reaches maximum horizontal width while remaining a strictly flattened 4:1 pancake horizontal ellipse (NEVER a front-facing circle), with short compact vertical cyan light rays shining straight downward underneath the ring.
- Frames 10 to 12 (Horizontal Contraction): The flat 4:1 horizontal elliptical vortex shrinks narrower in width while downward rays fade.
- Frames 13 to 16 (Closure): Collapses back into a faint horizontal spark line, then dissolves 100% into pure magenta.
```

### C. Mẫu Prompt Hướng Bên Trái (`LEFT-SIDE VERTICAL PLANE` — Mặt hướng sang Phải)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 separate animation frames of an opening and closing vertical side-angled dimensional warp portal facing right, floating on a continuous unbroken flat magenta #FF00FF canvas, neatly spaced in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL COLOR SEPARATION & ISOLATION:
- Background: 100% solid flat pure neon magenta #FF00FF everywhere, completely uniform brightness across all 16 frames, no dark purple tint, no gradient, no shadow.
- ZERO-SPILL: Absolutely NO magenta inside the cyan vortex.
- PURE VFX ONLY: No props, no walls, no terrain.

ZERO-BORDER & BOUNDING BOX CONSTRAINTS:
- Strictly NO grid lines, NO pink or black divider lines, NO borders, NO box outlines, NO separator lines. The entire image is ONE single seamless unbroken flat #FF00FF magenta sheet where the 16 small isolated portals float freely with generous 30% empty magenta margin on all sides.

CRITICAL HUMAN-EYE LEFT-SIDE PERSPECTIVE & 16-FRAME NARROW VERTICAL ELLIPSE LOCK:
- Viewed from human eye level looking at a vertical portal plane standing perpendicular to the floor on the left, with its vortex mouth facing RIGHT (towards 3 o'clock).
- In ALL 16 FRAMES (strictly including peak frames 6, 7, 8, 9, 10, 11, 12), the portal MUST remain a NARROW STANDING VERTICAL ELLIPSE (strictly 1:4 width-to-height ratio — tall vertically and compressed very thin horizontally).
- STRICTLY FORBIDDEN to draw a round 1:1 front-facing circle or a horizontal ellipse in ANY frame!
- HUMAN-EYE OPTICAL REFLECTION: The left/front vertical rim (closest to viewer's eye) is thicker and brighter white-cyan, while the right/far vertical rim is thinner and reveals the recessed inner blue vortex wall facing right. SHORT, COMPACT tapering cyan light rays shoot strictly HORIZONTALLY from LEFT TO RIGHT (towards 3 o'clock) from the vertical elliptical ring and fade out quickly inside the safe margin.

Subject & Style: dynamic 2D hand-drawn anime cartoon VFX, tall thin vertical spinning plasma whirlpool, luminous cyan rim, deep electric blue vortex core, short compact horizontal rightward light rays.

Temporal Animation Progression (16 frames - ALL locked to 1:4 narrow standing vertical ellipse facing right):
- Frames 1 to 3 (Opening): A thin vertical glowing slit opens upright and widens slightly into a small 1:4 narrow standing vertical ellipse emitting short horizontal light rays to the right.
- Frames 4 to 6 (Vertical Expansion): The narrow 1:4 standing vertical ellipse stretches taller top-to-bottom (strictly maintaining its compressed 1:4 thin vertical ratio, ZERO circular shape), with short horizontal cyan light rays pointing right.
- Frames 7 to 9 (Full Vertical Aperture): The side portal reaches maximum vertical height while remaining a strictly thin 1:4 standing vertical ellipse facing right (NEVER a front-facing circle), spinning with recessed inner rim depth and short compact horizontal rightward light rays.
- Frames 10 to 12 (Vertical Contraction): The thin 1:4 standing vertical elliptical vortex shrinks shorter vertically while staying narrow horizontally, rightward rays fading.
- Frames 13 to 16 (Closure): Snaps shut into a faint vertical spark line, then dissolves 100% into pure magenta.
```

### D. Mẫu Prompt Hướng Bên Phải (`RIGHT-SIDE VERTICAL PLANE` — Mặt hướng sang Trái)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 separate animation frames of an opening and closing vertical side-angled dimensional warp portal facing left, floating on a continuous unbroken flat magenta #FF00FF canvas, neatly spaced in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL COLOR SEPARATION & ISOLATION:
- Background: 100% solid flat pure neon magenta #FF00FF everywhere, completely uniform brightness across all 16 frames, no gradient, no shadow.
- ZERO-SPILL: Absolutely NO magenta inside the cyan vortex.
- PURE VFX ONLY: No props, no walls, no terrain.

ZERO-BORDER & BOUNDING BOX CONSTRAINTS:
- Strictly NO grid lines, NO black lines, NO borders, NO divider lines, NO box outlines, NO separator lines. The entire image is ONE single seamless unbroken flat #FF00FF magenta sheet where the 16 small isolated portals float freely with generous 30% empty magenta margin on all sides.

CRITICAL HUMAN-EYE RIGHT-SIDE PERSPECTIVE & 16-FRAME NARROW VERTICAL ELLIPSE LOCK:
- Viewed from human eye level looking at a vertical portal plane standing perpendicular to the floor on the right, with its vortex mouth facing LEFT (towards 9 o'clock).
- In ALL 16 FRAMES (strictly including peak frames 6, 7, 8, 9, 10, 11, 12), the portal MUST remain a NARROW STANDING VERTICAL ELLIPSE (strictly 1:4 width-to-height ratio — tall vertically and compressed very thin horizontally).
- STRICTLY FORBIDDEN to draw a round 1:1 front-facing circle or a horizontal ellipse in ANY frame!
- HUMAN-EYE OPTICAL REFLECTION: The right/front vertical rim (closest to viewer's eye) is thicker and brighter white-cyan, while the left/far vertical rim is thinner and reveals the recessed inner blue vortex wall facing left. SHORT, COMPACT tapering cyan light rays shoot strictly HORIZONTALLY from RIGHT TO LEFT (towards 9 o'clock) from the vertical elliptical ring and fade out quickly inside the safe margin.

Subject & Style: dynamic 2D hand-drawn anime cartoon VFX, tall thin vertical spinning plasma whirlpool, luminous cyan rim, deep electric blue vortex core, short compact horizontal leftward light rays.

Temporal Animation Progression (16 frames - ALL locked to 1:4 narrow standing vertical ellipse facing left):
- Frames 1 to 3 (Opening): A thin vertical glowing slit opens upright and widens slightly into a small 1:4 narrow standing vertical ellipse emitting short horizontal light rays to the left.
- Frames 4 to 6 (Vertical Expansion): The narrow 1:4 standing vertical ellipse stretches taller top-to-bottom (strictly maintaining its compressed 1:4 thin vertical ratio, ZERO circular shape), with short horizontal cyan light rays pointing left.
- Frames 7 to 9 (Full Vertical Aperture): The side portal reaches maximum vertical height while remaining a strictly thin 1:4 standing vertical ellipse facing left (NEVER a front-facing circle), spinning with recessed inner rim depth and short compact horizontal leftward light rays.
- Frames 10 to 12 (Vertical Contraction): The thin 1:4 standing vertical elliptical vortex shrinks shorter vertically while staying narrow horizontally, leftward rays fading.
- Frames 13 to 16 (Closure): Snaps shut into a faint vertical spark line, then dissolves 100% into pure magenta.
```

### E. Mẫu Prompt Hướng Chính Diện (`FRONT-FACING / DIRECT 0° VIEW`)
```text
A borderless square 1:1 animation sprite sheet containing exactly 16 separate animation frames of an opening and closing direct front-facing dimensional warp portal, floating on a continuous unbroken flat magenta #FF00FF canvas, neatly spaced in 4 rows of 4 columns, ordered left-to-right then top-to-bottom.

CRITICAL COLOR SEPARATION & ISOLATION:
- Background: 100% solid flat pure neon magenta #FF00FF everywhere, completely uniform across the entire image, no gradient, no drop shadow.
- ZERO-SPILL: Absolutely NO magenta inside the cyan vortex.
- PURE VFX ONLY: No props, no terrain.

ZERO-BORDER & BOUNDING BOX CONSTRAINTS:
- Strictly NO grid lines, NO black lines, NO borders, NO divider lines, NO box outlines, NO separator lines. The entire image is ONE single seamless unbroken flat #FF00FF magenta sheet where the 16 small isolated portals float freely with generous 30% empty magenta margin on all sides so no tendrils ever touch or clip.

CRITICAL HUMAN-EYE DIRECT 0-DEGREE FRONT PERSPECTIVE & 1:1 CIRCLE LOCK:
- Viewed 100% directly head-on at 0-degree orthographic front view facing the viewer's eyes.
- In ALL 16 FRAMES, the portal is a PERFECTLY SYMMETRIC 1:1 ROUND CIRCLE with concentric vortex depth pulling straight into the center of the screen, and short compact energy tendrils radiating evenly 360 degrees around the circular rim.
- STRICTLY FORBIDDEN to draw a flat horizontal or vertical ellipse; every frame must be a small, well-contained symmetric 1:1 circle.

Subject & Style: dynamic 2D hand-drawn anime cartoon VFX, spinning circular cyan-blue plasma whirlpool, concentric glowing cyan rim, deep electric blue vortex core, short compact 360-degree radial white energy tendrils.

Temporal Animation Progression (16 frames - ALL locked to 1:1 concentric front-facing circle):
- Frames 1 to 3 (Opening): A glowing central point spark opens and expands into a small concentric 1:1 circular ring.
- Frames 4 to 6 (Radial Expansion): The 1:1 circular vortex expands symmetrically outward with spinning cyan plasma swirl.
- Frames 7 to 9 (Full Circular Aperture): The warp gate reaches a moderate maximum 1:1 circular size (staying small enough to keep 30% empty magenta margin around it), with symmetric concentric depth and short compact orbiting energy tendrils.
- Frames 10 to 12 (Radial Contraction): The 1:1 circular portal shrinks symmetrically inward toward its center.
- Frames 13 to 16 (Closure): Snaps shut into a tiny central spark dot, then dissolves 100% into pure magenta.
```
