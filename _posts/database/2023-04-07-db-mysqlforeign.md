---
layout: post
title: "MySQL Foreign key 설정하기"
subtitle: 
categories: DB
tags: [DB, MySQL]
comments: true
published: true
---

MySQL Foreign Key를 설정하는 방법을 적어보려한다.   
간단하게 아래와 같은 테이블을 준비했다.  

스키마 이름은 sqlpractice로 설정하였다.

![image](https://user-images.githubusercontent.com/95069395/230547251-d9b554a7-85a0-4430-9aa2-5b1f4548b431.png){: .align-left style="max-width: 100%"}

SQL문은 아래와 같이 작성하면 테이블이 생성되고


```roomsql
CREATE TABLE sqlpractice.posts (
    id BIGINT NOT NULL PRIMARY KEY,
    title VARCHAR(255) NOT NULL
);

CREATE TABLE sqlpractice.tag (
    id BIGINT NOT NULL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(255) NOT NULL
);

CREATE TABLE sqlpractice.posts_tag (
    id BIGINT NOT NULL PRIMARY KEY,
    posts_id BIGINT NOT NULL,
    tag_id BIGINT NOT NULL
);
```

<br/>   

FOREIGN KEY를 설정하기전에 관계를 살펴보자

1 : N : 1 관계의 테이블이고   
POSTS_TAG가 연관관계의 주인이며 POSTS의 PK를 FK로 가지고 있고   
TAG의 PK를 FK로 가지고 있는 상태이다.  

FOREIGN KEY 설정 문법은 아래와 같다
```roomsql
ALTER TABLE 테이블명 ADD 
FOREIGN KEY (컬럼명) REFERENCES 대상 테이블명 (컬럼명);
```

<br/>


위의 관계도처럼 POSTS_TAG 테이블에 FOREIGN KEY를 설정하려면 아래와 같이 입력하면된다.   

```roomsql
ALTER TABLE sqlpractice.posts_tag ADD 
FOREIGN KEY (posts_id) REFERENCES sqlpractice.posts(id);

ALTER TABLE sqlpractice.posts_tag ADD 
FOREIGN KEY (tag_id) REFERENCES sqlpractice.tag(id);
```

<br/>
<br/>
<br/>
<br/>