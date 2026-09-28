# BÁO CÁO BÀI TẬP LỚN / THỰC HÀNH LẬP TRÌNH WEB

### Thông tin sinh viên:
- **Họ và tên:** Mông Chí Thành
- **Lớp:** 59KMT
- **Mã sinh viên:** k235480106066

---

## 1. Kiến trúc hệ thống & Công nghệ sử dụng
Hệ thống được đóng gói và vận hành hoàn toàn bằng Docker Compose, bao gồm:
- **Nginx (Reverse Proxy & Web Server):** Tiếp nhận luồng từ Internet, phân định tên miền (Virtual Hosts) và định tuyến API backend.
- **Node-RED (Backend & API Engine):** Xử lý logic đăng nhập, xác thực và kết nối Database. Đã cấu hình bảo mật `adminAuth` bằng bcrypt hash trong `settings.js`.
- **MariaDB & phpMyAdmin (Database Layer):** Lưu trữ bảng thông tin người dùng (`users`), quản trị qua giao diện web.
- **Cloudflare Zero Trust (Cloudflare Tunnel):** Tạo đường hầm bảo mật từ mạng nội bộ ra Internet toàn cầu, tự động mã hóa HTTPS/TLS.

---

## 2. Phân chia tên miền & Virtual Host
Hệ thống sử dụng Nginx để định tuyến độc lập 2 tên miền trên cùng một IP máy chủ:
- **Website 1:** `https://thanh59kmt.click` (Trỏ về `html/site1`, giao tiếp API `/api/` với Node-RED).
- **Website 2:** `https://thanhkmt.click` (Trỏ về `html/site2`, hoạt động độc lập chứng minh tính năng multi-domain).

---

## 3. Hướng dẫn khởi chạy hệ thống (Deployment)
1. **Khởi chạy cụm dịch vụ:**
   ```bash
   docker compose up -d