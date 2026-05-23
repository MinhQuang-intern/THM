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


1. Application Layer

Chức năng

- Chứa dữ liệu ứng dụng và giao thức người dùng sử dụng:

    - HTTP/HTTPS

    - DNS

    - FTP

    - SMTP

- Bao gồm:

    - Header: thông tin request/response

    - Payload: dữ liệu thật

Ví dụ: 

```http
GET /downloads/suspicious_package.zip HTTP/1.1
Host: www.tryhackme.thm
User-Agent: curl/7.85.0
```

