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

Danh sách file PCAP gần đây 

- double click để mở lại

*Capture Filter & Interfaces* 

Chọn interface để sniff traffic 

Ví dụ interface:

```
eth0
ens33
lo (loopback)
docker0
```

Có thể đặt capture filter trước khi capture 

*Status Bar*

Hiển thị trạng thái:

- interface đang dùng 

- số packet 

- profile 

- trạng thái capture 

`3. Loading PCAP Files`

Mở file .pcap/.pcapng để phân tích.

Cách mở:

- File → Open

- Drag & Drop

- Double click file recent

`4. Cấu trúc màn hình phân tích packet`

Sau khi mở PCAP sẽ có 3 pane chính:

*Packet List Pane*

Danh sách packet.

Hiển thị:

- Source IP

- Destination IP

- Protocol

- Time

- Length

- Info

Ví dụ: 192.168.1.10 → 8.8.8.8 DNS

Chọn packet tại đây để xem chi tiết.

*Packet Details Pane*

Chi tiết packet theo protocol.

Ví dụ:

```
Ethernet
IP
TCP
HTTP
DNS
```

Cho biết:

- header

- field

- flag

- port

- sequence number

*Packet Bytes Pane*

Hiển thị dữ liệu packet dạng:

- Hex

- ASCII

Giúp:

- xem payload

- kiểm tra dữ liệu raw

`5. Colouring Packets (Tô màu packet)`

Wireshark tô màu packet để dễ nhận biết protocol/bất thường.

Giúp:

- nhìn nhanh protocol

- phát hiện anomaly

- phân biệt loại traffic

Ví dụ:

- TCP

- UDP

- HTTP

- ICMP

- ARP

`6. View File Details`

Xem thông tin file PCAP.

Dùng khi:

- có nhiều PCAP

- cần phân loại

- cần xác minh metadata

Đường dẫn: Statistics → Capture File Properties



