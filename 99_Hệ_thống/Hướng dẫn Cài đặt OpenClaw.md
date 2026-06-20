---
type: system
status: evergreen
created: 2026-06-20
tags:
  - System/Setup
  - OpenClaw
---

# 🤖 Hướng dẫn kết nối OpenClaw (AI Agent always-on)

_Biến vault thành Bộ não thứ hai **tự vận hành**: một AI Agent (OpenClaw) chạy nền, tự dọn dẹp/ôn tập theo lịch và nhắn tin hỏi ý kiến bạn qua **Zalo hoặc Telegram**. Đây là điểm khác biệt lớn nhất của bộ khung này so với template chỉ-Obsidian._

> ⚠️ Bước này **tùy chọn**. Không có OpenClaw, vault vẫn dùng tốt 100% như một second brain Obsidian thường.

---

## 🅰️ Cách 1 — Dùng OpenClaw trực tiếp (KHÔNG cần Docker) ✅ khuyên dùng

Đơn giản nhất: chạy OpenClaw thẳng trên máy, cho agent đọc/ghi thẳng vào thư mục vault thật.

1. **Cài OpenClaw CLI** theo tài liệu chính thức (ví dụ qua npm / `create-openclaw-bot`).
2. **Khởi động agent**: chạy lệnh `openclaw` để bật gateway trên máy bạn.
3. **Trỏ tới vault**: vì chạy trực tiếp, đường dẫn vault chính là **thư mục thật** trên ổ đĩa — ví dụ `D:\vietbrain`. **Không cần mount, không cần `/mnt/secondbrain`.**
4. **Kết nối kênh chat**: cấu hình kênh **Zalo** *hoặc* **Telegram** trong OpenClaw để agent gửi/nhận tin (lấy ID/token theo hướng dẫn của từng kênh).
5. **Điền AGENT.md** (xem mục dưới) với đường dẫn vault thật + ID kênh chat của bạn.

> 💡 Cách này nhẹ, dễ debug, hợp cho cá nhân chạy trên 1 máy. Khi nào cần cô lập/triển khai server thì chuyển sang Cách 2.

---

## 🅱️ Cách 2 — Chạy bằng Docker (nâng cao, để cô lập/triển khai server)

1. Trong `docker-compose.yml` của OpenClaw, mount vault vào container:
   ```yaml
   volumes:
     - "D:\\vietbrain:/mnt/secondbrain"
   ```
2. Trong container, vault nằm tại `/mnt/secondbrain` — dùng đường dẫn này trong AGENT.md thay cho đường dẫn thật.
3. Còn lại (kênh chat, cron, AGENT.md) giống Cách 1.

---

## 📝 Điền thông tin vào AGENT.md
Mở [[AGENT|AGENT.md]] ở thư mục gốc, thay mọi chỗ `<...>`: tên agent, tên bạn, **kênh chat (Zalo hoặc Telegram) + ID/token**, đường dẫn vault, danh sách dự án/lĩnh vực.

---

## ⏰ Tạo Cron Job tự bảo trì
Tạo các cron job (dùng tính năng Cron sẵn có của OpenClaw) để agent tự quét vault và **gửi đề xuất qua Zalo/Telegram trước khi làm** (không bao giờ tự xóa file). Ví dụ message cho từng job:

| Tần suất | Lịch (cron) | Nội dung message |
|---|---|---|
| **Tuần** | `0 9 * * 0` | "Quét vault theo `99_Hệ_thống/Review & Cleanup.md`. Liệt kê file Inbox cần xử lý, tag cần gộp/xóa, dự án cần lưu trữ, file đính kèm thừa. Gửi đề xuất qua Zalo/Telegram, chờ duyệt." |
| **Tháng** | `30 9 1 * *` | "Quét các note `status: growing`, đề xuất note nào nên nâng lên `permanent`/`evergreen`. Hỏi ý kiến qua kênh chat." |
| **Quý** | `0 10 1 */3 *` | "Rà soát Bản đồ tri thức `50_Bản_đồ/`, đề xuất cải thiện liên kết. Báo cáo qua kênh chat." |
| **Nửa năm** | `30 10 1 */6 *` | "Đề xuất chuyển dự án/chủ đề ngừng hoạt động từ `01_brain_dự_án/` sang `90_Lưu_trữ/`. Hỏi ý kiến trước khi thực hiện." |

> 🔒 **Nguyên tắc an toàn:** Agent luôn liệt kê đề xuất và chờ bạn duyệt qua tin nhắn. TUYỆT ĐỐI không tự xóa/di chuyển file.

---

## 🔗 Liên kết
- [[AGENT|🤖 Cẩm nang cho AI Agent]]
- [[99_Hệ_thống/Review & Cleanup|🗄️ Quy trình Dọn dẹp]]
- Cài đặt OpenClaw: tham khảo repo/tài liệu chính thức của OpenClaw (`create-openclaw-bot`).
