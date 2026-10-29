# HỆ THỐNG QUẢN TRỊ HẠ TẦNG & NHẬT KÝ KỸ THUẬT (GREENTECH)

> **Tác giả:** Lê Phúc Duy  

---

## 1. TỔNG QUAN & MÔ HÌNH TƯ DUY
* **Mô hình IPO (Input - Process - Output):**
  * *Input:* Tệp nhật ký `data/log-he-thong.log` (~500MB) phát sinh từ máy chủ.
  * *Process:* Script `scripts/auto_report.bat` lọc lỗi bằng đường ống Pipe (`|`).
  * *Output:* Báo cáo sự cố `backup/bao-cao-su-co.txt`.
* **Mô hình DIKW:** Chuyển đổi từ dữ liệu thô (`Data` -> `Information` -> `Knowledge` -> `Wisdom`) thành quyết định vận hành hạ tầng (nâng cấp thiết bị, điều chỉnh băng thông).

---

## 2. KIẾN TRÚC CÂY THƯ MỤC (4 TRỤ CỘT)
Hệ thống tuân thủ chuẩn **kebab-case**, không dấu, không khoảng trắng:

```text
greentech-system/
├── docs/                
├── scripts/             
├── data/                 
├── backup/                 
└── .env                   
3. THIẾT KẾ CƠ SỞ DỮ LIỆU EXCEL (ASSET DATABASE)Sheet danh-muc-linh-kien (Bảng cha): ma_linh_kien (Primary Key), ten_thiet_bi, don_gia, loai_storage.Sheet trang-thai-tram (Bảng con): ma_tram, ma_linh_kien (Foreign Key), dung_luong_ram, ngay_lap_dat.4. CÁC MODULE CHỨC NĂNG & MÃ LỆNHModule 1: Khởi tạo Căn cứ số (Terminal)Khởi tạo toàn bộ cấu trúc thư mục bằng 1 lệnh duy nhất và tạo file cấu hình .env ẩn:DOSmkdir greentech-system\docs greentech-system\scripts greentech-system\data greentech-system\backup
cd greentech-system
echo. > .env
Module 2: Robot lọc Log tự động (scripts/auto_report.bat)Script xử lý tệp dung lượng lớn qua kỹ thuật Streaming (không tràn RAM), ghi nối bằng >>:DOS@echo off
echo ===== BAO CAO SU CO HE THONG ===== >> backup\bao-cao-su-co.txt
echo Thoi gian quet: %date% %time% >> backup\bao-cao-su-co.txt
echo ------------------------------------------------------- >> backup\bao-cao-su-co.txt

type data\log-he-thong.log | findstr /I "CRITICAL ERROR TIMEOUT" >> backup\bao-cao-su-co.txt

echo Hoan tat loc log.
Module 4: Đối soát tài nguyên (Excel Formulas)Làm sạch khoảng trắng và tra cứu thông tin thiết bị tự động:Làm sạch mã: =TRIM(B2)Đối soát VLOOKUP chính xác: =VLOOKUP(TRIM(B2), 'danh-muc-linh-kien'!$A$2:$D$100, 2, FALSE)5. RÀNG BUỘC KỸ THUẬT & BẪY DỮ LIỆUBẫy Terminal: Tuyệt đối không dùng tên thư mục có dấu hoặc khoảng trắng; dùng đường dẫn tương đối.Bẫy Scripting: Không dùng lệnh xóa nguy hiểm (del /f /s /q) chưa qua kiểm duyệt; luôn dùng toán tử >> thay vì > để tránh ghi đè mất log cũ.Bẫy Excel: Không để hàng trống hoặc gộp ô (Merge Cells) trong vùng dữ liệu để AutoFilter và VLOOKUP hoạt động chính xác.Quy đổi tốc độ mạng (Hardware Constraint)Bẫy Hardware & Mạng (Tính toán thời gian tải Log):

Công thức quy đổi: 1 Byte = 8 bits => B (MB/s) = b (Mbps) / 8.

Bài toán: Với đường truyền mạng trạm cảm biến là 100 Mbps, tốc độ tải thực tế đạt 12.5 MB/s. Tệp log 500 MB sẽ mất thời gian truyền tải lý thuyết là:

Thời gian = 500 MB / 12.5 MB/s = 40 giây.
