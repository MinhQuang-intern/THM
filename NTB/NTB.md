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

- Host gửi DNS query tới Internal DNS Server qua port 53.

- DNS server kiểm tra cache:

    - Có cache -> trả lời ngay

    - Không có -> gửi query ra Internet

- Query đi qua:

```
Internal DNS
→ Router
→ Firewall
→ External DNS Server
```
- DNS response quay lại theo đường ngược lại 

- Internal DNS trả kết quả cho host

Ý nghĩa:

- Theo dõi DNS ingress/egress traffic.

- Phát hiện DNS tunneling hoặc domain độc hại.

5. SMB with Kerberos Flow

Khái niệm

- SMB dùng để chia sẻ file trong Windows 

Ví dụ: \\FILESERVER\MARKETING

Quy trình hoạt động

Bước 1: Authentication với Kerberos 

- User login vào host.

- Host xác thực với KDC (Key Distribution Center) trên Domain Controller.

- Nhận Ticket Granting Ticket (TGT).

Sau đó: Host dùng TGT để yêu cầu service authentication ticket.

Bước 2: Thiết lập SMB Session

- Host dùng service ticket để kết nối SMB server.

- SMB session được thiết lập.

- User truy cập file share.

Flow đơn giản

```
Host → Domain Controller (KDC)
→ Request SMB service ticket

Host → SMB Server
→ Establish SMB session
```

Ý nghĩa: Dùng xác thực trước khi truy cập tài nguyên chia sẻ trong domain Windows.

```Method thu thập thông tin khi phân tích network traffic```

Network traffic analysis kết hợp nhiều ngôn ngữ để:

- Phân tích traffic 

- Tìm pattern bất thường 

- Hỗ trợ điều tra và phản ứng sự cố 

Nguồn thông tin chính

- Logs

- Full Packet Capture (PCAP)

- Network Statistics

1. Logs 

Khái niệm

- Là nguồn thông tin đầu tiên để biết điều gì đang xảy ra trong mạng.

- Hầu hết hệ thống và protocol đều có cơ chế logging.

Đặc điểm

- Không có chuẩn logging chung cho mọi hệ thống.

- Mỗi vendor tự quyết định:

    - Format log

    - Dữ liệu được log

Ví dụ:

- Microsoft → Windows Event Logs

- Linux → Syslog

- Apache → CLF (Common Log Format)

Nội dung log thường có

- Source IP

- Destination IP

- Username

- Timestamp

- Port

Ví dụ log xác thực SSH (Linux Syslog)

- Accepted password for gensane from 192.168.1.50 port 52234 ssh2

Ý nghĩa:

- User gensane đăng nhập thành công

- Từ IP 192.168.1.50

- Qua SSH

Ví dụ Apache Access Log

- 192.168.1.50 - - "GET /index.html HTTP/1.1" 200

Ý nghĩa:

- Client gửi HTTP GET request

- Truy cập file /index.html

- Status code 200 = thành công

Chuẩn gửi log phổ biến 

Cần:

- Syslog

- SNMP 

Khi log không đủ thông tin 

Cần:

- Correlate logs

- Kiểm tra Full Packet Capture

- Kiểm tra network statistics

2. Full Packet Capture 

Khái niệm

- Thu thập toàn bộ packet qua mạng 

- Cho phép kiểm tra nội dung packet chi tiết

Cách thu packet 

Có 2 cách:

2.1 Network TAP 

Khái niệm

- Thiết bị vật lý đặt inline trong mạng 

- Copy toàn bộ traffic mà không ảnh hưởng hiệu năng 

Cách hoạt động 

- Sao chép traffic đi qua 

- Gửi bản copy tới 

    - Packet capture box

    - IDS/IPS

    - Monitoring system

Đặc điểm 

- Hoạt dộng ở link layer 

- Không cần:

    - MAC address 

    - IP address 

Ví:

- Copy tín hiệu điện/quang trực tiếp 

Ưu điểm

- Không gây delay mạng 

- Ảnh hưởng hiệu năng gần như bằng 0

2.2 Port Mirroring (SPAN)

Khái niệm

- Copy packet bằng phần mềm switch/router

Cách hoạt động 

- Copy traffic từ một port sang port khác để monitoring.

Ví dụ Cisco SPAN:

```
monitor session 1 source interface fastEthernet0/1
monitor session 1 destination interface fastEthernet0/2
```

Ý nghĩa

- Traffic ở fastEthernet0/1

- Được copy sang fastEthernet0/2

Monitoring device sẽ nhận bản copy này.

Đặc điểm

- Có thể dùng trên

    - Physical switch

    - Virtual switch (VMware vSwitch)

    - Cloud environment

Ví dụ cloud:

- AWS VPC Traffic Mirroring

`Best Practices khi Full Packet Capture` 

Placement 

- Đặt TAP hoặc mirror đúng vị trí cần giám sát 

Duration 

- Full packet capture cần nhiều storage 

Mirror vs TAP 

TAP 

- Gần như không ảnh hưởng

Mirroring

- Có ảnh hưởng performance khi traffic lớn

Công cụ phân tích packet 

- Wireshark

- TCPDump

- Snort

- Suricata

- Zeek 

3. Network Statistics 

Khái niệm

- Thu thập metadata về network flow

- Không lưu packet đầy đủ   

Ví dụ

- Số lượng DNS requests

- Source/Destination IP

- Port

- Protocol 

- Packet count 

Mục tiêu

- Tìm anomaly trong mạng

3.1 NetFlow

Khái niệm

- Protocol do Cisco phát triển 

- Thu thập metadata về traffic flow 

Không chứa 

- Nội dung packet 

- Full packet capture 

Chỉ chứa metadata như: 

- Source IP

- Destination IP 

- Port 

- Protocol 

- Bytes 

- Packet count 

- Timestamp 

Dùng để phát hiện 

- C2 Traffic 

- Data exfiltraion 

- Lateral movement

3.2 IPFIX 

Khái niệm

- Successor của NetFlow

- Chuẩn trung lập (vendor-neutral)

Lý do xuất hiện

- NetFlow ban đầu chỉ dành cho Cisco 

- Từ NetFlow v9 mới hỗ trợ template cho vendor khác 

IETF tạo ra: IPFIX để chuẩn hóa và mở rộng NetFlow

Đặc điểm:

- Tương tự NetFlow

- Linh hoạt hơn trong chọn field cần capture

`Triển khai NetFlow/IPFIX`

Không cần hạ tầng mới

Thông thường 

1. Enable protocol trên thiết bị 

2. Configure nơi gửi metadata 

Nhiều thiết bị đã hỗ trợ sẵn:

- NGFW

- IDS

- IPS

Có thể collect và phân tích flow data trực tiếp 

