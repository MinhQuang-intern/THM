**NetworkMinner**

`NetworkMiner trong Forensics`

Mục tiêu của network forensics là phát hiện hoạt động độc hại, sự cố bảo mật hoặc bất thường từ lưu lượng mạng.

NetworkMiner giúp phân tích nhanh bằng cách cung cấp:

    - Thông tin host: IP, MAC, hostname, hệ điều hành (OS)

    - Dấu hiệu tấn công hoặc bất thường: traffic tăng đột biến, port scan,...

    - Công cụ có thể được dùng trong tấn công: ví dụ Nmap

`Các loại dữ liệu hỗ trợ`

Trong network forensics thường phân tích 3 loại dữ liệu:

- Live Traffic: lưu lượng mạng đang chạy trực tiếp

- Traffic Captures (PCAP): file ghi lại gói tin mạng

- Log Files: nhật ký hệ thống/mạng

NetworkMiner có thể xử lý live traffic và file PCAP, nên thường dùng để phân tích lưu lượng mạng thực tế hoặc đã capture.

`NetworkMiner – Tóm tắt chức năng`

- Traffic sniffing: bắt và ghi lại lưu lượng mạng (sniff packet).

- Đọc file PCAP: mở và phân tích nội dung gói tin trong file .pcap.

- Phân tích giao thức: nhận diện giao thức đang dùng (HTTP, DNS, FTP,...).

- OS fingerprinting: đoán hệ điều hành của host từ traffic.

- File extraction: trích xuất file từ traffic (ảnh, HTML, email,...).

- Credential grabbing: tìm thông tin đăng nhập trong traffic (nếu có).

- Cleartext keyword parsing: tìm chuỗi text/keyword dạng plaintext trong packet.

`Chế độ hoạt động (Operating Modes)`

- Sniffer Mode: bắt traffic trực tiếp. Có nhưng không mạnh bằng Wireshark, không nên dùng làm công cụ sniff chính.

- Packet Parsing/Processing: phân tích file capture (PCAP) để xem nhanh tổng quan traffic trước khi điều tra sâu.

`Ưu và nhược điểm của NetworkMiner`

Ưu điểm 

- Xác định OS của host

- Trích xuất file dễ dàng

- Tìm credential và keyword plaintext nhanh

- Cho cái nhìn tổng quan mạng nhanh

Nhược điểm

- Sniffing yếu, không phù hợp bắt traffic trực tiếp lâu dài

- Không tốt với file PCAP lớn

- Filter hạn chế

- Không mạnh cho phân tích thủ công chi tiết




