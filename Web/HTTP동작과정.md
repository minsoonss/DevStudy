# HTTP 동작 과정

사용자가 URL을 웹 브라우저에 입력하면 URL을 해독하는 곳 부터 동작이 시작됨

protocol://host:port/path/filename

### 순서

1. 사용자가 웹 브라우저에 URL 주소 입력
2. DNS 서버에 웹 서버의 호스트 이름을 IP 주소로 변경 요청
3. 웹 서버와 TCP 연결 시도
   - 3 way Handshake : 클라이언트 - 서버간 신뢰성 있는 연결을 하기 위해 3번의 패킷 교환 과정.
     1. SYN : 클라이언트가 서버로 임의로 생성한 시퀀스 번호를 전달
     2. SYN ACK : 서버는 클라이언트에서 전달한 시퀀스를 +1 시켜서 전달
     3. ACK : 클라이언트가 서버에서 전달해준 시퀀스를 +1 시켜서 다시 전달
4. 서버에게 명령 전송(Request)
5. 서버가 클라이언트에게 데이터를 회신
6. 서버 - 클라이언트간 연결 해제
   - 4 way Handshake : 서버와 클라이언트 양쪽 다 연결 종료시킨다는 메세지 전달
     1. Client -> Server : FIN
     2. Server -> Client : ACK
     3. Server -> Client : FIN
     4. Client -> Server : ACK
7. 웹 브라우저가 웹 문서를 출력



#### tcpdump 내용

