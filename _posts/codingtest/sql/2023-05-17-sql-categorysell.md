---
layout: post
title: "[Programmers] 저자 별 카테고리 별 매출액 집계하기 (MySQL)"
subtitle: Level 4
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [저자 별 카테고리 별 매출액 집계하기]

### 📖 문제 설명  

<p>다음은 어느 한 서점에서 판매중인 도서들의 도서 정보(<code>BOOK</code>), 저자 정보(<code>AUTHOR</code>) 테이블입니다.</p>

<p><code>BOOK</code> 테이블은 각 도서의 정보를 담은 테이블로 아래와 같은 구조로 되어있습니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
<th>Description</th>
</tr>
</thead>
        <tbody><tr>
<td>BOOK_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
<td>도서 ID</td>
</tr>
<tr>
<td>CATEGORY</td>
<td>VARCHAR(N)</td>
<td>FALSE</td>
<td>카테고리 (경제, 인문, 소설, 생활, 기술)</td>
</tr>
<tr>
<td>AUTHOR_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
<td>저자 ID</td>
</tr>
<tr>
<td>PRICE</td>
<td>INTEGER</td>
<td>FALSE</td>
<td>판매가 (원)</td>
</tr>
<tr>
<td>PUBLISHED_DATE</td>
<td>DATE</td>
<td>FALSE</td>
<td>출판일</td>
</tr>
</tbody>
      </table>
<p><code>AUTHOR</code> 테이블은 도서의 저자의 정보를 담은 테이블로 아래와 같은 구조로 되어있습니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
<th>Description</th>
</tr>
</thead>
        <tbody><tr>
<td>AUTHOR_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
<td>저자 ID</td>
</tr>
<tr>
<td>AUTHOR_NAME</td>
<td>VARCHAR(N)</td>
<td>FALSE</td>
<td>저자명</td>
</tr>
</tbody>
      </table>
<p><code>BOOK_SALES</code> 테이블은 각 도서의 날짜 별 판매량 정보를 담은 테이블로 아래와 같은 구조로 되어있습니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
<th>Description</th>
</tr>
</thead>
        <tbody><tr>
<td>BOOK_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
<td>도서 ID</td>
</tr>
<tr>
<td>SALES_DATE</td>
<td>DATE</td>
<td>FALSE</td>
<td>판매일</td>
</tr>
<tr>
<td>SALES</td>
<td>INTEGER</td>
<td>FALSE</td>
<td>판매량</td>
</tr>
</tbody>
      </table>
<hr>

<h5>문제</h5>

<p><code>2022년 1월</code>의 도서 판매 데이터를 기준으로 저자 별, 카테고리 별 매출액(<code>TOTAL_SALES = 판매량 * 판매가</code>) 을 구하여, 저자 ID(<code>AUTHOR_ID</code>), 저자명(<code>AUTHOR_NAME</code>), 카테고리(<code>CATEGORY</code>), 매출액(<code>SALES</code>) 리스트를 출력하는 SQL문을 작성해주세요. <br>
결과는 저자 ID를 오름차순으로, 저자 ID가 같다면 카테고리를 내림차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p>예를 들어 <code>BOOK</code> 테이블과 <code>AUTHOR</code> 테이블, <code>BOOK_SALES</code> 테이블이 다음과 같다면</p>
<table class="table">
        <thead><tr>
<th>BOOK_ID</th>
<th>CATEGORY</th>
<th>AUTHOR_ID</th>
<th>PRICE</th>
<th>PUBLISHED_DATE</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>인문</td>
<td>1</td>
<td>10000</td>
<td>2020-01-01</td>
</tr>
<tr>
<td>2</td>
<td>경제</td>
<td>1</td>
<td>9000</td>
<td>2021-02-05</td>
</tr>
<tr>
<td>3</td>
<td>경제</td>
<td>2</td>
<td>9000</td>
<td>2021-03-11</td>
</tr>
</tbody>
      </table><table class="table">
        <thead><tr>
<th>AUTHOR_ID</th>
<th>AUTHOR_NAME</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>홍길동</td>
</tr>
<tr>
<td>2</td>
<td>김영호</td>
</tr>
</tbody>
      </table><table class="table">
        <thead><tr>
<th>BOOK_ID</th>
<th>SALES_DATE</th>
<th>SALES</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>2022-01-01</td>
<td>2</td>
</tr>
<tr>
<td>2</td>
<td>2022-01-02</td>
<td>3</td>
</tr>
<tr>
<td>1</td>
<td>2022-01-05</td>
<td>1</td>
</tr>
<tr>
<td>2</td>
<td>2022-01-20</td>
<td>5</td>
</tr>
<tr>
<td>2</td>
<td>2022-01-21</td>
<td>6</td>
</tr>
<tr>
<td>3</td>
<td>2022-01-22</td>
<td>2</td>
</tr>
<tr>
<td>2</td>
<td>2022-02-11</td>
<td>3</td>
</tr>
</tbody>
      </table>
