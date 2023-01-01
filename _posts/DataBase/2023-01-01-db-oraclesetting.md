---
layout: post
title: Oracle Cloud Ubuntu 22.04 1GB 램 성능 개선
subtitle: 
categories: DB
tags: [DB, Oracle]
comments: true
published: true
---

[Oracle Cloud Free Tier 무료 인스턴스 생성과 접속]을 통해서  
오라클 평생 무료 인스턴스를 직접 만들어 보았다.   

하지만 인스턴스를 만들면서 느낀건데 램이 1GB로  
여러가지 설치를하고 사용하기에는 너무 무리가 있어보이는 사양을 확인할 수가 있다.    

AMD 기반으로 만들었기 때문에 사양이 낮은 무료 인스턴스를 사용하고 있지만  
몇 가지 설정을 통해 시스템을 원할히 돌아가게 셋팅을할 수가 있다.  

한번 알아보자  

#### 📌 현재 인스턴스 사양

```text
이미지 : Ubuntu 22.04
구성 : (AMD) VM.Standard.E2.1.Micro / 1 core OCPU, 1GB Memory
```

<br/>

#### 📌 기초 셋팅  

```shell
sudo apt update
```
가장 먼저 시스템 업데이트를 해준다.  

<br/>  

```text
sudo apt install -y nano
```
nano 편집기를 다운받아 편하게 사용해보자

<br/>  

#### 📌 SWAP 공간 생성  

오라클 클라우드 AMD 인스턴스는 위에서 얘기했듯이  
1GB RAM을 제공한다. 이것저것 설치하다보면 금방 RAM이 부족에 부딪힌다.  
상대적으로 넉넉한 HDD 저장공간의 일부를 RAM 공간으로 활용이 가능하다.  

**1). 스왑공간**

```shell
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
```
를 순서대로 입력을 해주게 된다면

```shell
Setting up swapspace version 1, size = 2 GiB (2147479552 bytes)
no label, UUID=8f85218d-329c-4eec-bf11-5d87964c5f0d
```
위와 같이 스왑공간이 정상적으로 생성되었다고 메세지가 뜬다.  

<br/>

**2). 스왑시작**  

```shell
sudo swapon /swapfile
```
위 명령어로 스왑을 시작한다.  

<br/>  

**3). 스왑생성 확인**

```shell
sudo swapon --show
```
명령어로 정상적으로 작동하는지 확인이 가능하다

```shell
NAME      TYPE SIZE USED PRIO
/swapfile file   2G   0B   -2
```
와 같은식으로 확인이되며, 2GB의 스왑공간이 확보되었다고 표시된다.

<br/>  

```shell
free -h
```
명령어를 통해 시스템에서 사용할 수 있는  
메모리 공간을 확인할 수 있다.  

<br/>  

**4). 스왑공간 시스템 고정**

스왑공간은 시스템이 재시작되면 초기화가 진행되므로  
스왑공간을 그대로 남아있게하기 위해서 간단한 셋팅값을 지정해주어야 한다.

```shell
sudo nano /etc/fstab
```

를 입력해 nano 편집기로 fstab 파일을 수정해줄 수 있다.  

```shell
LABEL=cloudimg-rootfs   /        ext4   discard,errors=remount-ro       0 1
LABEL=UEFI      /boot/efi       vfat    umask=0077      0 1
/swapfile swap swap defaults 0 0
```
최종적으로는 맨 아래의 `/swapfile swap swap defaults 0 0`를  
입력한후 저장해주면 된다. nano 편집기 저장방법은 `ctrl+s`한 다음에 `ctrl+x`를  
입력해주면 정상적으로 저장될 것 이다.

이렇게 설정을 완료하면 스왑공간이 기본값으로 활성화 되어  
시스템을 재시작하여도 문제없다 !

<br/>

#### 📌 시간설정  

마지막으로 서버의 시간을 설정해줄 수 있다.  

```shell
sudo timedatectl set-timezone Asia/Seoul
```

<br/>

✨ 참고 블로그   
[클라우드 성능 개선 참고 레퍼런스 1], [클라우드 성능 개선 참고 레퍼런스 2]


[클라우드 성능 개선 참고 레퍼런스 1]: https://yeon-kr.tistory.com/174 
[클라우드 성능 개선 참고 레퍼런스 2]: https://blog.hangyeong.com/1773#%EC%8B%9C%EC%8A%A4%ED%85%9C_%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8
[Oracle Cloud Free Tier 무료 인스턴스 생성과 접속]: https://mycatlikeschuru.github.io/db/2023/01/01/db-oracledb.html