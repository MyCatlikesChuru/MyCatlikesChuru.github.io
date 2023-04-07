---
layout: post
title: "MySQL Join 연습하기"
subtitle: 
categories: DB
tags: [DB, MySQL]
comments: true
published: false
---


![image](https://user-images.githubusercontent.com/95069395/230547251-d9b554a7-85a0-4430-9aa2-5b1f4548b431.png){: .align-left style="max-width: 100%"}


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

ALTER TABLE sqlpractice.posts_tag ADD 
FOREIGN KEY (posts_id) REFERENCES sqlpractice.posts(id);

ALTER TABLE sqlpractice.posts_tag ADD 
FOREIGN KEY (tag_id) REFERENCES sqlpractice.tag(id);
```

```roomsql
INSERT INTO sqlpractice.posts (posts.id,posts.title) VALUES ('1','오늘 점심 뭐먹지?');
INSERT INTO sqlpractice.posts (posts.id,posts.title) VALUES ('2','서울 좋아요');
INSERT INTO sqlpractice.posts (posts.id,posts.title) VALUES ('3','카공하기 좋은 카페추천 모음');
INSERT INTO sqlpractice.posts (posts.id,posts.title) VALUES ('4','듣기 좋은 노래 추천');
```

<br/>
<br/>
<br/>
<br/>