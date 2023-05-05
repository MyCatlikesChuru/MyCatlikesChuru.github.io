---
layout: post
title: "[Programmers] 즐겨찾기가 가장 많은 식당 정보 출력하기 (MySQL)"
subtitle: Level 3
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [즐겨찾기가 가장 많은 식당 정보 출력하기]

### 📖 문제 설명  

<p>다음은 식당의 정보를 담은 <code>REST_INFO</code> 테이블입니다. <code>REST_INFO</code> 테이블은 다음과 같으며 <code>REST_ID</code>, <code>REST_NAME</code>, <code>FOOD_TYPE</code>, <code>VIEWS</code>, <code>FAVORITES</code>, <code>PARKING_LOT</code>, <code>ADDRESS</code>, <code>TEL</code>은 식당 ID, 식당 이름, 음식 종류, 조회수, 즐겨찾기수, 주차장 유무, 주소, 전화번호를 의미합니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>REST_ID</td>
<td>VARCHAR(5)</td>
<td>FALSE</td>
</tr>
<tr>
<td>REST_NAME</td>
<td>VARCHAR(50)</td>
<td>FALSE</td>
</tr>
<tr>
<td>FOOD_TYPE</td>
<td>VARCHAR(20)</td>
<td>TRUE</td>
</tr>
<tr>
<td>VIEWS</td>
<td>NUMBER</td>
<td>TRUE</td>
</tr>
<tr>
<td>FAVORITES</td>
<td>NUMBER</td>
<td>TRUE</td>
</tr>
<tr>
<td>PARKING_LOT</td>
<td>VARCHAR(1)</td>
<td>TRUE</td>
</tr>
<tr>
<td>ADDRESS</td>
<td>VARCHAR(100)</td>
<td>TRUE</td>
</tr>
<tr>
<td>TEL</td>
<td>VARCHAR(100)</td>
<td>TRUE</td>
</tr>
</tbody>
      </table>
<hr>

<h5>문제</h5>

<p><code>REST_INFO</code> 테이블에서 음식종류별로 즐겨찾기수가 가장 많은 식당의 음식 종류, ID, 식당 이름, 즐겨찾기수를 조회하는 SQL문을 작성해주세요. 이때 결과는 음식 종류를 기준으로 내림차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p><code>REST_INFO</code> 테이블이 다음과 같을 때</p>
<table class="table">
        <thead><tr>
<th>REST_ID</th>
<th>REST_NAME</th>
<th>FOOD_TYPE</th>
<th>VIEWS</th>
<th>FAVORITES</th>
<th>PARKING_LOT</th>
<th>ADDRESS</th>
<th>TEL</th>
</tr>
</thead>
        <tbody><tr>
<td>00001</td>
<td>은돼지식당</td>
<td>한식</td>
<td>1150345</td>
<td>734</td>
<td>N</td>
<td>서울특별시 중구 다산로 149</td>
<td>010-4484-8751</td>
</tr>
<tr>
<td>00002</td>
<td>하이가쯔네</td>
<td>일식</td>
<td>120034</td>
<td>112</td>
<td>N</td>
<td>서울시 중구 신당동 375-21</td>
<td>NULL</td>
</tr>
<tr>
<td>00003</td>
<td>따띠따띠뜨</td>
<td>양식</td>
<td>1234023</td>
<td>102</td>
<td>N</td>
<td>서울시 강남구 신사동 627-3 1F</td>
<td>02-6397-1023</td>
</tr>
<tr>
<td>00004</td>
<td>스시사카우스</td>
<td>일식</td>
<td>1522074</td>
<td>230</td>
<td>N</td>
<td>서울시 서울시 강남구 신사동 627-27</td>
<td>010-9394-2554</td>
</tr>
<tr>
<td>00005</td>
<td>코슌스</td>
<td>일식</td>
<td>15301</td>
<td>123</td>
<td>N</td>
<td>서울특별시 강남구 언주로153길</td>
<td>010-1315-8729</td>
</tr>
</tbody>
      </table>
<p>SQL을 실행하면 다음과 같이 출력되어야 합니다.</p>
<table class="table">
        <thead><tr>
<th>FOOD_TYPE</th>
<th>REST_ID</th>
<th>REST_NAME</th>
<th>FAVORITES</th>
</tr>
</thead>
        <tbody><tr>
<td>한식</td>
<td>00001</td>
<td>은돼지식당</td>
<td>734</td>
</tr>
<tr>
<td>일식</td>
<td>00004</td>
<td>스시사카우스</td>
<td>230</td>
</tr>
<tr>
<td>양식</td>
<td>00003</td>
<td>따띠따띠뜨</td>
<td>102</td>
</tr>
</tbody>
      </table>

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. 가장 많은 즐겨찾기를 받은 음식종류를 찾기위해 SUB쿼리를 이용
2. 음식종류로 GROUP BY
3. 음식종류로 내림차순 진행

```roomsql
SELECT FOOD_TYPE,
    REST_ID,
    REST_NAME,
    FAVORITES
FROM REST_INFO
WHERE FAVORITES IN (SELECT MAX(FAVORITES)
                    FROM REST_INFO
                    GROUP BY FOOD_TYPE)
GROUP BY FOOD_TYPE
ORDER BY FOOD_TYPE DESC;
```


여기서 조금 헷갈려하는 부분이 있어 참고차 적어본다.   
우선 위에 코드에서는 즐겨찾기 수량을 SUB쿼리를 이용해   
최대값을 가진 음식종류의 행과 일치하는 즐겨찾기를 찾는 방법이다.   

<br/>   

아래와 같은 방식으로 푼 사람도 있을 것 같다.   

**오답**

```roomsql
SELECT FOOD_TYPE, 
    REST_ID, 
    REST_NAME, 
    MAX(FAVORITES) 
FROM REST_INFO
GROUP BY FOOD_TYPE
ORDER BY FOOD_TYPE DESC
```
위의 코드를 실행하면 결과는 동일하게 나오는 것 처럼 보일 것이다.   
해당처럼 실행하면 각 그룹의 FAVORITES 최댓값을 가져온다.   
하지만 행(튜플) 전체를 가져오진 않아서 첫 번째 행에 각 그룹의   
FAVORITES 최댓값만 덮어써진 형태이게된다.


<br/>

[즐겨찾기가 가장 많은 식당 정보 출력하기]:https://school.programmers.co.kr/learn/courses/30/lessons/131123
