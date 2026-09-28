# BÁO CÁO BÀI TẬP VỀ NHÀ: LẬP TRÌNH WEB & AN TOÀN BẢO MẬT THÔNG TIN

* **Sinh viên thực hiện:** Mông Chí Thành
* **Lớp:** 59KMT
* **Mã sinh viên:** k235480106066
* **Hình thức nộp bài:** Mã nguồn và tài liệu được lưu trữ trên kho GitHub công khai.

---

## PHẦN 1: MÔN AN TOÀN VÀ BẢO MẬT THÔNG TIN

### 1. Phân tích thuật toán mã hoá hiện đại DES và AES
* **Thuật toán DES (Data Encryption Standard):**
  * **Kích thước:** Xử lý khối dữ liệu 64-bit; Độ dài khóa 56-bit (kèm 8-bit kiểm tra chẵn lẻ).
  * **Cấu trúc:** Sử dụng mạng Feistel lặp qua 16 vòng (rounds).
  * **Quy trình:** Khối dữ liệu đi qua hoán vị khởi tạo, chia làm 2 nửa trái/phải. Nửa phải đi qua hàm F (mở rộng, XOR với khóa con, nén qua S-Box, hoán vị P-Box) rồi XOR với nửa trái. Quá trình lặp lại 16 lần. Giải mã áp dụng thứ tự khóa con ngược lại.
  * **Đánh giá:** Không gian khóa $2^{56}$ quá nhỏ, dễ dàng bị phá bằng tấn công vét cạn (brute-force), không còn an toàn.
* **Thuật toán AES (Advanced Encryption Standard):**
  * **Kích thước:** Xử lý khối cố định 128-bit; Khóa linh hoạt 128-bit, 192-bit hoặc 256-bit.
  * **Cấu trúc:** Sử dụng mạng thay thế - hoán vị SPN (Substitution-Permutation Network).
  * **Quy trình:** Mỗi vòng lặp gồm 4 bước: `SubBytes` (thay thế qua S-Box), `ShiftRows` (dịch vòng các hàng), `MixColumns` (nhân ma trận trộn cột), `AddRoundKey` (XOR với khóa vòng). Giải mã dùng các hàm nghịch đảo tương ứng.
* **Cài đặt AES (Python):** Triển khai AES-256 ở chế độ CBC bằng thư viện `pycryptodome`, sử dụng vector khởi tạo (IV) ngẫu nhiên và chuẩn đệm PKCS#7.

### 2. Thuật toán mã hoá bất đối xứng RSA
* **Nguyên lý sinh cặp khóa:**
  1. Chọn hai số nguyên tố lớn ngẫu nhiên $p$ và $q$.
  2. Tính giá trị Modulo hệ thống $n = p \times q$.
  3. Tính phi hàm Euler $\phi(n) = (p - 1)(q - 1)$.
  4. Chọn số mũ công khai $e$ (thường là 65537) sao cho $\gcd(e, \phi(n)) = 1$.
  5. Tính số mũ bí mật $d$ là nghịch đảo modulo của $e$: $d \equiv e^{-1} \pmod{\phi(n)}$.
  6. **Khóa công khai:** $KU = \{e, n\}$; **Khóa bí mật:** $KR = \{d, n\}$.

### 3. Mô hình ứng dụng và Giải pháp mã hóa lai
* **Các mô hình áp dụng RSA:**
  * **Xác thực người nhận (Bảo mật):** Người gửi mã hóa bằng Khóa công khai của người nhận. Chỉ người nhận mới dùng Khóa bí mật để giải mã được.
  * **Xác thực người gửi (Chữ ký số):** Người gửi mã hóa bản băm bằng Khóa bí mật của mình. Người nhận dùng Khóa công khai của người gửi để xác minh chữ ký.
  * **Xác thực 2 chiều:** Ký bằng Khóa bí mật người gửi, sau đó mã hóa toàn bộ bằng Khóa công khai người nhận.
* **So sánh thời gian xử lý:** RSA tính toán lũy thừa số nguyên lớn nên chậm hơn AES từ hàng trăm đến hàng nghìn lần. AES xử lý trực tiếp trên khối 128-bit và tối ưu phần cứng nên tốc độ cực nhanh.
* **Kết hợp RSA và AES (Mã hóa lai):**
  * Dùng thuật toán AES sinh khóa phiên (Session Key) dùng một lần để mã hóa dữ liệu dung lượng lớn.
  * Dùng thuật toán RSA để mã hóa khóa phiên AES.
  * Người nhận dùng RSA giải mã lấy khóa AES, sau đó dùng khóa AES giải mã dữ liệu nội dung gốc.

---

## PHẦN 2: MÔN LẬP TRÌNH WEB

### Bài tập 1: Xây dựng hạ tầng hệ thống đa dịch vụ
1. **Giả lập hệ điều hành:** Sử dụng môi trường WSL 2 (Ubuntu 24.04 LTS).
2. **Cài đặt Docker Compose:** Triển khai cụm vi dịch vụ đồng bộ gồm:
   * **Nginx:** Máy chủ Web Server & Reverse Proxy điều hướng giao thông.
   * **Node-RED:** Nền tảng xây dựng Backend và API Engine (Cổng 1880).
   * **MariaDB:** Cơ sở dữ liệu quan hệ lưu trữ thông tin.
   * **phpMyAdmin:** Công cụ quản trị CSDL trực quan trên Web (Cổng 8080).
   * **Cloudflared:** Dịch vụ tạo Tunnel đưa ứng dụng nội bộ ra internet qua domain xịn mà không cần NAT/Port Forwarding.
3. **Cấu hình Nginx Multi-Domain:**
   * **Domain 1 (`thanh59kmt.click`):** Phục vụ mã nguồn `site1`. Định tuyến thư mục `/api/` thẳng sang container Node-RED.
   * **Domain 2 (`thanhkmt.click`):** Phục vụ mã nguồn `site2`, hoạt động độc lập hoàn toàn.

### Bài tập 2: Tích hợp API Node-RED và JavaScript Frontend
1. **Tạo API trên Node-RED:**
   * Dùng node `http in` nhận Request (`GET /api/dssv`) và node `http response` trả về Client.
   * Node Function định dạng Payload phản hồi chuẩn JSON: `{"ok": 1, "msg": "thành công", "dssv": [{"name": "Mông Chí Thành", "money": 6666}, {"name": "Cốp", "money": 123}, {"name": "David", "money": 456}]}`.
2. **Cấu hình Nginx Proxy:** `site1.conf` chứa lệnh `proxy_pass http://nodered:1880/;` nhằm giải quyết chính sách CORS.
3. **Gọi API bằng JavaScript (Client-side):** Sử dụng hàm `fetch('/api/dssv')` trong HTML xử lý bất đồng bộ, bóc tách JSON và tự động render dữ liệu sinh viên lên giao diện DOM bằng hàm `document.createElement('li')`.

---

## 3. HƯỚNG DẪN KHỞI CHẠY DỰ ÁN

**Bước 1: Khởi động hệ thống vi dịch vụ Docker**
```bash
docker compose up -d
docker compose ps
