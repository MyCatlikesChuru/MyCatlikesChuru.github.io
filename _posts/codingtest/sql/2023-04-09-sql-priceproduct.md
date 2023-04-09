---
layout: post
title: "[Programmers] 가격대 별 상품 개수 구하기 (MySQL)"
subtitle: Level 2
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [[가격대 별 상품 개수 구하기]  

### 📖 문제 설명  

<p>다음은 어느 의류 쇼핑몰에서 판매중인 상품들의 정보를 담은 <code>PRODUCT</code> 테이블입니다. <code>PRODUCT</code> 테이블은 아래와 같은 구조로 되어있으며, <code>PRODUCT_ID</code>, <code>PRODUCT_CODE</code>, <code>PRICE</code>는 각각 상품 ID, 상품코드, 판매가를 나타냅니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>PRODUCT_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
<tr>
<td>PRODUCT_CODE</td>
<td>VARCHAR(8)</td>
<td>FALSE</td>
</tr>
<tr>
<td>PRICE</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<p>상품 별로 중복되지 않는 8자리 상품코드 값을 가지며 앞 2자리는 카테고리 코드를 나타냅니다.</p>

<hr>

<h5>문제</h5>

<p><code>PRODUCT</code> 테이블에서 만원 단위의 가격대 별로 상품 개수를 출력하는 SQL 문을 작성해주세요. 이때 컬럼명은 각각 컬럼명은 PRICE_GROUP, PRODUCTS로 지정해주시고 가격대 정보는 각 구간의 최소금액(10,000원 이상 ~ 20,000 미만인 구간인 경우 10,000)으로 표시해주세요. 결과는 가격대를 기준으로 오름차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p>예를 들어 <code>PRODUCT</code> 테이블이 다음과 같다면</p>
<table class="table">
        <thead><tr>
<th>PRODUCT_ID</th>
<th>PRODUCT_CODE</th>
<th>PRICE</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>A1000011</td>
<td>10000</td>
</tr>
<tr>
<td>2</td>
<td>A1000045</td>
<td>9000</td>
</tr>
<tr>
<td>3</td>
<td>C3000002</td>
<td>22000</td>
</tr>
<tr>
<td>4</td>
<td>C3000006</td>
<td>15000</td>
</tr>
<tr>
<td>5</td>
<td>C3000010</td>
<td>30000</td>
</tr>
<tr>
<td>6</td>
<td>K1000023</td>
<td>17000</td>
</tr>
</tbody>
      </table>
<p>만원 단위의 가격대 별로 상품을 나누면</p>

<ul>
<li>가격대가 0원 ~ 1만원 미만인 상품은 <code>PRODUCT_ID</code> 가 2인 상품 1개,</li>
<li>가격대가 1만원 이상 ~ 2만원 미만인 상품들은 <code>PRODUCT_ID</code> 가 1, 4, 6인 상품 3개,</li>
<li>가격대가 2만원 이상 ~ 3만원 미만인 상품은 <code>PRODUCT_ID</code> 가 3인 상품 1개,</li>
<li>가격대가 3만원 이상 ~ 4만원 미만인 상품은 <code>PRODUCT_ID</code> 가 5인 상품 1개,</li>
</ul>

<p>에 각각 해당하므로 다음과 같이 결과가 나와야 합니다.</p>
<table class="table">
        <thead><tr>
<th>PRICE_GROUP</th>
<th>PRODUCTS</th>
</tr>
</thead>
        <tbody><tr>
<td>0</td>
<td>1</td>
</tr>
<tr>
<td>10000</td>
<td>3</td>
</tr>
<tr>
<td>20000</td>
<td>1</td>
</tr>
<tr>
<td>30000</td>
<td>1</td>
</tr>
</tbody>
      </table>

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. CASE WHEN을 이용하여 가격별 집계를 할 수 있도록 만들어준다.
2. PRICE의 수량을 PRODUCTS 컬럼으로 묶어준다.
3. GROUP BY를 이용해 집계를 한다.
4. PRICE_GROUP 오름차순으로 정렬한다.

```RoomSql
SELECT CASE 
WHEN PRICE BETWEEN 0 AND 9999 THEN 0
WHEN PRICE BETWEEN 10000 AND 19999 THEN 10000
WHEN PRICE BETWEEN 20000 AND 29999 THEN 20000
WHEN PRICE BETWEEN 30000 AND 39999 THEN 30000
WHEN PRICE BETWEEN 40000 AND 49999 THEN 40000
WHEN PRICE BETWEEN 50000 AND 59999 THEN 50000
WHEN PRICE BETWEEN 60000 AND 69999 THEN 60000
WHEN PRICE BETWEEN 70000 AND 79999 THEN 70000
WHEN PRICE BETWEEN 80000 AND 89999 THEN 80000
END AS PRICE_GROUP, 
COUNT(PRICE) AS PRODUCTS
FROM PRODUCT
GROUP BY PRICE_GROUP
ORDER BY PRICE_GROUP ASC;
```

문제에서는 0~4만원까지 밖에 없어서 4만원까지 풀이했다가 왜 안풀리지 하고 헷갈렸다..   
`SELECT * FROM PRODUCT` 를 해보니 9만원까지 나와서 9만원까지 집계하였다.

<br/>  

또 다른 풀이 방법으로는   

```roomsql
SELECT  FLOOR(PRICE/10000)*10000 AS PRICE_GROUP,
        COUNT(*) AS PRODUCTS
  FROM  product
 GROUP 
    BY  PRICE_GROUP
 ORDER
    BY  PRICE_GROUP ASC
```
과 같이 소수점을 제거하는 함수인 `FLOOR`를 이용한 풀이를 할 수 있다.      
가격을 만원 단위로 나눠서 소수점을 버리고 만원씩 곱해주면    
CASE 문법보다 훨씬 깔끔하게 풀이가 가능하다.   

<br/>

[가격대 별 상품 개수 구하기]:https://school.programmers.co.kr/learn/courses/30/lessons/131530
