---
layout: post
title: Spring) Web server failed to start. Port 8080 was already in use 에러조치
subtitle:
categories: Spring
tags: [Spring, shell]
comments: true
published: true
---


매우 간단한 문제지만 조치한 내용을 적어본다.  
간혹 SpringBoot 애플리케이션 실행 시 아래와 같은 에러가 발생할 때가 있다.  
`Web server failed to start. Port 8080 was already in use.`

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOFKFwEPyH4_7jurd39N3DVi3THGwMbPs_dJTrUqvTM8pfJN1LsHFbrTv2QH4X74XeEypEkUkaXl2uns6jYE0fTI10XwA=w1185-h921){: .align-left style="max-width: 100%"}

8080포트가 이미 사용중이란 뜻.  
실제로 여러개의 서버를 켜두었거나.. 꺼져있지만 제대로 안꺼졌다는 뜻이다.  



Spotlight (⌘Command + SpaceBar)를 열어 터미널을 검색.

터미널을 띄운 후 아래의 명령어입력.

현재 열려있는 포트를 조회하는 명령어입니다.

```shell
lsof -PiTCP -sTCP:LISTEN
```

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPt2Ly-DDPVkFTKjureI8xqavnbgou9aAY3lM-hnoTfWRsUE57pIrtJhdIckagsaWTnsHQpPLvk64QKFHWeH5SssOBrqg=w1920-h921){: .align-left style="max-width: 100%"}

이렇게 사용중인 포트가 쭉 나온다.  
여기서 PID란 값과 TCP 란값 옆의 PORT 숫자를 기억해야한다.

TCP옆의 포트 8080으로 열려있는 java라는 놈이 문제다.  
분명 나는 build해놓은 프로그램이 없는데 포트가 살아있다... 이유는 잘 모르겠지만  
이 포트를 강제로 죽여줘야한다.  

아래의 명령어를 입력하면 해당 포트가죽는다

```shell
kill -9 pid값
```

나의 8080포트의 pid값은 3186이다  
고로 kill -9 3186 으로 입력하면 해당 포트가 죽을 것이다.

<br/>

**참고**  
포트 전체조회하는 법도 있지만 포트번호를 알경우 선택해서 찾는법

```shell
lsof -n -i4TCP:8080 | grep LISTEN
```
8080포트만 찾는 터미널 명령어.



