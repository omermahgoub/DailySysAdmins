## TCPDUMP : Dump HTTP headers as ASCII and HEX

The internet is a mix of protocols or suite of protocols at multiple OSI layers that carry information from one host to the another. Some of these protocols are encrypted before being sent out and others are sent in clear text. Anyone with access to the wire can parse unencrypted protocol and read them in clear text. Some of these are the very well known HTTP, FTP, DNS and SMTP (web, file transfer, domain resolution and mail basically). These are the parents of more secure and almost more widely used HTTPS, SFTP and FTPS, SMTPS / STARTTLS.

DNS encryption is a sensitive subject as it's purpose is to be as less chatty and as fast as possible, hence it relies on UDP not TCP, and encryption would require a few roundtrips before DNS packets can be exchanged (query and answer).

Let's discuss how we can print these clear text protocols ASCII information (headers and payload).
> Note that, first we need to know that tcpdump shipped with older Linux operating system has a default capture size of 68 or 94 bytes. Pay attention to the "capture size 65535 bytes" line right after starting tcpdump. More recent versions of the program use a default of 65535.

Tcpdump headers that we are interested in is http protocol

This shows just few of the benefits of capturing traffic with tcpdump. We can print out any ASCII characters, print HEX dumps of the traffic as well as use internal tcpdump protocol dissectors using the verbose (-v).

### Dump HTTP request ASCII characters with TCPDUMP

HTTP has a client and a server (obviously), but the HTTP request (from client to server) is usually small (if it is a GET) and fits one packet so it's easy to capture in tcpdump if we also apply a filter for TCP PSH (push) flag that tells the receiver (server) to push the payload from kernel to application, but the HTTP response will in tcpdump will be captured as whole - http response header and the payload which can either be very large or unreadable, if it is gzipped.

```
tcpdump -nni eth0 -A port 80 and 'tcp[13] & 8!=0'
```
> Output
```
23:29:26.309683 IP 15.10.83.176.20997 > 10.1.1.2.80: Flags [P.], seq 3879768457:3879769025, ack 3425363442, win 4117, options [nop,nop,TS val 831443251 ecr 1419347905], length 568
E..l..@.6...U...
...R..P.@...*......V>.....
1..3T...GET / HTTP/1.1
Host: ivorde.ro
Connection: keep-alive
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/40.0.2214.111 Safari/537.36
Accept-Encoding: gzip, deflate, sdch
Accept-Language: en-GB,en;q=0.8,en-US;q=0.6,nl;q=0.4,ro;q=0.2
Cookie: __utma=139744236.211547204.1421939507.1423480112.1423870763.15; __utmc=139744236; __utmz=139744236.1423870763.15.6.utmcsr=dmoz.org|utmccn=(referral)|utmcmd=referral|utmcct=/search
```

### Dump HTTP response ASCII information
```
# tcpdump -nni eth0 -A src port 80
```

> Output
```
23:31:18.513251 IP 10.1.1.2.80 > 15.10.83.176.20997: Flags [.], seq 1:8089, ack 717, win 15, options [nop,nop,TS val 1419376015 ecr 831554923], length 8088
E....i@.@.;.
......d.P......FF.............
T...1..kHTTP/1.1 200 OK
Server: nginx
Date: Wed, 18 Feb 2015 21:31:18 GMT
Content-Type: text/html
Transfer-Encoding: chunked
Connection: keep-alive
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Content-Encoding: gzip

2293
...........=is.8......hv.(.5EQ.%..ub'q..{c....bQ"$1.H6IY.l...}..OQ.m.v4cWbK$.....x..~:>.}...   ....\|zuv..H..|i.V...c.....3.Vk...m..L..-E9. ...4..wOQf.Yu..:.H..%...>...X...6o..iOo...6..O........._.jj...w...i...i@d.ZC{........~.n...c..T7D'......'.P...9....D..t`...?.........o...x......6?Y..._..K....7.....}.
.B.@3%...Z.;...B..@..>%o..A}.......g....p
```

### Dump protocol data in ASCII and HEX

```
# tcpdump -nni eth0 -A -X src port 80
```
> Output
```
23:34:09.091553 IP 10.1.22.2.80 > 192.168.3.100.58623: Flags [.], seq 0:8088, ack 1, win 45, options [nop,nop,TS val 1419418660 ecr 831724900], length 8088
   0x0000:  4500 1fcc cad7 4000 4006 6c45 0a01 1602  E.....@.@.lE....
   0x0010:  c0a8 0364 0050 e4ff a9ad b08c 4f5c 555e  ...d.P......O\U^
   0x0020:  8010 002d 03ce 0000 0101 080a 549a 9c24  ...-........T..$
   0x0030:  3193 1d64 4854 5450 2f31 2e31 2032 3030  1..dHTTP/1.1.200
   0x0040:  204f 4b0d 0a53 6572 7665 723a 206e 6769  .OK..Server:.ngi
   0x0050:  6e78 0d0a 4461 7465 3a20 5765 642c 2031  nx..Date:.Wed,.1
   0x0060:  3820 4665 6220 3230 3135 2032 313a 3334  8.Feb.2015.21:34
   0x0070:  3a30 3920 474d 540d 0a43 6f6e 7465 6e74  :09.GMT..Content
   0x0080:  2d54 7970 653a 2074 6578 742f 6874 6d6c  -Type:.text/html
   0x0090:  0d0a 5472 616e 7366 6572 2d45 6e63 6f64  ..Transfer-Encod
   0x00a0:  696e 673a 2063 6875 6e6b 6564 0d0a 436f  ing:.chunked..Co
   0x00b0:  6e6e 6563 7469 6f6e 3a20 6b65 6570 2d61  nnection:.keep-a
   0x00c0:  6c69 7665 0d0a 4578 7069 7265 733a 2054  live..Expires:.T
   0x00d0:  6875 2c20 3139 204e 6f76 2031 3938 3120  hu,.19.Nov.1981.
   0x00e0:  3038 3a35 323a 3030 2047 4d54 0d0a 4361  08:52:00.GMT..Ca
   0x00f0:  6368 652d 436f 6e74 726f 6c3a 206e 6f2d  che-Control:.no-
   0x0100:  7374 6f72 652c 206e 6f2d 6361 6368 652c  store,.no-cache,
   0x0110:  206d 7573 742d 7265 7661 6c69 6461 7465  .must-revalidate
   0x0120:  2c20 706f 7374 2d63 6865 636b 3d30 2c20  ,.post-check=0,.
   0x0130:  7072 652d 6368 6563 6b3d 300d 0a50 7261  pre-check=0..Pra
   0x0140:  676d 613a 206e 6f2d 6361 6368 650d 0a43  gma:.no-cache..C
   0x0150:  6f6e 7465 6e74 2d45 6e63 6f64 696e 673a  ontent-Encoding:
   0x0160:  2067 7a69 700d 0a0d 0a32 3239 330d 0a1f  .gzip....2293...
   0x0170:  8b08 0000 0000 0000 03ed 3d69 73db 3896  ..........=is.8.
   0x0180:  9f93 aaf9 0f68 76cd 2899 3545 5197 251f  .....hv.(.5EQ.%.
   0x0190:  9a75 6227 71b7 137b 63e7 e89d 9a62 5122  .ub'q..{c....bQ"
   0x01a0:  2431 a148 3649 59d6 6cef fef6 7d0f 004f  $1.H6IY.l...}..O
   0x01b0:  5112 6dd1 7634 6357 624b 24f8 f02e bc03  Q.m.v4cWbK$.....
```
