

#### В чем проблема ? Что случилось? Что наблюдаем?

У пользователей недоступны ресурсы по https из наших подсетей **109.172.0.0/16**. Предположительно может быть блокировка по ТСПУ.

Тикеты с жалобами:
- https://hp.beget.ru/ticket/2581109
- https://hp.beget.ru/ticket/2577663

В примере из тикета https://hp.beget.ru/ticket/2577663 вывод curl:
```
ibel@DESKTOP-1418RO6:~$ curl -ILvk http://crm.florcat.ru
*   Trying 109.172.39.81:80...
* Connected to crm.florcat.ru (109.172.39.81) port 80 (#0)
> HEAD / HTTP/1.1
> Host: crm.florcat.ru
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 301 Moved Permanently
HTTP/1.1 301 Moved Permanently
< Server: nginx
Server: nginx
< Date: Thu, 22 May 2025 10:16:11 GMT
Date: Thu, 22 May 2025 10:16:11 GMT
< Content-Type: text/html
Content-Type: text/html
< Content-Length: 162
Content-Length: 162
< Connection: keep-alive
Connection: keep-alive
< Location: https://crm.florcat.ru/
Location: https://crm.florcat.ru/
< X-Content-Type-Options: nosniff
X-Content-Type-Options: nosniff
< X-Frame-Options: SAMEORIGIN
X-Frame-Options: SAMEORIGIN

<
* Connection #0 to host crm.florcat.ru left intact
* Clear auth, redirects to port from 80 to 443
* Issue another request to this URL: 'https://crm.florcat.ru/'
*   Trying 109.172.39.81:443...
* Connected to crm.florcat.ru (109.172.39.81) port 443 (#1)
* ALPN, offering h2
* ALPN, offering http/1.1
* TLSv1.0 (OUT), TLS header, Certificate Status (22):
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS header, Certificate Status (22):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS header, Finished (20):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.2 (OUT), TLS header, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
*  subject: CN=florcat.onewaygl.ru
*  start date: May 21 16:22:49 2025 GMT
*  expire date: Aug 19 16:22:48 2025 GMT
*  issuer: C=US; O=Let's Encrypt; CN=R11
*  SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.
* Using HTTP2, server supports multiplexing
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* Using Stream ID: 1 (easy handle 0x564509f4c9f0)
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
> HEAD / HTTP/2
> Host: crm.florcat.ru
> user-agent: curl/7.81.0
> accept: */*
>
* OpenSSL SSL_read: Connection timed out, errno 110
* Failed receiving HTTP2 data
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* OpenSSL SSL_write: Broken pipe, errno 32
* Failed sending HTTP2 data
* Connection #1 to host crm.florcat.ru left intact
curl: (56) OpenSSL SSL_read: Connection timed out, errno 110
```
tcpdump запроса к сайту с устройства:
```
ibel@DESKTOP-1418RO6:~$ sudo tcpdump -i eth0 -n host 109.172.39.81
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
16:55:10.193488 IP 172.24.151.250.50952 > 109.172.39.81.80: Flags [S], seq 3864832953, win 64240, options [mss 1460,sackOK,TS val 1831359454 ecr 0,nop,wscale 7], length 0
16:55:10.214687 IP 109.172.39.81.80 > 172.24.151.250.50952: Flags [S.], seq 1754306716, ack 3864832954, win 65160, options [mss 1460,sackOK,TS val 974126791 ecr 1831359454,nop,wscale 7], length 0
16:55:10.214719 IP 172.24.151.250.50952 > 109.172.39.81.80: Flags [.], ack 1, win 502, options [nop,nop,TS val 1831359475 ecr 974126791], length 0
16:55:10.214753 IP 172.24.151.250.50952 > 109.172.39.81.80: Flags [P.], seq 1:80, ack 1, win 502, options [nop,nop,TS val 1831359475 ecr 974126791], length 79: HTTP: HEAD / HTTP/1.1
16:55:10.235855 IP 109.172.39.81.80 > 172.24.151.250.50952: Flags [.], ack 80, win 509, options [nop,nop,TS val 974126812 ecr 1831359475], length 0
16:55:10.235855 IP 109.172.39.81.80 > 172.24.151.250.50952: Flags [P.], seq 1:254, ack 80, win 509, options [nop,nop,TS val 974126812 ecr 1831359475], length 253: HTTP: HTTP/1.1 301 Moved Permanently
16:55:10.235874 IP 172.24.151.250.50952 > 109.172.39.81.80: Flags [.], ack 254, win 501, options [nop,nop,TS val 1831359496 ecr 974126812], length 0
16:55:10.236681 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [S], seq 1199989899, win 64240, options [mss 1460,sackOK,TS val 1831359497 ecr 0,nop,wscale 7], length 0
16:55:10.258717 IP 109.172.39.81.443 > 172.24.151.250.41022: Flags [S.], seq 507015206, ack 1199989900, win 65160, options [mss 1460,sackOK,TS val 974126834 ecr 1831359497,nop,wscale 7], length 0
16:55:10.258754 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [.], ack 1, win 502, options [nop,nop,TS val 1831359519 ecr 974126834], length 0
16:55:10.295670 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [P.], seq 1:518, ack 1, win 502, options [nop,nop,TS val 1831359556 ecr 974126834], length 517
16:55:10.315898 IP 109.172.39.81.443 > 172.24.151.250.41022: Flags [.], ack 518, win 506, options [nop,nop,TS val 974126892 ecr 1831359556], length 0
16:55:10.321927 IP 109.172.39.81.443 > 172.24.151.250.41022: Flags [P.], seq 1:2897, ack 518, win 506, options [nop,nop,TS val 974126898 ecr 1831359556], length 2896
16:55:10.321927 IP 109.172.39.81.443 > 172.24.151.250.41022: Flags [P.], seq 2897:3652, ack 518, win 506, options [nop,nop,TS val 974126898 ecr 1831359556], length 755
16:55:10.321939 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [.], ack 2897, win 496, options [nop,nop,TS val 1831359582 ecr 974126898], length 0
16:55:10.321960 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [.], ack 3652, win 492, options [nop,nop,TS val 1831359582 ecr 974126898], length 0
16:55:10.323133 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [P.], seq 518:598, ack 3652, win 501, options [nop,nop,TS val 1831359583 ecr 974126898], length 80
16:55:10.323184 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [P.], seq 598:693, ack 3652, win 501, options [nop,nop,TS val 1831359583 ecr 974126898], length 95
16:55:10.323249 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [P.], seq 693:794, ack 3652, win 501, options [nop,nop,TS val 1831359583 ecr 974126898], length 101
16:55:10.343752 IP 109.172.39.81.443 > 172.24.151.250.41022: Flags [P.], seq 3652:3939, ack 794, win 504, options [nop,nop,TS val 974126920 ecr 1831359583], length 287
16:55:10.343752 IP 109.172.39.81.443 > 172.24.151.250.41022: Flags [P.], seq 3939:4226, ack 794, win 504, options [nop,nop,TS val 974126920 ecr 1831359583], length 287
16:55:10.343898 IP 172.24.151.250.41022 > 109.172.39.81.443: Flags [.], ack 4226, win 501, options [nop,nop,TS val 1831359604 ecr 974126920], length 0
```
tcpdump запроса к сайту со стороны сервера:
```
[root@crm ~]# tcpdump host 93.100.211.50
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
09:20:09.568718 IP 93.100.211.50.pool.sknt.ru.60728 > crm.florcat.ru.http: Flags [S], seq 1730528941, win 64240, options [mss 1460,nop,wscale 8,nop,nop,sackOK], length 0
09:20:09.568802 IP crm.florcat.ru.http > 93.100.211.50.pool.sknt.ru.60728: Flags [S.], seq 2911456416, ack 1730528942, win 64240, options [mss 1460,nop,nop,sackOK,nop,wscale 7], length 0
09:20:09.590494 IP 93.100.211.50.pool.sknt.ru.60728 > crm.florcat.ru.http: Flags [.], ack 1, win 1026, length 0
09:20:09.590677 IP 93.100.211.50.pool.sknt.ru.60728 > crm.florcat.ru.http: Flags [P.], seq 1:386, ack 1, win 1026, length 385: HTTP: GET / HTTP/1.1
09:20:09.590693 IP crm.florcat.ru.http > 93.100.211.50.pool.sknt.ru.60728: Flags [.], ack 386, win 501, length 0
09:20:09.590951 IP crm.florcat.ru.http > 93.100.211.50.pool.sknt.ru.60728: Flags [P.], seq 1:416, ack 386, win 501, length 415: HTTP: HTTP/1.1 301 Moved Permanently
09:20:09.617725 IP 93.100.211.50.pool.sknt.ru.60729 > crm.florcat.ru.https: Flags [S], seq 1108542049, win 64240, options [mss 1460,nop,wscale 8,nop,nop,sackOK], length 0
09:20:09.617804 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [S.], seq 3139945429, ack 1108542050, win 64240, options [mss 1460,nop,nop,sackOK,nop,wscale 7], length 0
09:20:09.638419 IP 93.100.211.50.pool.sknt.ru.60729 > crm.florcat.ru.https: Flags [P.], seq 1:1890, ack 1, win 1026, length 1889
09:20:09.638485 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], ack 1890, win 488, length 0
09:20:09.638593 IP 93.100.211.50.pool.sknt.ru.60729 > crm.florcat.ru.https: Flags [.], ack 1, win 1026, length 0
09:20:09.638601 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], ack 1890, win 488, length 0
09:20:09.644638 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [P.], seq 1:2921, ack 1890, win 488, length 2920
09:20:09.644658 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [P.], seq 2921:3636, ack 1890, win 488, length 715
09:20:09.657256 IP 93.100.211.50.pool.sknt.ru.60728 > crm.florcat.ru.http: Flags [.], ack 416, win 1024, length 0
09:20:09.688764 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [P.], seq 2921:3636, ack 1890, win 488, length 715
09:20:09.910802 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], seq 1:1461, ack 1890, win 488, length 1460
09:20:10.358780 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], seq 1:1461, ack 1890, win 488, length 1460
09:20:11.294765 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], seq 1:1461, ack 1890, win 488, length 1460
09:20:13.086801 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], seq 1:1461, ack 1890, win 488, length 1460
09:20:16.670782 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], seq 1:1461, ack 1890, win 488, length 1460
09:20:19.613181 IP 93.100.211.50.pool.sknt.ru.60728 > crm.florcat.ru.http: Flags [.], seq 385:386, ack 416, win 1024, length 1: HTTP
09:20:19.613260 IP crm.florcat.ru.http > 93.100.211.50.pool.sknt.ru.60728: Flags [.], ack 386, win 501, options [nop,nop,sack 1 {385:386}], length 0
09:20:23.966800 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], seq 1:1461, ack 1890, win 488, length 1460
09:20:29.645483 IP 93.100.211.50.pool.sknt.ru.60728 > crm.florcat.ru.http: Flags [.], seq 385:386, ack 416, win 1024, length 1: HTTP
09:20:29.645534 IP crm.florcat.ru.http > 93.100.211.50.pool.sknt.ru.60728: Flags [.], ack 386, win 501, options [nop,nop,sack 1 {385:386}], length 0
09:20:29.771525 IP 93.100.211.50.pool.sknt.ru.60733 > crm.florcat.ru.https: Flags [S], seq 495084630, win 64240, options [mss 1460,nop,wscale 8,nop,nop,sackOK], length 0
09:20:29.771618 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60733: Flags [S.], seq 941952243, ack 495084631, win 64240, options [mss 1460,nop,nop,sackOK,nop,wscale 7], length 0
09:20:29.793035 IP 93.100.211.50.pool.sknt.ru.60733 > crm.florcat.ru.https: Flags [.], ack 1, win 1026, length 0
09:20:29.793421 IP 93.100.211.50.pool.sknt.ru.60733 > crm.florcat.ru.https: Flags [P.], seq 1:657, ack 1, win 1026, length 656
09:20:29.793449 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60733: Flags [.], ack 657, win 497, length 0
09:20:29.798797 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60733: Flags [P.], seq 1:2921, ack 657, win 497, length 2920
09:20:29.798816 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60733: Flags [P.], seq 2921:3636, ack 657, win 497, length 715
09:20:29.820102 IP 93.100.211.50.pool.sknt.ru.60733 > crm.florcat.ru.https: Flags [.], ack 3636, win 1026, length 0
09:20:38.302791 IP crm.florcat.ru.https > 93.100.211.50.pool.sknt.ru.60729: Flags [.], seq 1:1461, ack 1890, win 488, length 1460
09:20:39.679189 IP 93.100.211.50.pool.sknt.ru.60728 > crm.florcat.ru.http: Flags [.], seq 385:386, ack 416, win 1024, length 1: HTTP
09:20:39.679267 IP crm.florcat.ru.http > 93.100.211.50.pool.sknt.ru.60728: Flags [.], ack 386, win 501, options [nop,nop,sack 1 {385:386}], length 0
09:20:49.712395 IP 93.100.211.50.pool.sknt.ru.60728 > crm.florcat.ru.http: Flags [.], seq 385:386, ack 416, win 1024, length 1: HTTP
09:20:49.712467 IP crm.florcat.ru.http > 93.100.211.50.pool.sknt.ru.60728: Flags [.], ack 386, win 501, options [nop,nop,sack 1 {385:386}], length 0
```
Аналогичное поведение с других VPS пользователей которые получилось найти:
- `6b453ec9-6c24-47b1-8d23-d7ab97372097` домен bisieyonid.beget.app **109.172.37.60**
- `c3a61da8-97ee-421f-bd79-68b5a2c24ce1` домен [hijygiy7.sa.com](http://hijygiy7.sa.com) **109.172.39.24**
- `54a84cd3-ddda-4cbe-898e-2abb88067705` домен uyamudaf.beget.app **109.172.39.35**
####  На что влияет:

Недоступность сайтов по https из нашей подсети.

#### Период наблюдения проблемы:

Предположительно проблема начала наблюдаться с 21.05.2025

####  Что сделали:

- Анализ и поиск проблемы у других пользователей, от  которых еще не поступило жалоб.
- Вопрос в топик админов: https://zulip.beget.ru/#narrow/stream/124-tech/topic/.D0.B2.D0.BE.D0.BF.D1.80.D0.BE.D1.81.D1.8B.20.D0.B0.D0.B4.D0.BC.D0.B8.D0.BD.D0.B0.D0.BC/near/26152054