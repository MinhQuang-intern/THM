**Network Security Essentials**

`Scenario 1: Probing for Ports (Port Scanning)`

- An attacker is testing your network to see which ports are open or closed, while the firewall is doing its job and blocking them.

*Firewall Log*

```
2025-09-22 08:30:04 ALLOW TCP 198.51.100.45:49876 -> 10.0.0.51:80
2025-09-22 08:30:05 BLOCK TCP 203.0.113.10:50001 -> 10.0.0.20:21
2025-09-22 08:30:06 BLOCK TCP 203.0.113.10:50002 -> 10.0.0.20:22
2025-09-22 08:30:07 ALLOW TCP 192.0.2.115:51235 -> 10.0.0.50:443
2025-09-22 08:30:08 BLOCK TCP 203.0.113.10:50003 -> 10.0.0.20:23
2025-09-22 08:30:09 BLOCK TCP 203.0.113.10:50004 -> 10.0.0.20:25
2025-09-22 08:30:10 ALLOW TCP 198.51.100.92:51111 -> 10.0.0.50:443
2025-09-22 08:30:11 BLOCK TCP 203.0.113.10:50005 -> 10.0.0.20:53
```

- The same external IP (203.0.113.10) is trying to connect to multiple ports on the same internal machine quickly.

`Scenario 2: Attacking the Web Server (SQL Injection)`

The company website is being attacked. An intrusion detection system (IDS) provides more details than a firewall, identifying the type of attack.

*WAF (Web Application Firewall) Logs*

```
timestamp=2025-09-22T09:14:44Z src_ip=192.0.2.130 action=ALLOW request="GET /index.html"
timestamp=2025-09-22T09:14:45Z src_ip=198.51.100.92 action=ALLOW request="GET /products.php?id=9"
timestamp=2025-09-22T09:14:46Z src_ip=[REDACTED] action=BLOCK request="GET /search.php?q=<script>alert('XSS')</script>" rule_id=941100 attack_type="XSS"
timestamp=2025-09-22T09:14:47Z src_ip=192.0.2.140 action=ALLOW request="GET /css/style.css"
timestamp=2025-09-22T09:15:42Z src_ip=[REDACTED] action=BLOCK request="GET /../../../../etc/passwd" rule_id=930120 attack_type="Directory Traversal"
...
.....
```

- This log shows a mix of ALLOW and BLOCK actions. An analyst can filter for action=BLOCK to immediately find threats. The WAF does the hard work by blocking the request and telling us why.

- The attack_type="SQL Injection" alert shows the attacker trying to dump database information.

- The attack_type="XSS" alert is an attempt to inject malicious scripts.

- The attack_type="Directory Traversal" alert shows an attempt to read sensitive server files.

`Scenario 3: Guessing the Password (VPN Brute-Force)`

An attacker is trying to guess a user's password to gain remote access to the network. This creates a lot of noise in the authentication logs.

*VPN Gateway Log*

```
2025-09-22 10:12:11 FAILED_AUTH TCP [REDACTED]:31245 -> 10.0.0.1:443 (user 'admin')
2025-09-22 10:12:15 FAILED_AUTH TCP [REDACTED]:31248 -> 10.0.0.1:443 (user 'admin')
2025-09-22 10:12:21 SUCCESS_AUTH TCP 198.51.100.88:41233 -> 10.0.0.1:443 (user 'b.jones')
2025-09-22 10:12:08 FAILED_AUTH TCP [REDACTED]:31249 -> 10.0.0.1:443 (user 'guest')
2025-09-22 10:12:09 FAILED_AUTH TCP [REDACTED]:31250 -> 10.0.0.1:443 (user 'user')
```

- The log is flooded with FAILED_AUTH and SUCCESS_AUTH events.
 
- A few successful logins from various IPs are normal

- To find the attack, an analyst would filter or group the logs by source IP.

------------------------------------------------------------------------------------------------------------------------------

**Incident Scenario**

`Method 1: Manual Log Analysis`

