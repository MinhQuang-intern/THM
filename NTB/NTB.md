**Network Traffic Basics**

1. Network traffic analysis là gì?

- Là quá trình theo dõi và kiểm tra dữ liệu đi qua mạng

- Giúp biết:

    - Máy nào đang gửi dữ liệu

    - Gửi đến đâu 

    - Dùng giao thức gì(DNS, HTTP, HTTPS)

    - Nội dung có đáng ngờ hay không

Ví dụ: DNS Tunneling & Beaconing

Tình huống

SOC nhận cảnh báo: 

- Máy WIN-016 (192.168.1.16) gửi rất nhiều DNS query bất thường

- Các query đều đến cùng domain nhưng subdomain thay đổi liên tục 

Ví dụ:

- aj39skdm.malicious-tld.com

- msd91azx.malicious-tld.com

- cmd01.malicious-tld.com

Tại sao cần phân tích traffic 

1. Phát hiện tấn công

Ví dụ:

- DNS Tunneling

- Malware beaconing 

- Data exfiltration 

- C2 communication 

2. Xem nội dung thật của gói tin 

Ví dụ:

- TXT record chứa lệnh từ attacker 

- Malware tải file độc hại qua HTTP

- Dữ liệu bị đánh cắp qua DNS 

3. Điều tra sự cố (Incident Response)

- Dựng timeline tấn công

- Biết máy nào bị nhiễm 

- Biết attacker đã làm gì 

4. Xác minh cảnh báo SOC 

-----------------------------------------------------------------------------------

```TCP/IP Model là gì``` 

- Là mô hình mô tả cách dữ liệu truyền trên mạng 

- Dữ liệu đi qua nhiều tầng, mỗi tầng thêm thông tin riêng gọi là header 

Các tầng gồm: 

- Application

- Transport 

- Internet 

- Link 

Khi phân tích traffic, SOC sẽ kiểm tra thông tin ở từng tầng để phát hiện bất thường


```1. Application Layer```

Chức năng

- Chứa dữ liệu ứng dụng và giao thức người dùng sử dụng:

    - HTTP/HTTPS

    - DNS

    - FTP

    - SMTP

- Bao gồm:

    - Header: thông tin request/response

    - Payload: dữ liệu thật

Ví dụ HTTP Request 

```http
GET /downloads/suspicious_package.zip HTTP/1.1
Host: www.tryhackme.thm
User-Agent: curl/7.85.0
```

Ý nghĩa: 

- GET -> yêu cầu tải file 

- /downloads/suspicious_package.zip -> file đang được tải

- Host: web đích

- User-Agent: công cụ/phần mềm gửi request

Ví dụ HTTP Response 

```http
HTTP/1.1 200 OK
Content-Type: application/zip
```

Ý nghĩa: 

- 200 OK → server chấp nhận request

- application/zip → file ZIP được trả về

Nhưng log thường không thấy nội dung file ZIP, chỉ thấy header.

=> Muốn xem file chứa gì phải phân tích traffic/PCAP.

```2. Transport Layer (TCP/UDP)```

Chức năng: 

    - Chia dữ liệu thành nhiều phần nhỏ để gửi 

    - Dùng chủ yếu: TCP, UDP 

Header thường có: 

    - Source Port

    - Destination Port

    - Flags (SYN, ACK,…)

    - Sequence Number

TCP 3-way Handshake 

Quy trình tạo kết nối TCP:

```
Client -> SYN 
Server -> SYN, ACK
Client -> ACK
```

Ý nghĩa: 

- Thiết lập kết nối trước khi truyền dữ liệu 

Sequence Number là gì?

- Số thứ tự gói tin TCP.

- Giúp sắp xếp dữ liệu đúng thứ tự.

Nếu sequence number nhảy bất thường: Seq=1 → Seq=34567232

=> Có thể là: 

- Session hijacking

- Packet injection

- Tấn công chèn gói tin

```3. Internet Layer (IP Layer)```

Chức năng

- Định tuyến gói tin giữa các mạng 

- Chứa thông tin IP

Header gồm:

- Source IP

- Destination IP 

- TTL 

- Fragment Offset 

```Fragmentation là gì?```

Nếu gói quá lớn: > MTU 

-> IP chia thành nhiều mảnh nhỏ (fragment)