<p>2022년 1월의 도서 별 총 매출액은 도서 ID 가 1 인 도서가 총 3권 * 10,000원 = 30,000원, 도서 ID 가 2 인 도서가 총 14권 * 9,000 = 126,000원 이고, 도서 ID 가 3 인 도서가 총 2권 * 9,000 = 18,000원 입니다.</p>

<p>저자 별 카테고리 별로 매출액을 집계하면 결과는 다음과 같습니다.</p>
<table class="table">
        <thead><tr>
<th>AUTHOR_ID</th>
<th>AUTHOR_NAME</th>
<th>CATEGORY</th>
<th>TOTAL_SALES</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>홍길동</td>
<td>인문</td>
<td>30000</td>
</tr>
<tr>
<td>1</td>
<td>홍길동</td>
<td>경제</td>
<td>126000</td>
</tr>
<tr>
<td>2</td>
<td>김영호</td>
<td>경제</td>
<td>18000</td>
</tr>
</tbody>
      </table>
<p>그리고 저자 ID, 카테고리 순으로 내림차순 정렬하면 다음과 같이 나와야 합니다.</p>
<table class="table">
        <thead><tr>
<th>AUTHOR_ID</th>
<th>AUTHOR_NAME</th>
<th>CATEGORY</th>
<th>TOTAL_SALES</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>홍길동</td>
<td>인문</td>
<td>30000</td>
</tr>
<tr>
<td>1</td>
<td>홍길동</td>
<td>경제</td>
<td>126000</td>
</tr>
<tr>
<td>2</td>
<td>김영호</td>
<td>경제</td>
<td>18000</td>
</tr>
</tbody>
      </table>

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. 1월에 판매한 BOOK_ID 기준으로 판매수량의 총합을 테이블을 가상으로 만든다 (TOTAL_SALES)
2. BOOK 테이블과 AUTHOR 테이블 그리고 가상으로만든 TOTAL_SALES 테이블을 JOIN한다.
3. 카테고리와 저자이름을 기준으로 GROUP BY를한다. 
4. TOTAL_SALES 컬럼은 SUM함수로 그룹핑할때 합산을 저장해준다.
5. 판매수량 총합 * 가격 = 총 가격이지만 여기에 4번을 수행하기 때문에 SUM을 붙여줘야한다.
6. 정렬은 AUTHOR_ID를 기준으로 오름차순 같으면 카테고리별로 내림차순한다.

```roomsql

WITH TOTAL_SALES AS (
    SELECT 
        BOOK_ID,
        SUM(SALES) AS SALES
    FROM BOOK_SALES
    WHERE SALES_DATE LIKE '2022-01%'
    GROUP BY BOOK_ID
)

SELECT 
    B.AUTHOR_ID,
    A.AUTHOR_NAME,
    B.CATEGORY,
    SUM((TS.SALES * B.PRICE)) AS TOTAL_SALES
FROM BOOK B
LEFT JOIN AUTHOR A
ON B.AUTHOR_ID = A.AUTHOR_ID
LEFT JOIN TOTAL_SALES TS
ON B.BOOK_ID = TS.BOOK_ID
GROUP BY B.CATEGORY, A.AUTHOR_NAME
ORDER BY B.AUTHOR_ID ASC, B.CATEGORY DESC;
```

<br/>   


WITH 명령어를 사용하지 않고    
쿼리작성은 아래와 같이 해볼 수 있다.

```roomsql
SELECT 
    B.AUTHOR_ID,
    A.AUTHOR_NAME,
    B.CATEGORY,
    SUM((SALES * B.PRICE)) AS TOTAL_SALES
FROM BOOK B
LEFT JOIN AUTHOR A
ON B.AUTHOR_ID = A.AUTHOR_ID
LEFT JOIN (    
    SELECT 
        BOOK_ID,
        SUM(SALES) AS SALES
    FROM BOOK_SALES
    WHERE SALES_DATE LIKE '2022-01%'
    GROUP BY BOOK_ID ) TS
ON B.BOOK_ID = TS.BOOK_ID
GROUP BY B.CATEGORY, A.AUTHOR_NAME
ORDER BY B.AUTHOR_ID ASC, B.CATEGORY DESC;
```

JOIN 절에 WITH 명령어에서 만든 가상 테이블 쿼리가   
그대로 들어가있는 모습이다. 

<br/>

[저자 별 카테고리 별 매출액 집계하기]:https://school.programmers.co.kr/learn/courses/30/lessons/144856