- Commandline: head firewall.log

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ head firewall.log 
2025-08-25 00:47:46 ALLOW TCP [REDACTED]:60317 -> 10.0.0.50:443
2025-08-25 01:29:33 ALLOW TCP 203.0.113.100:62718 -> [REDACTED]:443
2025-08-25 01:42:12 ALLOW TCP 203.0.113.100:55875 -> [REDACTED]:80
2025-08-25 03:30:47 ALLOW TCP [REDACTED]:63035 -> [REDACTED]:80
2025-08-25 04:06:58 ALLOW TCP 192.0.2.115:65458 -> [REDACTED]:25
2025-08-25 05:51:36 ALLOW TCP 203.0.113.100:56035 -> [REDACTED]:53
2025-08-25 06:09:50 ALLOW TCP 198.51.100.92:63418 -> [REDACTED]:8080
2025-08-25 07:39:29 ALLOW TCP [REDACTED]:55955 -> [REDACTED]:8080
2025-08-25 08:24:34 ALLOW TCP 198.51.100.92:63475 -> [REDACTED]:8080
2025-08-25 08:57:21 ALLOW TCP 198.51.100.92:58636 -> 10.0.0.50:53
```

- Commandline: head ids_alerts.log

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ head ids_alerts.log 
2025-08-25 00:12:53 [**] [1:2003272:1] ET POLICY Suspicious HTTP [**] [Classification: Suspicious Activity] [Priority: 3] {TCP} 198.51.100.92:20127 -> [REDACTED]:22
2025-08-25 01:50:30 [**] [1:2003377:1] ET POLICY Suspicious HTTP [**] [Classification: Suspicious Activity] [Priority: 1] {TCP} 203.0.113.100:56603 -> [REDACTED]:25
2025-08-25 02:16:39 [**] [1:2003437:1] ET INFO Possible Benign Scan [**] [Classification: Suspicious Activity] [Priority: 3] {TCP} [REDACTED]:62546 -> [REDACTED]:21
2025-08-25 02:23:07 [**] [1:2003344:1] ET WEB_SERVER Possible SQL Injection [**] [Classification: Suspicious Activity] [Priority: 2] {TCP} 198.51.100.45:12396 -> [REDACTED]:22
2025-08-25 02:25:48 [**] [1:2003445:1] ET POLICY Suspicious HTTP [**] [Classification: Suspicious Activity] [Priority: 3] {TCP} 192.0.2.115:3952 -> [REDACTED]:22
2025-08-25 03:35:00 [**] [1:2003160:1] ET INFO Possible Benign Scan [**] [Classification: Suspicious Activity] [Priority: 1] {TCP} [REDACTED]:38760 -> [REDACTED]:443
2025-08-25 05:02:36 [**] [1:2003187:1] ET WEB_SERVER Possible SQL Injection [**] [Classification: Suspicious Activity] [Priority: 1] {TCP} 198.51.100.92:46776 -> [REDACTED]:3389
2025-08-25 06:04:26 [**] [1:2003179:1] ET INFO Possible Benign Scan [**] [Classification: Suspicious Activity] [Priority: 2] {TCP} 198.51.100.92:20632 -> 10.0.0.50:8080
2025-08-25 14:12:11 [**] [1:2003500:1] ET INFO Possible Benign Scan [**] [Classification: Suspicious Activity] [Priority: 2] {TCP} 192.0.2.115:30225 -> [REDACTED]:445
2025-08-25 15:30:03 [**] [1:2003354:1] ET POLICY Suspicious HTTP [**] [Classification: Suspicious Activity] [Priority: 3] {TCP} 203.0.113.100:27572 -> [REDACTED]:4444
```

- Commandline: head vpn_auth.log

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$  head vpn_auth.log 
2025-08-25 08:25:10 [REDACTED] alice SUCCESS assigned_ip=10.8.0.143
2025-08-25 08:27:38 203.0.113.100 svc_[REDACTED] SUCCESS assigned_ip=10.8.0.131
2025-08-25 14:57:10 203.0.113.10 svc_[REDACTED] SUCCESS assigned_ip=10.8.0.116
2025-08-25 23:04:53 203.0.113.10 jsmith SUCCESS assigned_ip=10.8.0.31
2025-08-26 03:36:17 198.51.100.92 svc_[REDACTED] SUCCESS assigned_ip=10.8.0.62
2025-08-26 08:55:14 [REDACTED] bob SUCCESS assigned_ip=10.8.0.126
2025-08-26 10:02:45 198.51.100.92 svc_[REDACTED] SUCCESS assigned_ip=10.8.0.81
2025-08-27 03:11:33 198.51.100.45 bob SUCCESS assigned_ip=10.8.0.163
2025-08-28 02:52:16 192.0.2.115 alice SUCCESS assigned_ip=10.8.0.132
2025-08-28 03:20:33 [REDACTED] svc_[REDACTED] SUCCESS assigned_ip=10.8.0.193
```

`Reconnaissance attempt`

- Commandline: cat firewall.log | grep "BLOCK" | head

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat firewall.log | grep "BLOCK" | head
2025-08-26 12:12:47 BLOCK TCP 203.0.113.10:64292 -> 10.0.0.50:21
2025-08-27 03:18:28 BLOCK TCP [REDACTED]:61701 -> [REDACTED]:23
2025-08-27 11:56:20 BLOCK TCP 203.0.113.10:64952 -> 10.0.0.50:22
2025-08-27 22:52:00 BLOCK TCP 203.0.113.10:63686 -> [REDACTED]:445
2025-08-28 10:00:00 BLOCK TCP [REDACTED]:50000 -> [REDACTED]:4444
2025-08-28 10:02:30 BLOCK TCP [REDACTED]:50005 -> [REDACTED]:22
```

