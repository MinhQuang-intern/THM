**Network Discovery Detection**

`Horizontal Scanning`
Horizontal scanning là kiểu quét trong đó kẻ tấn công kiểm tra cùng một cổng dịch vụ trên nhiều địa chỉ IP đích khác nhau.

Mục đích của kiểu quét này là xác định những máy nào trong mạng đang mở một cổng cụ thể. Sau khi tìm được các máy có cổng đó đang mở, kẻ tấn công có thể tiếp tục khai thác lỗ hổng liên quan đến dịch vụ chạy trên cổng đó.

Ví dụ, mã độc WannaCry ransomware từng lây lan qua mạng bằng cách khai thác lỗ hổng SMBv1. Nó thường quét các máy có cổng 445 đang mở, vì cổng 445 được sử dụng cho dịch vụ SMB trên Windows.

Ví dụ:
```
192.168.1.100 -> 192.168.1.1:445
192.168.1.100 -> 192.168.1.2:445
192.168.1.100 -> 192.168.1.3:445
192.168.1.100 -> 192.168.1.4:445
```

`Vertical Scanning`
Vertical scanning là kiểu quét trong đó kẻ tấn công tập trung vào một địa chỉ IP đích duy nhất, nhưng kiểm tra nhiều cổng khác nhau trên máy đó.

Mục đích của vertical scanning là xác định những dịch vụ nào đang được mở trên một máy cụ thể. Từ đó, kẻ tấn công có thể biết máy đó đang chạy dịch vụ gì, ví dụ như web, SSH, SMB, RDP, mail server,... rồi tìm cách khai thác lỗ hổng tương ứng.

Ví dụ: 
```
192.168.1.100 -> 192.168.1.25:80
192.168.1.100 -> 192.168.1.25:25
192.168.1.100 -> 192.168.1.25:22
192.168.1.100 -> 192.168.1.25:445
192.168.1.100 -> 192.168.1.25:3389
```

`Ping Sweep`
Ping Sweep là một trong những kỹ thuật quét mạng cơ bản nhất. Kỹ thuật này thường được dùng để xác định những máy chủ nào đang tồn tại và đang hoạt động trong mạng.

Ping Sweep hoạt động bằng cách gửi gói tin ICMP đến các máy trong mạng. Nếu máy đích đang online, nó sẽ phản hồi lại bằng một gói ICMP khác.

Tuy nhiên, trong nhiều tổ chức hiện nay, lưu lượng ICMP thường bị chặn bởi firewall hoặc các cơ chế bảo mật. Vì vậy, Ping Sweep có thể dễ bị phát hiện hoặc không đem lại kết quả chính xác.

Nói ngắn gọn: Ping Sweep = gửi ICMP để kiểm tra host nào đang online.

`TCP SYN Scan`
Một kết nối TCP thông thường được thiết lập thông qua quá trình bắt tay 3 bước:

SYN -> SYN-ACK -> ACK

Các công cụ quét mạng có thể lợi dụng cơ chế này để xác định máy nào đang online và cổng nào đang mở.

Cách hoạt động:

Scanner gửi gói SYN đến máy đích.
Nếu máy đích trả lời SYN-ACK, nghĩa là:
- Máy đích đang online
- Cổng được quét đang mở

TCP SYN Scan thường được xem là kỹ thuật quét tương đối “ẩn” hơn so với một số kỹ thuật khác, vì nó có thể hòa lẫn với lưu lượng TCP bình thường trong mạng và khó bị phát hiện hơn.

Nói ngắn gọn:

TCP SYN Scan = gửi SYN để kiểm tra port có mở hay không.

Ví dụ:

Attacker -> Server: SYN tới port 80
Server -> Attacker: SYN-ACK
=> Port 80 đang mở

`UDP Scan`

UDP Scan là kỹ thuật quét dùng để xác định host online và các cổng UDP đang mở.

Thông thường, scanner sẽ gửi một gói UDP, thường là gói rỗng, đến cổng cần kiểm tra.

Cách hoạt động:

Nếu port đóng:
Máy đích trả về ICMP Port Unreachable.

Nếu không có phản hồi:
Scanner có thể đánh dấu port là open hoặc open|filtered.

Nếu nhận được gói UDP phản hồi:
Đây là dấu hiệu rõ ràng rằng port đang mở.

Tuy nhiên, UDP Scan thường chậm và kém tin cậy hơn TCP Scan. Nguyên nhân là vì UDP không có cơ chế bắt tay như TCP, nên scanner thường phải chờ đến khi timeout nếu không nhận được phản hồi.

Nói ngắn gọn:

UDP Scan = gửi UDP để kiểm tra port, nhưng kết quả có thể không chắc chắn.


