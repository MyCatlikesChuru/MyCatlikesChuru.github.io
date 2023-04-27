---
layout: post
title: "[Programmers] 조회수가 가장 많은 중고거래 게시판의 첨부파일 조회하기 (MySQL)"
subtitle: Level 3
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [조회수가 가장 많은 중고거래 게시판의 첨부파일 조회하기]

### 📖 문제 설명  

<p>다음은 중고거래 게시판 정보를 담은 <code>USED_GOODS_BOARD</code> 테이블과 중고거래 게시판 첨부파일 정보를 담은 <code>USED_GOODS_FILE</code> 테이블입니다. <code>USED_GOODS_BOARD</code> 테이블은 다음과 같으며 <code>BOARD_ID</code>, <code>WRITER_ID</code>, <code>TITLE</code>, <code>CONTENTS</code>, <code>PRICE</code>, <code>CREATED_DATE</code>, <code>STATUS</code>, <code>VIEWS</code>은 게시글 ID, 작성자 ID, 게시글 제목, 게시글 내용, 가격, 작성일, 거래상태, 조회수를 의미합니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>BOARD_ID</td>
<td>VARCHAR(5)</td>
<td>FALSE</td>
</tr>
<tr>
<td>WRITER_ID</td>
<td>VARCHAR(50)</td>
<td>FALSE</td>
</tr>
<tr>
<td>TITLE</td>
<td>VARCHAR(100)</td>
<td>FALSE</td>
</tr>
<tr>
<td>CONTENTS</td>
<td>VARCHAR(1000)</td>
<td>FALSE</td>
</tr>
<tr>
<td>PRICE</td>
<td>NUMBER</td>
<td>FALSE</td>
</tr>
<tr>
<td>CREATED_DATE</td>
<td>DATE</td>
<td>FALSE</td>
</tr>
<tr>
<td>STATUS</td>
<td>VARCHAR(10)</td>
<td>FALSE</td>
</tr>
<tr>
<td>VIEWS</td>
<td>NUMBER</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<p><code>USED_GOODS_FILE</code> 테이블은 다음과 같으며 <code>FILE_ID</code>, <code>FILE_EXT</code>, <code>FILE_NAME</code>, <code>BOARD_ID</code>는 각각 파일 ID, 파일 확장자, 파일 이름, 게시글 ID를 의미합니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>FILE_ID</td>
<td>VARCHAR(10)</td>
<td>FALSE</td>
</tr>
<tr>
<td>FILE_EXT</td>
<td>VARCHAR(5)</td>
<td>FALSE</td>
</tr>
<tr>
<td>FILE_NAME</td>
<td>VARCHAR(256)</td>
<td>FALSE</td>
</tr>
<tr>
<td>BOARD_ID</td>
<td>VARCHAR(10)</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<hr>

<h5>문제</h5>

<p><code>USED_GOODS_BOARD</code>와 <code>USED_GOODS_FILE</code> 테이블에서 조회수가 가장 높은 중고거래 게시물에 대한 첨부파일 경로를 조회하는  SQL문을 작성해주세요. 첨부파일 경로는 FILE ID를 기준으로 내림차순 정렬해주세요. 기본적인 파일경로는 /home/grep/src/ 이며, 게시글 ID를 기준으로 디렉토리가 구분되고, 파일이름은 파일 ID, 파일 이름, 파일 확장자로 구성되도록 출력해주세요. 조회수가 가장 높은 게시물은 하나만 존재합니다.</p>

<hr>

<h5>예시</h5>

<p><code>USED_GOODS_BOARD</code> 테이블이 다음과 같고</p>
<table class="table">
        <thead><tr>
<th>BOARD_ID</th>
<th>WRITER_ID</th>
<th>TITLE</th>
<th>CONTENTS</th>
<th>PRICE</th>
<th>CREATED_DATE</th>
<th>STATUS</th>
<th>VIEWS</th>
</tr>
</thead>
        <tbody><tr>