Examining the blocked requests indicates that an external IP has been found probing against internal IPs using various ports (22,23,21,445,3389).

- Commandline: cat firewall.log | grep "BLOCK" | cut -d' ' -f5 | cut -d: -f1 | sort -nr | uniq -c

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat firewall.log | grep "BLOCK" | cut -d' ' -f5 | cut -d: -f1 | sort -nr | uniq -c
279 [REDACTED]
46 203.0.113.10
26 [REDACTED]
```

`Method 1: Manual Log Analysis`

Commandline: head firewall.log

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ head firewall.log 
2025-08-25 00:47:46 ALLOW TCP [REDACTED]:60317 -> 10.0.0.50:443
2025-08-25 01:29:33 ALLOW TCP 203.0.113.100:62718 -> [REDACTED]:443
2025-08-25 01:42:12 ALLOW TCP 203.0.113.100:55875 -> [REDACTED]:80
2025-08-25 03:30:47 ALLOW TCP [REDACTED]:63035 -> [REDACTED]:80
2025-08-25 04:06:58 ALLOW TCP 192.0.2.115:65458 -> [REDACTED]:25
2025-08-25 05:51:36 ALLOW TCP 203.0.113.100:56035 -> [REDACTED]:53
2025-08-25 06:09:50 ALLOW TCP 198.51.100.92:63418 -> [REDACTED]:8080
2025-08-25 07:39:29 ALLOW TCP [REDACTED]:55955 -> [REDACTED]:8080
2025-08-25 08:24:34 ALLOW TCP 198.51.100.92:63475 -> [REDACTED]:8080
2025-08-25 08:57:21 ALLOW TCP 198.51.100.92:58636 -> 10.0.0.50:53
```

