---
layout: post
title: Oracle Cloud 외부 접속 설정
subtitle: 
categories: Server
tags: [Server, Oracle]
comments: true
published: false
---

오늘은 Oracle Cloud 서버에  
nginx에 클라이언트를 배포해 외부에서 접속하고  
WorkBench를 이용해 mysql을 접속하려한다.   

외부접속관련해 설정을 정리해보려한다. 

[//]: # (<br/>)

[//]: # ()
[//]: # (### 📌 VCN 설정  )

[//]: # ()
[//]: # (![image]&#40;&#41;{: .align-left style="max-width: 100%"})

[//]: # ()
[//]: # (![image]&#40;&#41;{: .align-left style="max-width: 100%"})

[//]: # ()
[//]: # (![image]&#40;&#41;{: .align-left style="max-width: 100%"})

[//]: # ()
[//]: # (![image]&#40;&#41;{: .align-left style="max-width: 100%"})

<br/>


### 📌 UFW vs iptables

iptables를 이용해 방화벽 설정을 할 수 있다.   

```shell
sudo iptables -I INPUT 1 -p tcp --dport 80 -j ACCEPT
```
와 같이 입력해주면 설정이 가능하다.   
UFW는 이를 좀더 쉽게m 설정할 수 있도록 해주는 명령어이고  
수준높은 방화벽 구성에는 iptables를 사용해야한다.  

<br/>

### 📌 UFW 활성/비활성

**1). UFW 활성화**

```shell
sudo ufw enable
```

<br/>

**2). UFW 비활성화**

```shell
sudo ufw disable
```

<br/>

**3). UFW 상태확인**

```shell
sudo ufw status verbose
```

상태확인 명령어를 입력하면

```shell
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
3306/tcp                   ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
3306/tcp (v6)              ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)
```
와 같이 허용한 포트에 대해 표시가된다. ex) 80, 3306  
위에서 확인이 가능하듯, 기본룰은

incoming는 전부 거부(deny)  
outgoing은 전부 허가(allow)  
로 UFW에 기본으로 설정되어있는 룰이다.

<br/>


### 📌 UFW 허용,차단 룰추가

**1). http 80번 포 TCP,UDP 포트를 모두허용**

```shell
sudo ufw allow 80
```

<br/>

**2). http 80번 포 TCP 포트만 허용**

```shell
sudo ufw allow 80/tcp
```

<br/>

**3). http 80번 포 UDP 포트만 허용**

```shell
sudo ufw allow 80/udp
```

<br/>

반대로 차단과 같은 경우에는 allow자리에   
`deny` 명령어를 입력해주면 된다.  

<br/>  

### 📌 UFW 허용,차단 룰 삭제  

위에서 `allow`, `deny`명령어를 이용해   
허용과 차단 룰을 추가해주었다.   
`sudo ufw status verbose`로 조회하였을 경우에  
아래와 같이 조회되었다고 가정했을때 

```shell
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
80/tcp                     ALLOW IN    Anywhere
80                         DENY IN     Anywhere
80/tcp (v6)                ALLOW IN    Anywhere (v6)
80 (v6)                    DENY IN     Anywhere (v6)
```
해당 정책들을 삭제하고 싶은 경우에는  

```shell
sudo ufw delete deny 80
```
```shell
sudo ufw delete allow 80/tcp
```
와 같은 방식으로 `delete` 명령어를 이용해 룰을 삭제할 수 있다.


<br/>

✨ 참고 블로그   
[클라우드 방화벽 설정 참고 레퍼런스], [우분투 UFW 설정 참고 레퍼런스]


[클라우드 방화벽 설정 참고 레퍼런스]: https://kibua20.tistory.com/124
[우분투 UFW 설정 참고 레퍼런스]: https://webdir.tistory.com/206
