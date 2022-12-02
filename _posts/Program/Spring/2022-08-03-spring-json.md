---
layout: post
title: Spring) @JsonIgnore 사용하기
subtitle:
categories: Spring
tags: [Spring]
comments: true
published: true
---

@JsonIgone은 리턴할때 해당 Entity의 값을 숨겨주는 기능이다.  
DB에는 값이저장되지만, Json 반환을할떄 표시되지 않는다.


![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPAJiEDaYwKoZMK1eaSEBGvTEIcZsun1caE8_Z434L8d1W5TF3LZPzP1FhVIym3-K1fc9LtF6ODySMsI4xlmYL2bVD6hA=w1185-h921){: .align-left style="max-width: 100%"}


위의 값들은  
userId, username, password, nickname, activated 이렇게 5개의 값이  
DB column으로 표시되어야한다.

<br/>

하나의 api를 만들어 Json으로 전부 return하게 테스트 해보았다.  
Postman으로 api실행 요청을 보내고 반환 받은 객체다.  
위의 @JsonIgnore 어노테이션을 붙인 activated만 나오지 않는 모습을 볼 수 있다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOLVoBdyZupIGOosrdUrQsIdJpI9QjSMljsC-xeDrA_Ig8x3cTAP2UqQ08OEZaFm2mFfiGqeKJZiJ2LxofTCHdQlUcw9Q=w1185-h921){: .align-left style="max-width: 100%"}