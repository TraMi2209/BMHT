# Phân Tích và Đề Xuất Giải Pháp Bảo Mật Cho Quá Trình Sao Lưu và Phục Hồi Dữ Liệu Oracle

Dự án này là kết quả của bài báo cáo kết thúc học phần "Bảo mật hệ thống thông tin" tại Trường Đại học Ngoại ngữ - Tin học Thành phố Hồ Chí Minh (HUFLIT). Dự án tập trung vào việc nghiên cứu, phân tích và triển khai các biện pháp bảo mật nhằm bảo vệ dữ liệu doanh nghiệp trong quá trình sao lưu (Backup) và phục hồi (Recovery) sử dụng Oracle Database.

## 👥 Nhóm Thực Hiện (Nhóm 16)
*   **Phan Hoàng Ân** (23DH110177)
*   **Nguyễn Thị Trà Mi** (23DH112041)
*   **Giảng viên hướng dẫn:** ThS Phạm Đức Thành

## 🎯 Mục Tiêu Dự Án
Quá trình sao lưu dữ liệu thường là một "điểm mù" bảo mật; nếu các tệp sao lưu không được mã hóa, chúng dễ dàng trở thành mục tiêu của tin tặc. Đồ án này nhằm:
*   Nghiên cứu kiến trúc bảo mật của Oracle và cách thức hoạt động của công cụ **RMAN (Recovery Manager)**
*   Triển khai **RMAN Security** thông qua kỹ thuật mã hóa bản sao lưu (RMAN Backup Encryption).
*   Thực hiện các kịch bản kiểm thử giả lập tấn công (Physical Theft) và khôi phục sau thảm họa (Disaster Recovery).

## 🏗️ Kiến Trúc Hệ Thống & Kịch Bản Giả Định
Dự án giả lập môi trường của doanh nghiệp "M&A Luxury" trên nền tảng Oracle Database 21c (Docker/Ubuntu Server). Dữ liệu cần bảo vệ là bảng `LUONG_CTY` chứa thông tin nhạy cảm của nhân sự (Mã NV, Họ tên, Mức lương).

**Phân quyền hệ thống (RBAC):**
*   **Nhóm Quản trị (User `SYS`):** Toàn quyền cấu hình RMAN, thực hiện sao lưu/phục hồi và nắm giữ mật khẩu giải mã.
*   **Nhóm Nghiệp vụ (User `nhansu`):** Chủ sở hữu bảng dữ liệu lương.
*   **Nhóm Rủi ro/Kiểm thử (User `scott`):** Đối tượng giả lập để Hacker (sử dụng Kali Linux) khai thác và thực hiện tấn công.

## 🛡️ Giải Pháp Bảo Mật Đã Triển Khai
Áp dụng cơ chế **Password-based Encryption** bằng thuật toán **AES (Advanced Encryption Standard)** trên RMAN.

*   **Quy trình Mã hóa:** Lệnh sao lưu được thiết lập mật khẩu (`MatKhauBaoMat123`). RMAN sử dụng thuật toán AES để xáo trộn dữ liệu thành Ciphertext, khiến tệp `.bkp` trở thành các ký tự vô nghĩa nếu mở bằng công cụ thông thường.
*   **Quy trình Phục hồi:** Yêu cầu quản trị viên cung cấp chính xác mật khẩu để giải mã. Nếu sai, quá trình phục hồi sẽ bị từ chối.

## 🧪 Kịch Bản Kiểm Thử & Kết Quả
1.  **Tấn công Đánh cắp vật lý (Physical Theft):** Sử dụng Kali Linux đánh cắp tệp `.bkp`. Trước khi mã hóa, lệnh `strings` có thể đọc được toàn bộ dữ liệu lương. Sau khi áp dụng mã hóa, kẻ tấn công không thể đọc được nội dung.
2.  **Phục hồi thảm họa (Disaster Recovery):** Giả lập việc xóa sạch bảng lương (`DROP TABLE`). Sau đó, sử dụng RMAN cùng mật khẩu giải mã để khôi phục dữ liệu thành công về trạng thái nguyên vẹn.

## 📊 Đánh Giá Hiệu Năng
Việc mã hóa AES làm tăng thời gian sao lưu (khoảng 15-25%) và tăng tải CPU do các phép toán xáo trộn dữ liệu. Tuy nhiên, sự đánh đổi này là hoàn toàn xứng đáng để đảm bảo an toàn tuyệt đối cho tài sản dữ liệu của doanh nghiệp.
