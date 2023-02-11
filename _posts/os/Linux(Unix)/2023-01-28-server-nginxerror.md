---
layout: post
title: Nginx 에러 조치 , Failed to start A high performance web server and a reverse proxy server.
subtitle:
categories: Linux/Unix
tags: [Linux, Unix]
comments: true
published: true
---

https 관련 설정을 만지다가  
nginx를 실행하는데 아래와 같은 에러가 발생했다.
```shell
$ sudo service nginx start
Job for nginx.service failed because the control process exited with error code.
See "systemctl status nginx.service" and "journalctl -xeu nginx.service" for details.
```
터미널에 나온대로 `systemctl status nginx.service`를   
입력하여 로그를 확인해봤다.

```shell
$ systemctl status nginx.service
× nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Sat 2023-01-28 02:54:33 KST; 14s ago
       Docs: man:nginx(8)
    Process: 11722 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
    Process: 11723 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=1/FAILURE)
        CPU: 27ms

Jan 28 02:54:32 instance-20230101-2217 nginx[11723]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Unknown error)
Jan 28 02:54:32 instance-20230101-2217 nginx[11723]: nginx: [emerg] bind() to [::]:80 failed (98: Unknown error)
Jan 28 02:54:32 instance-20230101-2217 nginx[11723]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Unknown error)
Jan 28 02:54:32 instance-20230101-2217 nginx[11723]: nginx: [emerg] bind() to [::]:80 failed (98: Unknown error)
Jan 28 02:54:33 instance-20230101-2217 nginx[11723]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Unknown error)
Jan 28 02:54:33 instance-20230101-2217 nginx[11723]: nginx: [emerg] bind() to [::]:80 failed (98: Unknown error)
Jan 28 02:54:33 instance-20230101-2217 nginx[11723]: nginx: [emerg] still could not bind()
Jan 28 02:54:33 instance-20230101-2217 systemd[1]: nginx.service: Control process exited, code=exited, status=1/FAILURE
Jan 28 02:54:33 instance-20230101-2217 systemd[1]: nginx.service: Failed with result 'exit-code'.
Jan 28 02:54:33 instance-20230101-2217 systemd[1]: Failed to start A high performance web server and a reverse proxy server.
```

위와 같은 에러 코드들이 나왔다.   
`Failed to start A high performance web server and a reverse proxy server.`를  
키워드로 검색하니 [Stackoverflow 해결 레퍼런스]로 문제 해결이 가능했다.  

나와 같은 경우는  
```shell
sudo fuser -k 80/tcp
sudo systemctl restart nginx
```
를 입력하여주니 오류가 해결되었다.

내용을 보자하니.. 이미 실행 80으로 실행중인 포트가 있었던 것..  
`sudo lsof -i:80`를 입력해서 한번 확인후

위에 명령어처럼 해당하는 포트를 중지시켜주자!




[Stackoverflow 해결 레퍼런스]: https://stackoverflow.com/questions/51525710/nginx-failed-to-start-a-high-performance-web-server-and-a-reverse-proxy-server




