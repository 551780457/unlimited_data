**正在更新中**

#HTTP Injector
**Mobile proxy server with the ability to modify requests and access blocked websites behind firewall, etc.**

|Glossary | Payload Keyword | Inject Method | Troubleshoot | VPN |
|:-------:|:---------------:|:-------------:|:------------:|:---:|

##Glossary

Before we get started, let's understand the meaning

|  #   |  Word  |                           Meaning                          |
|:-----|:-------|:-----------------------------------------------------------|
|**[Main]**|Payload | HTTP header data payload that will be injected             |
||Remote Proxy| Remote HTTP proxy (ex: Squid proxy/APN proxy)|
||Debug Mode| Show your data that you send/received in app log (Please turn off during normal usage)|
||Battery Saver | Disable Wakelock that prevent your CPU from sleeping
||Google DNS|| Use Google DNS to bypass blocked websites and prevent your ISP from knowing what websites you're visiting.|
||Start SSH |Start SSH connection upon starting injection.|
|**[SSH/Setting]**|Connect Mode |There's two modes, iptables (root) which will require root and it only tunnel port 80 and 443 traffic.VPN Service mode is for Android 4.0+ and will tunnel all traffic to your server. (Please use root mode, if you are having issue with VPN Service mode)|
||SSH |Secure Shell is a encrypted network protocol for initiating text-based shell sessions on remote machines in a secure way.|
||SSH Host |SSH IP/hostname|
||SSH Port |The port for your SSH server, normally will be port 22 (Normally public squid/http proxy will block port 22)|
||Username |SSH username|
||Password |SSH password|
||Data Compression |Compress data packet using zlib.|
||Upstream Proxy |HTTP Injector currently is using port 8989 (fixed), you have to enable and set 127.0.0.1:8989 for the SSH to connect to HTTP Injector local proxy server.|

##Payload Keyword

Using the below keyword, the app will automatically replace with relavent data upon injection, you don't need to replace the word inside the square bracket unless you know what you are doing.
Supported keyword: [host] [port] [host_port] [protocol] [netData] [cr] [lf] [crlf] [lfcr]
Assume that 188.100.100.123 is your SSH Server IP and 22 is your SSH port
Keyword Auto Replaced   Meaning
[host]  188.100.100.123 Destination host
[port]  22  Destination port
[host_port] 188.100.100.123:22  Destination host and port, seperated by colon :
[ssh]   188.100.100.123:22  SSH server ip and port that you set at settings
[protocol]  HTTP\1.0 or HTTP\1.1    HTTP protocol version
[netData]   CONNECT [host_port] [protocol]  Short form of three keywords
[cr]    \r  Carriage Return, U+000D
[lf]    \n  Line Feed, U+000A
[crlf]  \r\n    CR (U+000D) followed by LF (U+000A)
[lfcr]  \n\r    LF (U+000A) followed by CR (U+000D)
[crlf][crlf]    \r\n\r\n    To indicate the end of HTTP header

##Inject Method

Notice: In this example we are using "CONNECT" & "HEAD" request method and domain.com as the host you want to spoof, you might need to use GET/POST/DELETE/CONNECT/HEAD...
Make sure your remote HTTP proxy allow the request method else you will get 403 error.
Tips: Use "Payload Generator" inside HTTP Injector app instead of writing manually. Normally we just need to use "CONNECT" request method
Method  Payload
Normal  CONNECT [host_port] [protocol][crlf]Host: domain.com[crlf][crlf]
Front Inject    GET http://domain.com/ HTTP/1.1[crlf]Host: domain.com[crlf][crlf]CONNECT [host_port] [protocol][crlf][crlf]
Back Inject CONNECT [host_port] HTTP/1.1[crlf][crlf]GET http://domain.com/ [protocol][crlf]Host: domain.com[crlf][crlf]
Front Query CONNECT domain.com@[host_port][crlf]GET http://domain.com/ [protocol][crlf]Host: domain.com[crlf][crlf]
Back Query  CONNECT [host_port]@domain.com[crlf]GET http://domain.com/ [protocol][crlf]Host: domain.com[crlf][crlf]

##Troubleshoot

**302 Error code **- If you are getting 302 HTTP header code that means your ISP is redirecting your request to their captive portal page. Normally ISP blacklisted your remote proxy or the HTTP Payload
**400 Error code **- This indicate that your payload is invalid and can't be understand by remote proxy
**Connection timeout **- It could be that your network connection is slow or the remote proxy is slow
VPN

Psiphon - To use with psiphon (uncheck Start SSH), you can't use [host_port] in your payload, you need to get the SSH IP of psipon and hardcode it (not tested)
OpenVPN - Disable "Start SSH" and use HTTP Injector with your OpenVPN if you don't have SSH server.
You have to add http-proxy 127.0.0.1 8989 and bypass route route replace_to_your_remote_proxy_ip 255.255.255.255 net_gateway (change "replace_to_your_remote_proxy_ip" to IP) to your VPN config.
Evozi - email@evozi.com