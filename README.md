# Xây Dựng Hệ Thống Honeypot/Honeynet Để Bẫy Hacker

Dự án triển khai mô hình phòng thủ chiều sâu trên môi trường máy ảo VMware nhằm chủ động bẫy, dẫn dụ và ghi lại hành vi tấn công mạng qua giao thức SSH.

## Mô Hình Kiến Trúc Hệ Thống
Hệ thống phân tách thành 3 phân vùng logic chính:
* **Internet / Attacker:** Đại diện bởi máy Kali Linux (IP: 192.168.1.128).
* **Vùng trung gian (DMZ):** Dải mạng 192.168.10.0/24, bao gồm:
  * **Honeypot Cowrie:** Máy chủ Ubuntu Server (IP: 192.168.10.60) giả lập dịch vụ SSH.
  * **Web Server Apache:** Máy chủ Ubuntu Desktop (IP: 192.168.10.30) chạy website thật.
* **Mạng nội bộ (Internal LAN) & Firewall:** Được bảo vệ phía sau tường lửa pfSense.

---

## Kịch Bản Hoạt Động & Cấu Hình pfSense

### 1. Tạo IP Mồi Nhử (Virtual IP)
Trên giao diện WAN của pfSense, thiết lập một địa chỉ IP ảo đóng vai trò làm mục tiêu giả lập:
* **Type:** IP Alias
* **Address:** 192.168.1.30/32
* **Description:** Honeypot Decoy IP

### 2. Cấu hình NAT Port Forward (Bẻ lái gói tin)
Thiết lập quy tắc chuyển hướng tự động lưu lượng tấn công SSH từ bên ngoài chui thẳng vào bẫy:
* **Interface:** WAN
* **Protocol:** TCP
* **Destination IP:** 192.168.1.30 (Port 22)
* **Redirect Target IP:** 192.168.10.60 (Port 2222 - Cổng lắng nghe của Cowrie).

---

##  Kết Quả Thực Nghiệm
* **Quét và Tấn công:** Hacker dùng Nmap quét dải mạng và phát hiện Port 22 mở trên IP ảo. Tiến hành Brute-force kết nối SSH.
* **Cách ly và Ghi Log:** Khi hacker truy cập thành công, mọi thông tin tài khoản (username/password), lệnh thực thi và mã độc tải xuống đều bị cô lập hoàn toàn.
* **Xác thực Log:** Toàn bộ tiến trình phá hoại được ghi lại chi tiết theo thời gian thực tại file nhật ký `/opt/cowrie/var/log/cowrie/cowrie.log`. Các máy chủ thật như Web Server Apache không bị ảnh hưởng.

---

  4. Tống Lạc Lan Viên (2033230322) – Cấu hình Honeypot Cowrie & Quay video.
