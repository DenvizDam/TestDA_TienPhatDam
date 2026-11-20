HCVN – Data Analyst Test
Sau khi giải nén, mở thư mục HCVN/ trước. Trong đó có hai file PDF:
- HCVN_Bai_lam.pdf — mở file này trước để đọc bài làm cho cả ba phần SQL, Power BI và Technical.
- 00_Huong_dan.pdf — hướng dẫn chạy từng phần, kèm ảnh dashboard và kết quả mẫu để đối chiếu.
Các đường dẫn bên dưới đều tính từ thư mục HCVN/.


1. Mở file nào cho từng phần?
File cần mở	Cách dùng
Đề bài gốc	de_bai/DA - Test - HCVN.xlsx	Mở Excel để xem ba sheet SQL, PBI và Technical.

Bài SQL	sql/HCVN.sqlite	Mở database bằng DBeaver với kết nối SQLite. Chạy lần lượt ba file SQL bắt đầu bằng 01_, 02_ và 03_. Kết quả mẫu nằm trong results/.


Power BI – Giữ chân khách hàng	powerbi/HCVN.pbip	Mở bằng Power BI Desktop trên Windows rồi chọn Home → Refresh. Khi bỏ bộ lọc, kết quả là 2/10 cuộc gọi giữ chân thành công (20%).


Technical – Xem nhanh	technical/live/Technical_dashboard_demo.gif	Mở ảnh động để xem dashboard mô phỏng thay đổi qua 10 sự kiện. Có thể xem trên Windows hoặc Mac mà không cần chạy Docker.


Technical – Chạy dashboard trực tiếp	technical/live/Start_dashboard.cmd	Chạy trên Windows để khởi động Kafka, Oracle và mở báo cáo Entertainment.pbip. Các bước thiết lập nằm ngay bên dưới.


Lưu ý về file Power BI: Khi sao chép hoặc giải nén dự án, giữ mỗi file .pbip cạnh hai thư mục .Report và .SemanticModel đi kèm. Power BI cần cả ba thành phần để mở báo cáo.





2. Chạy dashboard Technical trên Windows
Bước 1. Khởi động hệ thống
Máy cần có Docker Desktop và Power BI Desktop. Mở Docker Desktop ở chế độ Linux containers và đợi Docker khởi động xong.

Vào technical/live/, nhấp đúp Start_dashboard.cmd và giữ cửa sổ lệnh mở. Lần chạy đầu có thể mất vài phút để tải Kafka và Oracle. Khi dữ liệu sẵn sàng, chương trình sẽ mở Entertainment.pbip.
Bước 2. Bật kết nối Oracle DirectQuery trong Power BI
Nếu dùng bộ kết nối Oracle tích hợp sẵn trong Power BI Desktop, thiết lập một lần trên máy:
1. Vào File → Options and settings → Options → Preview features.
2. Tích Enable Oracle Bundled Provider for DirectQuery Mode.
3. Bấm OK, đóng hẳn Power BI Desktop rồi mở lại technical/live/Entertainment.pbip. Giữ Docker và cửa sổ lệnh ở bước 1 đang chạy.
Khi Power BI hỏi cách đăng nhập, chọn Database ở cột bên trái và dùng thông tin của môi trường demo:
Trường	Giá trị
Server	localhost:1521/FREEPDB1
User name	HCVN
Password	HcvnDemo2026


Bấm Save hoặc Connect để mở báo cáo.
Bước 3. Bật tự cập nhật và phát dữ liệu
1. Trên trang Live entertainment operations, bấm vào vùng nền trống giữa hai bảng.
2. Trong Visualizations, chọn Format page → Page refresh → On → Fixed interval và đặt 5 seconds.
3. Lưu báo cáo và giữ trang mở. Quay lại cửa sổ Start_dashboard.cmd, nhấn một phím để bắt đầu phát 10 sự kiện mẫu, mỗi sự kiện cách nhau 8 giây.
Bạn có thể theo dõi bảng Latest activity để thấy từng hành động. Khi khách hủy vé, Active bookings giảm. Khi hệ thống xác nhận hoàn tiền, Net receipts mới giảm.
3. Kiểm tra dashboard tự cập nhật
Sau 10 sự kiện, dashboard sẽ hiển thị 1 booking còn hiệu lực, 180.000 đồng thu ròng, 20 phút xem và 1 lượt check-in. Bộ phát dừng ở mốc 10 và giữ dữ liệu cho lần mở sau.

Để thấy dashboard tự đổi số, giữ Power BI mở và mở PowerShell trong thư mục technical/live/. Khi ô Events received đang là 10, chạy:
docker compose --profile demo run --rm --no-deps producer publish --interval 1 --limit 11

Lệnh này nâng tổng số sự kiện lên 11 bằng cách phát thêm một lượt xem City Lights dài 12 phút. Quan sát dashboard mà không bấm Refresh:
Chỉ số	Trước khi phát	Sau sự kiện thứ 11
Events received	10	11
Minutes watched	20	32
Net receipts	180.000 đồng	180.000 đồng


Khi xem xong, chạy lệnh sau trong cùng thư mục để dừng các dịch vụ và giữ lại dữ liệu:
docker compose --profile demo down
