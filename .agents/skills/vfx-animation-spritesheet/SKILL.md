---
name: vfx-animation-spritesheet
description: Tạo chuỗi ảnh 16-Frame VFX Animation Sprite Sheet (Multi-Sheet Sequential Chain: Mở -> Xoáy lặp -> Đóng, hoặc Giáng đòn -> Bùng nổ) trên Google Flow với chuyển động xoay/biến hình mượt mà qua từng frame, khóa phối cảnh 5 hướng mắt người và chọn màu nền phẳng không trùng chi tiết.
---

# Kỹ Năng Tạo Chuỗi VFX Animation Sprite Sheet Mượt Mà & Nối Tiếp (`/fk-gen-vfx-spritesheet`)

Kỹ năng này kết hợp trực tiếp với rule `.agents/rules/vfx_animation_spritesheet.md` để tạo **Chuỗi 16-Frame VFX Sprite Sheet Nối Tiếp (Multi-Sheet Sequential Chain)** trên Google Flow (`POST http://127.0.0.1:8100/flow/generate-image`), sau đó **bắt buộc tải ảnh về kiểm tra trực quan (visual verification)** độ mượt động học giữa các frame và tính nối tiếp giữa các tấm.

Xem chi tiết đầy đủ tại:
- Rule: `D:\flowkit-agent\.agents\rules\vfx_animation_spritesheet.md`
- Skill: `D:\flowkit-agent\agent-veo3\skills\fk-gen-vfx-spritesheet.md`