```Tấn công Fragmentation```

Attacker cố tình fragment chồng lấn (overlap)

Ví dụ: Offset = 1480

       Offset = 1480 => overlap
Mục đích: 

- Né IDS/IPS

- Làm sai quá trình ghép gói 

4. Link Layer (MAC/ARP)

Chức năng

- Giao tiếp trong mạng LAN 

- Dùng địa chỉ MAC

Thông tin gồm: 

- Source MAC

- Destination MAC

- ARP 

```ARP là gì?```

Máy hỏi: Who has 192.168.1.10?

Máy đích trả lời: 192.168.1.10 is at 00:11:22:33:44:55

=> Dùng để map: IP <=> MAC 

ARP Poisoning / Spoofing

Attacker giả mạo: 192.168.1.10 is at aa:bb:cc:dd:ee:ff

Làm nạn nhân tin rằng MAC attacker là máy thật 

Kết quả:

- Traffic đi qua attacker

- Có thể nghe lén hoặc chỉnh sửa dữ liệu

```Sources và Flows trong Network Traffic```

1. Sources (Nguồn traffic)

Nguồn traffic trong mạng được chia thành 2 loại:

1.1 Intermediary Sources (Thiết bị trung gian)

Khái niệm

- Là thiết bị mà network traffic đi qua 

- Sinh ít traffic hơn endpoint

- Chủ yếu chuyển tiếp, lọc hoặc giám sát traffic 

Thiết bị thuộc nhóm này 

- Firwwall 

- Router

- Switch 

- IDS/IPS

- Web Proxy 

- Access Point

- Wireless LAN Controller 

Traffic thường tạo ra

- Routing protocol: EIGRP, OSPF, BGP

- Management protocol: SNMP, PING

- Logging protocol: SYSLOG

- Infrastructure protoccol: ARP, STP, DHCP 

=> Giúp quan sát, kiểm soát và ghi log traffic mạng 

1.2 Endpoint Sources (Thiết bị đầu cuối)

Khái niệm

- Là nơi traffic bắt đầu hoặc hết thúc

- Chiếm phần lớn băng thông mạng 

Thiết bị thuộc nhóm này

- Host/ User machine

- Server

- Virtual Machine (VM)

- IoT device

2. Flows (Luồng traffic)

Flow = đường đi của traffic trong mạng, phụ thuộc vào dịch vụ đang dùng như:

- Active Directory

- SMB

- HTTPS

- DNS

Traffic được chia thành 2 loại

2.1 North-South Traffic (NS)

Khái niệm

- Traffic đi giữa: LAN ↔ WAN/Internet

- Đi ra ngoài hoặc từ ngoài đi vào mạng nội bộ 

- Thường đi qua firewall 

Protocol thường gặp 

- HTTPS

- DNS 

- SSH

Hướng traffic
 
- Ingress (Inbound): traffic đi vào 

- Egress (Outpound): traffic đi ra 

Ý nghĩa

- Cần monitor chặt chẽ vì liên quan đến Internet

- Visibility phụ thuộc vào firewall rule và logging 

2.2 East-West Trafic (EW)

Khái niệm

- Traffic chỉ nằm bên trong LAN bao gồm môi trường cloud nội bộ 

```
Host ↔ Server
Server ↔ Server
Host ↔ Domain Controller
```

Đặc điểm

- Thường ít được monitor hơn North-South

- Quan trọng vì attacker thường dùng để lateral movement

Ý nghĩa

- Dùng phát hiện di chuyển ngang (lateral move) trong mạng 

3. HTTPS Flow (TLS Inspection qua Proxy)

Cách hoạt động

Khi host truy cập website: Host → Web Proxy → Web Server

Quy trình

```
Host gửi request tới NGFW/Web Proxy.
Proxy đóng vai trò web server với client.
Proxy tạo phiên TCP/TLS mới tới web server thật.
Proxy nhận response, kiểm tra nội dung.
Nếu an toàn → forward về host.
```

4. External DNS Flow 

Quy trình DNS trong doanh nghiệp

1. Host gửi DNS query tới Internal DNS Server qua port 53.

2. DNS server kiểm tra cache:

    - Có cache -> trả lời ngay

    - Không có -> gửi query ra Internet

3. 

