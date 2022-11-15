---
layout: post
title: Spring) mysql 연동 하기
subtitle: yml 파일 설정
categories: Spring
tags: [Spring, mysql]
comments: true
published: false
---


![image](){: .align-left style="max-width: 100%"}

```
runtimeOnly 'mysql:mysql-connector-java'
```


```yaml
spring:
  jpa:
    hibernate:
      ddl-auto: create-drop  # (1) 스키마 자동 생성
    show-sql: true      # (2) SQL 쿼리 출력
    properties:
      hibernate:
        format_sql: true  # (3) SQL pretty print
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:(포트넘버)/(스키마 이름)?serverTimezone=Asia/Seoul # 포트:3306 , 스키마:example
    username:  # 유저네임
    password:  # 비밀번호
```