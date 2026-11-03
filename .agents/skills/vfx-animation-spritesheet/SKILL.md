---
name: vfx-animation-spritesheet
description: Tạo ảnh 16-Frame VFX Animation Sprite Sheet (lưới 4x4 tỉ lệ 1:1) trên Google Flow với khóa phối cảnh mắt người chuẩn xác cho 5 hướng (Mặt đất, Trên trời, Bên trái, Bên phải, Chính diện), tự động tải ảnh về và kiểm tra nghiệm thu.
---

# Kỹ Năng Tạo VFX Animation Sprite Sheet 16 Frames Theo 5 Hướng Mắt Người (`/fk-gen-vfx-spritesheet`)

Kỹ năng này kết hợp trực tiếp với rule `.agents/rules/vfx_animation_spritesheet.md` để tạo **16-Frame VFX Sprite Sheet (4×4 Grid, 1:1 Square)** trên Google Flow (`POST http://127.0.0.1:8100/flow/generate-image`), sau đó **bắt buộc tải ảnh về kiểm tra trực quan (visual verification)** xem toàn bộ 16 frames (đặc biệt là các frame giữa 6–12) có giữ đúng góc nhìn phối cảnh mắt người hay không.

Xem chi tiết đầy đủ tại:
- Rule: `D:\flowkit-agent\.agents\rules\vfx_animation_spritesheet.md`
- Skill: `D:\flowkit-agent\agent-veo3\skills\fk-gen-vfx-spritesheet.md`
