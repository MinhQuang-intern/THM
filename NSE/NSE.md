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


