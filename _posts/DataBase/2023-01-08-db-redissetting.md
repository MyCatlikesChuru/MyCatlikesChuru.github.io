---
layout: post
title: Redis CLI 설치 및 사용방법
subtitle: 
categories: DB
tags: [DB, redis]
comments: true
published: true
---

**Redis**는 **Remote Dictionary Server**의 약자로   
`key:value`로 값을 저장하는 NoSQL이다.   
인-메모리(In-Memory) 데이터베이스로 메모리에 데이터가 저장된다.. 

![image](https://user-images.githubusercontent.com/95069395/211693910-59b5da13-847e-4313-aed9-ce48a76c7434.png){: .align-left style="max-width: 100%"}

비관계형 데이터 베이스 관리 시스템(DBMS)이며  
캐시서버로 많이 이용된다.    
캐시는 한번 읽어온 데이터를 임의의 공간에 저장하여  
다음에 읽을 때는 빠르게 결과값을 받을 수 있도록 도와주는 공간이다.  

<br/>   

학습의 목적은 JWT Refresh Token 관리 목적과  
좋아요,혹은 조회수 같은 자주 업데이트가 일어나는 데이터를  
RDBMS에서 계속 쿼리를 날리고 싶지 않기때문이였다.  

본격적으로 알아보기전에 CLI를 통해 설치와 기본적인 명령어를 익혀보자

<br/>

### 📌 Redis 설치

맥을 사용하는 유저라면 brew를 사용할 텐데

```shell
brew install redis
```
를 입력하면 redis 설치가 가능하다.  

<br/>

### 📌 Redis 시작, 종료

기본 적인 redis 시작 명렁어이다.
```shell
brew services start redis
```
`==> Successfully started redis (label: homebrew.mxcl.redis)`  
와 같이 표현되면 정상적으로 실행 된 것이다.   

<br/>

시작이완료되고 아래 명령어를 입력하면
```shell
redis-cli
```
`127.0.0.1:6379>`과 같이 `localhost:6379`로  
접속되었다는 표시가 나올 거다.


<br/>

redis 종료, 재시작 명렁어이다.
```shell
brew services stop redis
```
```shell
brew services restart redis
```

nginx, httpd 등 시작명령어가 비슷하고 익숙할 것이다.

<br/>

### 📌 Redis CRUD

**1). 현재의 키값을 확인**
```shell
127.0.0.1:6379> keys *
```

<br/>

**2). key / value 형태로 저장 (한개만)**
```shell
127.0.0.1:6379> set test a
```
test : key 값이된다.  
a : value 값이된다.  
즉 test라는 키의 a라는 value로 저장!  

<br/>

**3). key / value 형태로 저장 (여러개 한번에)**

```shell
127.0.0.1:6379> mset test1 a test2 b
```
와 같이 입력하게 될 경우, test1,test2 라는 키가 생기고  
a,b라는 value가 생긴다.

<br/>

**4). key의 value를 조회**  
```shell
127.0.0.1:6379> get test
```
test라는 키의 value를 조회한다.   
위에서 a로 지정했으니 a로 출력된다.

<br/>

**5). 소멸시간 지정해서 저장하기**

```shell
127.0.0.1:6379> setex test 10 a
```
와 같이 지정하게 될 경우, test라는 키값은  
10초동안만 존재하게 된다.  
10초후에 자동으로 소멸되며 조회를해도 찾을 수 없다.  

<br/>

**6). 키와 값 삭제하기**  

```shell
127.0.0.1:6379> del test
```
키값을 입력해 만들어둔 키를 삭제할 수 있다.   
삭제가 완료되면 `(integer) 1`와 같이 표시된다.

<br/>

**7). 모든 키와 값 삭제하기**

```shell
127.0.0.1:6379> flushall
```
모든 키와 값을 삭제해주는 명령어다

<br/>

### 📌 Redis 여러개의 필드  

redis의 hashes 자료구조에는  
Key와 Field 용어가 나온다.  

```text
Hash Key1
     ᄂ Hash Sub Key1 (=Field1)
     ᄂ Hash Sub Key2 (=Field2)
```
와 같은 구조로 하나의 Key값에 여러가지  
Field를 저장을 할수 있다.  

<br/>  

**1). 하나의 키로 여러개 필드 저장**

```shell
127.0.0.1:6379> hset dhfif718@naver.com token "value1" like "10"
```
HSET 명령어를 이용할 수 있다.  

dhfif718@naver.com 라는 Key값에   

key : token / value : value1  
key : like / value : 10  
이라는 필드로 저장이 되어진 것이다.

어떻게 보면 dhfif718@naver.com Key의 필드는 token과 like가 되는 셈이다.  

<br/>

**2). 조회하는 방법**

```shell
127.0.0.1:6379> hget dhfif718@naver.com like
```

조회를할때 위와 같은 방법으로 조회하게되면  
10이라는 값이 출력되게 되어진다.  

만약 전체 필드를 조회하고 싶다면

```shell
127.0.0.1:6379> hgetall dhfif718@naver.com
```
hgetall을 key값과 입력하면 전체 조회가 된다.
```shell
1) "token"
2) "value1"
3) "like"
4) "10"
```
와 같은 방식으로 출력되어진다.   


<br/>

**3). 삭제하는 방법**  

마지막으로 삭제하는 방법이다.  

```shell
127.0.0.1:6379> hdel dhfif718@naver.com token like
```

여러개의 필드를 한번에 삭제할 수도 있고  
하나씩 지정해서 삭제할 수도 있다.




<br/>  
<br/>

✨ 참고 블로그   
[Redis 참고 레퍼런스 1], [Redis 참고 레퍼런스 2], [Redis Hash 참고 레퍼런스], [Redis 우분투 설치 레퍼런스]

<br/>
<br/>

[Redis 참고 레퍼런스 1]: https://freeblogger.tistory.com/10
[Redis 참고 레퍼런스 2]: https://wlswoo.tistory.com/44
[Redis 우분투 설치 레퍼런스]: https://server-talk.tistory.com/472  
[Redis Hash 참고 레퍼런스]: https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=wideeyed&logNo=221428664697
