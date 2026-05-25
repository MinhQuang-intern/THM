**Wireshark: The Basic**

`1. Use Cases của Wireshark`

Wireshark là công cụ phân tích traffic mạng (packet analyzer/sniffer).

Mục đích sử dụng

- Phát hiện và xử lý sự cố mạng

    - Network congestion (tắc nghẽn)

    - Điểm lỗi mạng

    - Kết nối chậm/mất kết nối

- Phát hiện bất thường bảo mật

    - Rogue host (thiết bị lạ)

    - Port usage bất thường

    - Traffic đáng ngờ

- Điều tra và học giao thức

    - HTTP response code

    - DNS request/response

    - Payload dữ liệu

Lưu ý 

    - Wireshark không phải IDS

    - Chỉ

        - đọc packet

        - phân tích packet

        - hỗ trợ điều tra

    - Không chặn hoặc sửa traffic

=> Khả năng phát hiện phụ thuộc vào kỹ năng analyst.

`2.Giao diện (GUI) của Wireshark`

Wireshark có nhiều khu vực chính:

*Toolbar*

Thanh công cụ chính.

Chức năng:

- Start/Stop capture

- Filter

- Sort

- Export

- Merge PCAP

- Điều hướng packet

*Display Filter Bar*

Thanh lọc packet 

Dùng để

- tìm packet 

- lọc protocol

- lọc IP/port

Ví dụ

```
http
dns
ip.addr == 192.168.1.10
tcp.port == 80
```

*Recent File*