<td>B0001</td>
<td>kwag98</td>
<td>반려견 배변패드 팝니다</td>
<td>정말 저렴히 판매합니다. 전부 미개봉 새상품입니다.</td>
<td>12000</td>
<td>2022-10-01</td>
<td>DONE</td>
<td>250</td>
</tr>
<tr>
<td>B0002</td>
<td>lee871201</td>
<td>국내산 볶음참깨</td>
<td>직접 농사지은 참깨입니다.</td>
<td>3000</td>
<td>2022-10-02</td>
<td>DONE</td>
<td>121</td>
</tr>
<tr>
<td>B0003</td>
<td>goung12</td>
<td>배드민턴 라켓</td>
<td>사놓고 방치만 해서 팝니다.</td>
<td>9000</td>
<td>2022-10-02</td>
<td>SALE</td>
<td>212</td>
</tr>
<tr>
<td>B0004</td>
<td>keel1990</td>
<td>디올 귀걸이</td>
<td>신세계강남점에서 구입. 정품 아닐시 백퍼센트 환불</td>
<td>130000</td>
<td>2022-10-02</td>
<td>SALE</td>
<td>199</td>
</tr>
<tr>
<td>B0005</td>
<td>haphli01</td>
<td>스팸클래식 팔아요</td>
<td>유통기한 2025년까지에요</td>
<td>10000</td>
<td>2022-10-02</td>
<td>SALE</td>
<td>121</td>
</tr>
</tbody>
      </table>
<p><code>USED_GOODS_FILE</code> 테이블이 다음과 같을 때</p>
<table class="table">
        <thead><tr>
<th>FILE_ID</th>
<th>FILE_EXT</th>
<th>FILE_NAME</th>
<th>BOARD_ID</th>
</tr>
</thead>
        <tbody><tr>
<td>IMG_000001</td>
<td>.jpg</td>
<td>photo1</td>
<td>B0001</td>
</tr>
<tr>
<td>IMG_000002</td>
<td>.jpg</td>
<td>photo2</td>
<td>B0001</td>
</tr>
<tr>
<td>IMG_000003</td>
<td>.png</td>
<td>사진</td>
<td>B0002</td>
</tr>
<tr>
<td>IMG_000004</td>
<td>.jpg</td>
<td>사진</td>
<td>B0003</td>
</tr>
<tr>
<td>IMG_000005</td>
<td>.jpg</td>
<td>photo</td>
<td>B0004</td>
</tr>
</tbody>
      </table>
<p>SQL을 실행하면 다음과 같이 출력되어야 합니다.</p>
<table class="table">
        <thead><tr>
<th>FILE_PATH</th>
</tr>
</thead>
        <tbody><tr>
<td>/home/grep/src/B0001/IMG_000001photo1.jpg</td>
</tr>
<tr>
<td>/home/grep/src/B0001/IMG_000002photo2.jpg</td>
</tr>
</tbody>
      </table>

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. CONCAT을 이용한 문자열 만들기 -> FILE_PATH 컬럼을 반환할때 필요
2. LEFT JOIN을 BOARD_ID로하여 FILE 테이블에 접근
3. WHERE절과 Sub쿼리를 이용해 가장 높은값의 데이터를 추출
4. FILE_ID로 내림차순 정렬

```roomsql
SELECT CONCAT('/home','/grep','/src','/',b.BOARD_ID,'/',f.FILE_ID,f.FILE_NAME,f.FILE_EXT) 
    AS FILE_PATH
    FROM USED_GOODS_FILE f
LEFT JOIN USED_GOODS_BOARD b
ON f.BOARD_ID = b.BOARD_ID
WHERE b.VIEWS = (SELECT MAX(VIEWS) FROM USED_GOODS_BOARD)
ORDER BY FILE_ID DESC;
```

문제가 언뜻보면 어려워보일 수 있으나 해결방법은 상당히 간단하다.   

우선 `/home/grep/src/B0001/IMG_000001photo1.jpg`문자열을 만들기위해   
String값과 FILE 테이블의 컬럼의 값과 잘 조합해야한다.   

조합하기 위해선 당연히 FILE 테이블을 참조해야하기 때문에 JOIN을 해야하고   
가장 큰값을 찾기위해서 서브쿼리를 이용해 값을 추출하고   
WHERE절로 가장큰값과 같은값을 비교해서 필터링해주면 정답이된다.   


<br/>

[조회수가 가장 많은 중고거래 게시판의 첨부파일 조회하기]:https://school.programmers.co.kr/learn/courses/30/lessons/164671
