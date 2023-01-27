---
layout: post
title: Ubuntu Nginx 클라이언트 https 변경
subtitle:
categories: Linux/Unix
tags: [Linux, Unix]
comments: true
published: false
---

### ✅ 작업환경

```text
Oracle Cloud 인스턴스 (Ubuntu 22.04)
```

<br/>

### 📌 Certbot 설치하기

```shell
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx
```
와 같은 순서로 설치를 진행해주자

<br/>

❗️**에러상황**️❗️

**1). 에러상황 1**
```shell
E: The repository 'https://ppa.launchpadcontent.net/certbot/certbot/ubuntu jammy Release' does not have a Release file.
```
만약 위와 같은 에러를 만나게 될경우에는
```shell
sudo apt-add-repository -r ppa:certbot/certbot
```
명령어로 설치를 진행하자

<br/>

**1). 에러상황 2**

```shell
E: Unable to locate package python-certbot-nginx
```
만약 위와 같은 에러를 만나게 될경우에는
```shell
sudo apt-get install certbot python3-certbot-nginx
```
명령어로 설치를 진행하자

<br/>

###  

```shell
sudo certbot --nginx
```



![image](){: .align-left style="max-width: 100%"}


[클라우딩 컴퓨터 배포 참고레퍼런스]: 