```shell
root@9fad56569586:/# tcpdump -ni eth0 -vv
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
06:59:01.755949 IP (tos 0x0, ttl 64, id 50375, offset 0, flags [DF], proto TCP (6), length 60)
    172.20.20.2.57948 > 172.21.21.100.80: Flags [S], cksum 0x81be (incorrect -> 0xdb0f), seq 874923841, win 64240, options [mss 1460,sackOK,TS val 3957958257 ecr 0,nop,wscale 7], length 0
06:59:01.756177 IP (tos 0x0, ttl 64, id 0, offset 0, flags [DF], proto TCP (6), length 60)
    172.21.21.100.80 > 172.20.20.2.57948: Flags [S.], cksum 0xd087 (correct), seq 1407129372, ack 874923842, win 5792, options [mss 1460,sackOK,TS val 2458534 ecr 3957958257,nop,wscale 8], length 0
06:59:01.756270 IP (tos 0x0, ttl 64, id 50376, offset 0, flags [DF], proto TCP (6), length 52)
    172.20.20.2.57948 > 172.21.21.100.80: Flags [.], cksum 0x81b6 (incorrect -> 0x13ff), seq 1, ack 1, win 502, options [nop,nop,TS val 3957958257 ecr 2458534], length 0
06:59:01.756349 IP (tos 0x0, ttl 64, id 50377, offset 0, flags [DF], proto TCP (6), length 129)
    172.20.20.2.57948 > 172.21.21.100.80: Flags [P.], cksum 0x8203 (incorrect -> 0x899b), seq 1:78, ack 1, win 502, options [nop,nop,TS val 3957958257 ecr 2458534], length 77: HTTP, length: 77
        GET / HTTP/1.1
        Host: 172.21.21.100
        User-Agent: curl/7.52.1
        Accept: */*

06:59:01.756549 IP (tos 0x0, ttl 64, id 38075, offset 0, flags [DF], proto TCP (6), length 52)
    172.21.21.100.80 > 172.20.20.2.57948: Flags [.], cksum 0x1591 (correct), seq 1, ack 78, win 23, options [nop,nop,TS val 2458534 ecr 3957958257], length 0
06:59:01.757972 IP (tos 0x0, ttl 64, id 38076, offset 0, flags [DF], proto TCP (6), length 1500)
06:59:01.757972 IP (tos 0x0, ttl 64, id 38076, offset 0, flags [DF], proto TCP (6), length 1500)
    172.21.21.100.80 > 172.20.20.2.57948: Flags [.], cksum 0xaf0c (correct), seq 1:1449, ack 78, win 23, options [nop,nop,TS val 2458534 ecr 3957958257], length 1448: HTTP, length: 1448
        HTTP/1.1 200 OK
        Date: Tue, 07 Apr 2020 06:15:37 GMT
        Content-Type: text/html
        Content-Length: 10918
        Connection: keep-alive
        Keep-Alive: timeout=60
        Server: Apache/2.4.25 (Ubuntu)
        Last-Modified: Fri, 15 Dec 2017 01:25:22 GMT
        ETag: "2aa6-56056dfd0a480"
        Accept-Ranges: bytes
        Vary: Accept-Encoding
        Set-Cookie: pask=ac1515020050; Max-Age=3600; Path=/


        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
        <html xmlns="http://www.w3.org/1999/xhtml">
          <!--
            Modified from the Debian original for Ubuntu
            Last updated: 2016-11-16
            See: https://launchpad.net/bugs/1288690
          -->
          <head>
            <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
            <title>Apache2 Ubuntu Default Page: It works</title>
            <style type="text/css" media="screen">
          * {
            margin: 0px 0px 0px 0px;
            padding: 0px 0px 0px 0px;
          }

          body, html {
            padding: 3px 3px 3px 3px;

            background-color: #D8DBE2;

            font-family: Verdana, sans-serif;
            font-size: 11pt;
            text-align: center;
          }

          div.main_page {
            position: relative;
            display: table;

            width: 800px;

            margin-bottom: 3px;
            margin-left: auto;
            margin-right: auto;
            padding: 0px 0px 0px 0px;

            border-width: 2px;
            border-color: #212738;
            border-style: solid;

            background-color: #FFFFFF;

            text-align: center;
          }

          div.page_header {
            height: 99px;
            width: 1[!http]
06:59:01.758044 IP (tos 0x0, ttl 64, id 50378, offset 0, flags [DF], proto TCP (6), length 52)
    172.20.20.2.57948 > 172.21.21.100.80: Flags [.], cksum 0x81b6 (incorrect -> 0x0e09), seq 78, ack 1449, win 501, options [nop,nop,TS val 3957958259 ecr 2458534], length 0
06:59:01.758083 IP (tos 0x0, ttl 64, id 38077, offset 0, flags [DF], proto TCP (6), length 2948)
    172.21.21.100.80 > 172.20.20.2.57948: Flags [.], cksum 0x8d06 (incorrect -> 0x1e55), seq 1449:4345, ack 78, win 23, options [nop,nop,TS val 2458534 ecr 3957958257], length 2896: HTTP
06:59:01.758120 IP (tos 0x0, ttl 64, id 50379, offset 0, flags [DF], proto TCP (6), length 52)
    172.20.20.2.57948 > 172.21.21.100.80: Flags [.], cksum 0x81b6 (incorrect -> 0x02c7), seq 78, ack 4345, win 487, options [nop,nop,TS val 3957958259 ecr 2458534], length 0
06:59:01.758208 IP (tos 0x0, ttl 64, id 38079, offset 0, flags [DF], proto TCP (6), length 1500)
    172.21.21.100.80 > 172.20.20.2.57948: Flags [.], cksum 0xe066 (correct), seq 4345:5793, ack 78, win 23, options [nop,nop,TS val 2458534 ecr 3957958259], length 1448: HTTP
06:59:01.758264 IP (tos 0x0, ttl 64, id 50380, offset 0, flags [DF], proto TCP (6), length 52)
    172.20.20.2.57948 > 172.21.21.100.80: Flags [.], cksum 0x81b6 (incorrect -> 0xfd10), seq 78, ack 5793, win 501, options [nop,nop,TS val 3957958259 ecr 2458534], length 0
06:59:01.758401 IP (tos 0x0, ttl 64, id 38080, offset 0, flags [DF], proto TCP (6), length 5534)
    172.21.21.100.80 > 172.20.20.2.57948: Flags [P.], cksum 0x9720 (incorrect -> 0xf5fd), seq 5793:11275, ack 78, win 23, options [nop,nop,TS val 2458534 ecr 3957958259], length 5482: HTTP
06:59:01.759357 IP (tos 0x0, ttl 64, id 38084, offset 0, flags [DF], proto TCP (6), length 52)
    172.21.21.100.80 > 172.20.20.2.57948: Flags [F.], cksum 0xe981 (correct), seq 11275, ack 79, win 23, options [nop,nop,TS val 2458534 ecr 3957958260], length 0
06:59:01.759436 IP (tos 0x0, ttl 64, id 50383, offset 0, flags [DF], proto TCP (6), length 52)
    172.20.20.2.57948 > 172.21.21.100.80: Flags [.], cksum 0x81b6 (incorrect -> 0xe7a3), seq 79, ack 11276, win 501, options [nop,nop,TS val 3957958260 ecr 2458534], length 0
```

