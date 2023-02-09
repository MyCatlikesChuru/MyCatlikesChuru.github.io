---
layout: post
title: MySQL replace 함수사용하여 일괄 변경하기
subtitle: 
categories: DB
tags: [DB, MySQL]
comments: true
published: true
---


예전에 작업했던 이미지 사진이 날라가버렸다... 구글 드라이브에 이미지를 넣어 주소를 뽑아서    
MySQL 테이블에 주소값으로 넣어두었는데 사진이 날라가버렸다...

![image](https://user-images.githubusercontent.com/95069395/217779406-6b06d791-ae7a-4f77-b89d-e68c3c63e8af.png){: .align-left style="max-width: 100%"}

OAuth2.0으로 회원가입을 진행한 회원들은 해당 벤더에서  
사진주소를 가져와서 DB에 저장하기때문에 남아있고  
일반으로 회원가입한 유저는 위에 12가지 기본이미지를 랜덤으로 배정받게되는데  
보는거처럼 사진 원본파일이 구글드라이브에서 날라가서 표시가안되는 모습이다.  

처음했던 프로젝트이기도하고 사진관리하는 방법이 너무 miss인 것 같다.   
우선 데이터베이스에 회원으로 가입되어있는 멤버가 100명정도 가입되어있는데   
해당 데이터를 조작하기위해서 Replace라는 함수를 이용해 일괄 변경하기로 정했다.


<br/>

### 📌 Replace 함수 사용

WorkBench 열어서 강제로 SQL을 만들어 변경할려고한다.

![image](https://user-images.githubusercontent.com/95069395/217779419-2cc32d98-7a95-4ebd-ad58-da1cadcf0395.png){: .align-left style="max-width: 100%"}

member라는 테이블에 image 컬럼의 데이터를 바꾸는 것이다.   
(❗️ 현재 이미지 주소 종류를 12가지로 한정지어놔서 가능한 작업이다.  
만약 종류가 각기달랐다면 다른방법을 알아봐야했을 것이다.)  

<br/>

```shell
SELECT REPLACE(image, 'https://기존이미지주소','https://변경이미지주소') FROM member;
UPDATE member SET member.image = REPLACE(image, 'https://기존이미지주소','https://변경이미지주소');
```
Table : member  
Column : image  

변경하고자 하는 데이터는 image의 Text값을 변경하는 것이다.  
`REPLACE('문자열','기존문자열','변경문자열')` 형식으로 쿼리를 날려주면 된다.     


```shell
SELECT REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOtBngM6igLV_5Gi86DYVCKWPOuuBgzUxZiiu2DZVYZdi1T_CpI2-xBeHbODJMwMymHxzTtCtLwlbTsvuCZj1dbg5fWkw=w1920-h921','https://user-images.githubusercontent.com/95069395/217765437-5eb0766a-139b-4d74-8ea7-f13fb511fb6b.png') FROM member;
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOtBngM6igLV_5Gi86DYVCKWPOuuBgzUxZiiu2DZVYZdi1T_CpI2-xBeHbODJMwMymHxzTtCtLwlbTsvuCZj1dbg5fWkw=w1920-h921','https://user-images.githubusercontent.com/95069395/217765437-5eb0766a-139b-4d74-8ea7-f13fb511fb6b.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOg7R_zu1d5ondnz2mF9Mfs5POsyaYwY0bpBfzQmTCA5iNpVhU3rycNjteK9RZKX0_dsQLXn1AGkA_MtfZJKeSJ03vOJg=w1000-h921','https://user-images.githubusercontent.com/95069395/217765442-ddf39075-30c9-45a1-9d55-806b35c9c991.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPE5xpI0-lxEqtHYRm-avwUL1X2y8HUz_CtZoT2afckhm77Y_hZfo5N7xB6VP2bDxsiIT4DcFRwe8wSc5-y9BFHYfhe=w1000-h921','https://user-images.githubusercontent.com/95069395/217765447-d8b3c96e-42d2-4b4d-9660-df536e82a48e.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPEZNW9ORtC6bMAw54VDZFQ3IaDPrGVWFKi18kERIrAxuE8N8MmbZhtol2Ujxvyrz21Ows7-0_4P8cz_sJlXYPvVu4z=w1920-h921','https://user-images.githubusercontent.com/95069395/217765450-c1e29534-8974-45c3-ba3d-0d98dc3ac4c7.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOUcWn-HIk4JoOiMBxVqMc458drW7579JOqVcfR3Nq7eHs2WZV1URKdSy7iAEnrteP-19yw4B7xAAphkiPKu4fAez8j3g=w1000-h921','https://user-images.githubusercontent.com/95069395/217765456-26f2ac03-91a6-4b4d-a14c-ab6275d49022.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gMY6Ju7pO_FZp5hyfYdjKljdMAFlS5nCtbnvfJfXXqG52kH_WvYSqNsi7ClGgYVgmK8Gav_XdM_tCc5liRtQD8bZq4Ryg=w1920-h921','https://user-images.githubusercontent.com/95069395/217765459-d95c0338-5ced-4689-9afb-61d917e0c4ad.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gP3vIemuwNCa2gLq8QFxJ0mvEUyH90NCzzCdEh6zkRFmQvF6_MHHo8N46VTTP58ovW3ikNSXN281NhTcbDaBfESUl6eUw=w1000-h921','https://user-images.githubusercontent.com/95069395/217765462-8b65713a-f436-405e-812d-c7b906bcb8e8.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gMzNhh9XTiXBHg_K4sHjKxhNVZTpihvtXFyGa_pR2L3j2ymHy6w9uDu5Pqmjo_-PPl40kIJENWVqGGfVE7-o1vMxMS0Zg=w1000-h921','https://user-images.githubusercontent.com/95069395/217765465-83f95069-d4be-4418-a7ee-f569b235f665.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOnvAfzDjdq54wfaQ3aQnYMNbfCsFwZVvyBIxEzKYw7LUtT7SnlxkIyZnkXyxrN2y8N5CuXVCLdNdloEMYYl7PPTBotWA=w1000-h921','https://user-images.githubusercontent.com/95069395/217765469-2a34827e-f18c-4bca-aa49-ab3c698c46ce.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOeqDCgTYyD4_NlylK0EE-y1Fn0lj364mopuRJbuN4JvWP2eQGBJt6Ygv48nrlnHiuiq2Dkgz3ep7XILzUgT_lVAN1VpA=w1000-h921','https://user-images.githubusercontent.com/95069395/217765473-c7044900-f16b-407c-a27e-939c7c8a2e17.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPKgEG4sHNVV90PQrE5jn37skBOV5Qp7M3czg2sShhPYx46ybBb0KhQ37mcxGl6H6E_1xVdAhna7aAJGi8qp1oYGCWMMw=w1000-h921','https://user-images.githubusercontent.com/95069395/217765476-31afe366-b1d2-4ee8-92f4-7d254a7616d2.png');
UPDATE member SET member.image = REPLACE(image, 'https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gNXn3Qy3cjHid6lgXk6cT9J78rm98pYCgumlTWAHENmA_Q3ujH3MxEbMBYp_lHBLM8rpIU51BtZnzaIKi3CiEsrqcDKQA=w1000-h921','https://user-images.githubusercontent.com/95069395/217765478-e9cbd679-d241-4a42-b285-f334b0b22241.png');
```

쿼리를 만들고 테이블에 정상적으로 변경되는지 확인까지 완료!  

![image](https://user-images.githubusercontent.com/95069395/217785372-71da8902-27fb-42ed-97ee-d171e916a983.png){: .align-left style="max-width: 100%"}

다시 사이트가 제대로된 이미지로 표현되는 것을 볼 수 있다.   
쿼리 실행시 1175 에러가 발생한다면 -> [Update시 에러코드 1175 처리]블로그를 참고해보자

<br/>  

이렇게 테이블의 컬럼자체 내용을 바꿔끼워야할 경우에  
REPLACE를 사용하는 것보단 Table Data export를 사용해 csv파일에서   
일괄변경하여 수정한다음 import해서 데이터를 갈아끼워주는것이 훨씬더 효율적인 것 같다.


<br/>
<br/>
<br/>


✨ 참고 블로그
[Mysql Replace 함수 사용법 레퍼런스]

<br/>
<br/>

[Mysql Replace 함수 사용법 레퍼런스]: https://leenow.tistory.com/13
[Update시 에러코드 1175 처리]: https://jwgye.tistory.com/31