Commandline: head ids_alert.log

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ head ids_alerts.log 
2025-08-25 00:12:53 [**] [1:2003272:1] ET POLICY Suspicious HTTP [**] [Classification: Suspicious Activity] [Priority: 3] {TCP} 198.51.100.92:20127 -> [REDACTED]:22
2025-08-25 01:50:30 [**] [1:2003377:1] ET POLICY Suspicious HTTP [**] [Classification: Suspicious Activity] [Priority: 1] {TCP} 203.0.113.100:56603 -> [REDACTED]:25
2025-08-25 02:16:39 [**] [1:2003437:1] ET INFO Possible Benign Scan [**] [Classification: Suspicious Activity] [Priority: 3] {TCP} [REDACTED]:62546 -> [REDACTED]:21
2025-08-25 02:23:07 [**] [1:2003344:1] ET WEB_SERVER Possible SQL Injection [**] [Classification: Suspicious Activity] [Priority: 2] {TCP} 198.51.100.45:12396 -> [REDACTED]:22
2025-08-25 02:25:48 [**] [1:2003445:1] ET POLICY Suspicious HTTP [**] [Classification: Suspicious Activity] [Priority: 3] {TCP} 192.0.2.115:3952 -> [REDACTED]:22
2025-08-25 03:35:00 [**] [1:2003160:1] ET INFO Possible Benign Scan [**] [Classification: Suspicious Activity] [Priority: 1] {TCP} [REDACTED]:38760 -> [REDACTED]:443
2025-08-25 05:02:36 [**] [1:2003187:1] ET WEB_SERVER Possible SQL Injection [**] [Classification: Suspicious Activity] [Priority: 1] {TCP} 198.51.100.92:46776 -> [REDACTED]:3389
2025-08-25 06:04:26 [**] [1:2003179:1] ET INFO Possible Benign Scan [**] [Classification: Suspicious Activity] [Priority: 2] {TCP} 198.51.100.92:20632 -> 10.0.0.50:8080
2025-08-25 14:12:11 [**] [1:2003500:1] ET INFO Possible Benign Scan [**] [Classification: Suspicious Activity] [Priority: 2] {TCP} 192.0.2.115:30225 -> [REDACTED]:445
2025-08-25 15:30:03 [**] [1:2003354:1] ET POLICY Suspicious HTTP [**] [Classification: Suspicious Activity] [Priority: 3] {TCP} 203.0.113.100:27572 -> [REDACTED]:4444
```

Commandline: head vpn_auth.log

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$  head vpn_auth.log 
2025-08-25 08:25:10 [REDACTED] alice SUCCESS assigned_ip=10.8.0.143
2025-08-25 08:27:38 203.0.113.100 svc_[REDACTED] SUCCESS assigned_ip=10.8.0.131
2025-08-25 14:57:10 203.0.113.10 svc_[REDACTED] SUCCESS assigned_ip=10.8.0.116
2025-08-25 23:04:53 203.0.113.10 jsmith SUCCESS assigned_ip=10.8.0.31
2025-08-26 03:36:17 198.51.100.92 svc_[REDACTED] SUCCESS assigned_ip=10.8.0.62
2025-08-26 08:55:14 [REDACTED] bob SUCCESS assigned_ip=10.8.0.126
2025-08-26 10:02:45 198.51.100.92 svc_[REDACTED] SUCCESS assigned_ip=10.8.0.81
2025-08-27 03:11:33 198.51.100.45 bob SUCCESS assigned_ip=10.8.0.163
2025-08-28 02:52:16 192.0.2.115 alice SUCCESS assigned_ip=10.8.0.132
2025-08-28 03:20:33 [REDACTED] svc_[REDACTED] SUCCESS assigned_ip=10.8.0.193
```

Commandline: cat firewall.log | grep "BLOCK" | head

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat firewall.log | grep "BLOCK" | head
2025-08-26 12:12:47 BLOCK TCP 203.0.113.10:64292 -> 10.0.0.50:21
2025-08-27 03:18:28 BLOCK TCP [REDACTED]:61701 -> [REDACTED]:23
2025-08-27 11:56:20 BLOCK TCP 203.0.113.10:64952 -> 10.0.0.50:22
2025-08-27 22:52:00 BLOCK TCP 203.0.113.10:63686 -> [REDACTED]:445
2025-08-28 10:00:00 BLOCK TCP [REDACTED]:50000 -> [REDACTED]:4444
2025-08-28 10:02:30 BLOCK TCP [REDACTED]:50005 -> [REDACTED]:22
```

Dòng 1: IP 2003.0.113.10 cố kết nối tới FTP Server (Port 21) nhưng Firewall đã chặn 
Dòng 2: Telnet truyền dữ liệu dạng plaintext, ngày nay hầu như không dùng nên firewall chặn là điều bình thường 
Dòng 3: Có người từ 203.0.113.10 đang cố SSH vào 10.0.0.50 => Firewall chặn lại 
Dòng 4: 445 = port SMB (dịch vụ chia sẻ file trên Windows) 
Dòng 5: 4444 là port thường gặp trong metasploit ...

Commandline: cat ids_alerts.log | grep [REDACTED] | head

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat ids_alerts.log | grep [REDACTED] | head    
2025-09-05 06:00:00 [**] [1:2000200:1] ET SCAN Possible SSH Scan [**] [Classification: Attempted Unauthorized Access] [Priority: 1] {TCP} [REDACTED]:2000 -> [REDACTED]:22
2025-09-05 06:10:00 [**] [1:2000201:1] ET EXPLOIT Possible MS-SMB Lateral Movement [**] [Classification: Attempted Unauthorized Access] [Priority: 1] {TCP} [REDACTED]:2001 -> [REDACTED]:445
2025-09-05 06:20:00 [**] [1:2000202:1] ET SCAN Possible SSH Scan [**] [Classification: Attempted Unauthorized Access] [Priority: 1] {TCP} [REDACTED]:2002 -> [REDACTED]:22
2025-09-05 06:30:00 [**] [1:2000203:1] ET EXPLOIT Possible RDP Brute Force [**] [Classification: Attempted Unauthorized Access] [Priority: 1] {TCP} [REDACTED]:2003 -> [REDACTED]:3389
2025-09-05 07:10:00 [**] [1:2000207:1] ET SCAN Possible SSH Scan [**] [Classification: Attempted Unauthorized Access] [Priority: 1] {TCP} [REDACTED]:2007 -> [REDACTED]:22
2025-09-05 07:20:00 [**] [1:2000208:1] ET EXPLOIT Possible RDP Brute Force [**] [Classification: Attempted Unauthorized Access] [Priority: 1] {TCP} [REDACTED]:2008 -> [REDACTED]:3389
2025-09-05 07:30:00 [**] [1:2000209:1] ET SCAN Possible SSH Scan [**] [Classification: Attempted Unauthorized Access] [Priority: 1] {TCP} [REDACTED]:2009 -> [REDACTED]:22
```

