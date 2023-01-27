---
layout: post
title: AWS EC2 NginX 클라이언트 배포
subtitle:
categories: Linux/Unix
tags: [Linux, Unix]
comments: true
published: true
---

### ✅ NGINX 이용하여 클라이언트 배포하기

[클라우딩 컴퓨터 배포 참고레퍼런스]를 토대로  
공부하고 테스트해본 기록을 적어보려한다.   

우리 코스에 한분이 작성하신 글이고  
헤매고있는 나에게 빛줄기를 내려주셨다...🥹   

우선 우리팀에서 생각한 아키텍처이다.  
정확히 맞는지는 모르겠지만, 우리가 아는 지식을 모아 그려봤다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gN7pzIQwGbZHGCEUS4jHehIs9yAoOY3RHwfGUqiNHJgziufPka455gX63ZA492CwBarFgJwzDiqJx6XRydBBBJCWJJYNA=w1920-h921){: .align-left style="max-width: 100%"}

AWS 클라우딩 컴퓨터를 이용한 S3 - EC2 - RDS를 운용하고 싶었지만...   
프리티어를 사용하는 우리에게 혹시나 비용문제가 걸림돌이 될 수 있을 것 같아  
꼼꼼히 홈페이지 사양을 읽어보긴했지만,,, 아직 미숙한 우리들에게 사고를  
미연에 방지하고자 EC2만 빌린다음에 진행하려고한다.       

<br/>  

#### 📌 작업 환경
```text
NGINX version : nginx/1.22.1
EC2 인스턴스 유형 : Ubuntu, 22.04 LTS (HVM), SSD Volume Type , 64비트(x86)  
JDK : openjdk version 11.0.17
작업 컴퓨터 OS : macOS Monterey 12.6
```


<br/>  

#### 📌 NGINX 설치

EC2 인스턴스에 Nginx를 설치해줘야한다.   
```shell
sudo apt-get update
sudo apt-get install nginx
```
업데이트를 해주고 nginx를 설치해주면 된다.  

설치가 완료되었다면 `nginx -version`을 입력하면  
`nginx version: nginx/1.22.1`와 같이 버젼이   
나오면 정상적으로 설치된 것이다.   

<br/>  

#### 📌 NGINX 시작, 종료, 삭제

1). 시작 명령어
```shell
sudo service nginx start
```

<br/>  

2). 중지 명령어  
```shell
sudo service nginx stop
```

<br/>  

3). 상태 확인 명령어  
```shell
sudo service nginx status
```

<br/>  

4). 설정파일 테스트 명령어  
```shell
sudo nginx -t 
```  

<br/>

5). 재시작 명령어 
```shell
sudo service nginx restart
```

<br/>

6). nginx 설치 삭제 명령어 
```shell
apt-get remove --purge nginx nginx-full nginx-common 
```
폴더부터 전부 삭제하는 방법이니 뭔가 꼬였다 싶을때  
경건한마음으로 삭제했다가 다시시도해봐도될 것 같다.

<br/>


이렇게 5가지를 이용해 중지,시작 및 상태확인이 가능하다.

처음에 Start 명령어로 시작을한다면   
기본 페이지인 아래의 화면이 나올 것이다.   
도메인 주소는 당연히 EC 인스턴스의 IPv4를 입력해주면된다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPDQyLIrueFY34ZhChcQqOsepcLPLrsa-qd8SRR-U_eG55nRUstLL8aOqIAjkjg84o68D2be4WfoeOlMewuzCKEmjHv=w1920-h921){: .align-left style="max-width: 100%"}

기본 설정 PORT가 80이기 때문에  
`http://3.35.174.126` 으로 입력하여도 접속이 가능한 것이다.

해당 html 파일의 주소에 접속하고 싶다면  
`/usr/share/nginx/html` 경로로 이동하면   
`index.html`과 `50x.html`을 확인할 수 있다.  

<br/>  

#### 📌 NGINX 설정

`/etc/nginx` 폴더에 설정 관련파일들을 찾아볼 수 있다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPlZj5J-OVdyUfWtLi-3GsOpph99bcO6chVUREqjXeqUdIDkUOKwDuTaIgeCapp2Qls01XhQ1J7hcVYQA0VCaSmXCmlDQ=w1656-h864){: .align-left style="max-width: 100%"}


여기서 `nginx.conf` 파일에 설정을 시작해주면 된다.  
`nginx.conf`파일에는 기본적으로 `conf.d` 폴더안의 `.conf`파일들을  
읽을 수 있도록 코드가 작성되어 있어, `conf.d` 폴더에 새로운 `.conf`파일을  
만들어서 사용 설정이 가능하다.  
(`include /etc/nginx/conf.d/*.conf;` 이런 코드가 있다.)
  
`conf.d`폴더에는 `default.conf` 파일이 있다.   
해당 파일을 수정하여 사용해도되고, 새로운 `.conf`파일을 만들어도 된다.   

나와 같은 경우는 `nginx.conf` 파일은 초기설정 그대로 두고  
`conf.d`폴더에 1).`client.conf` , 2).`server.conf` 두개를 만들었다.   

**1). client.conf**  
```text
server {
  server_name stackoverflowpre.p-e.kr;
  root /home/ubuntu/preproject/seb41_pre_037/client/build;
  index index.html index.htm;
  try_files $uri $uri/ /index.html;
  location / {
    try_files $uri $uri/ /index.html;
  }
}
```
server_name에는 [내 도메인 한국]에서 받은   
도메인(stackoverflowpre.p-e.kr)으로 적어주었고    
EC2 IPv4 주소와 연결되어있다.  

root에는 프론트엔드쪽 빌드된 파일이 올라가있는 경로이다.


<br/>

**2). server.conf**
```text
upstream preServer {
        ip_hash;
        server 127.0.0.1:8080;
}

server {

        server_name stackoverflowpre.p-e.kr;
        location / {

            proxy_pass http://preServer; # 위 upstream 에서 설정한 이름

            proxy_set_header    HOST $http_host;

            proxy_set_header    X-Real-IP $remote_addr;

            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;

            proxy_set_header    X-Forwarded-Proto $scheme;

            proxy_redirect  off;
            charset utf-8;
        }

}
```
preServer에 `localhost:8080`인 `127.0.0.1:8080`을 설정해주었고 (스프링 톰켓서버)  
server_name에는 동일하게 도메인을 넣어주었다.  

이제 jar 빌드파일을 실행시켜놓고   
`sudo service nginx start ` 명령어를 입력해주면

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gP7Sh6YFwUrS61qHgvcTyuBFnYthfSnOon6P1upWJ51Brs04kAsppAbtReYthNfYC4poomu4WMycAhF6xnd6bN9sVF8OQ=w1656-h864){: .align-left style="max-width: 100%"}

우리가 만들어 놓은 `index.html` 화면이 보이기 시작하면서    
정삭적으로 배포환경에서 동작하는지 확인이 가능하다 !!   
(스택오버플로우 클론코딩을 한 도메인이다)  



[클라우딩 컴퓨터 배포 참고레퍼런스]: https://velog.io/@gudcks0305/%EB%B0%B0%ED%8F%AC-Cloud-Compute-%EB%A1%9C-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0
[내 도메인 한국]: https://xn--220b31d95hq8o.xn--3e0b707e/