Cấu trúc log IDS

Ví dụ:

```
2025-09-05 06:10:00
[1:2000201:1]
ET EXPLOIT Possible MS-SMB Lateral Movement
[Classification: Attempted Unauthorized Access]
[Priority: 1]
{TCP}
[REDACTED]:2001 -> [REDACTED]:445
```

Các trường:

2025-09-05 06:10:00:Thời gian

ET:	Rule của Emerging Threats

Possible MS-SMB Lateral Movement:	IDS nghi ngờ hành vi lateral movement qua SMB

Attempted Unauthorized Access:	Phân loại: cố gắng truy cập trái phép

Priority: 1	Mức ưu tiên cao nhất

TCP: Giao thức

Source → Destination:	Nguồn → Đích

Commandline: cat ids_alerts.log | grep C2 | head

```
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat ids_alerts.log | grep C2 | head
2025-09-11 01:00:00 [**] [1:2001000:1] ET TROJAN Possible C2 Beaconing [**] [Classification: A network Trojan was detected] [Priority: 1] {TCP} [REDACTED]:30000 -> [REDACTED]:4444
2025-09-11 07:00:00 [**] [1:2001001:1] ET TROJAN Possible C2 Beaconing [**] [Classification: A network Trojan was detected] [Priority: 1] {TCP} [REDACTED]:30001 -> [REDACTED]:4444
2025-09-11 13:00:00 [**] [1:2001002:1] ET TROJAN Possible C2 Beaconing [**] [Classification: A network Trojan was detected] [Priority: 1] {TCP} [REDACTED]:30002 -> [REDACTED]:4444
2025-09-12 13:00:00 [**] [1:2001006:1] ET TROJAN Possible C2 Beaconing [**] [Classification: A network Trojan was detected] [Priority: 1] {TCP} [REDACTED]:30006 -> [REDACTED]:4444
2025-09-12 19:00:00 [**] [1:2001007:1] ET TROJAN Possible C2 Beaconing [**] [Classification: A network Trojan was detected] [Priority: 1] {TCP} [REDACTED]:30007 -> [REDACTED]:4444
2025-09-13 01:00:00 [**] [1:2001008:1] ET TROJAN Possible C2 Beaconing [**] [Classification: A network Trojan was detected] [Priority: 1] {TCP} [REDACTED]:30008 -> [REDACTED]:4444
2025-09-13 07:00:00 [**] [1:2001009:1] ET TROJAN Possible C2 Beaconing [**] [Classification: A network Trojan was detected] [Priority: 1] {TCP} [REDACTED]:30009 -> [REDACTED]:4444
```

Cấu trúc log 

Lấy dòng đầu: 

```
2025-09-11 01:00:00
[1:2001000:1]
ET TROJAN Possible C2 Beaconing
[Classification: A network Trojan was detected]
[Priority: 1]
{TCP}
[REDACTED]:30000 -> [REDACTED]:4444
```

ET TROJAN:	Rule thuộc nhóm Trojan

Possible C2 Beaconing:	Nghi ngờ beacon tới máy chủ C2

Classification:	Trojan trên mạng được phát hiện

Priority: 1	Mức ưu tiên cao nhất

TCP:	Giao thức

:30000:	Cổng nguồn

:4444:	Cổng đích

`Method 2: Analyzing Logs via Splunk`

![alt text](image-1.png)

![alt text](image-2.png)



