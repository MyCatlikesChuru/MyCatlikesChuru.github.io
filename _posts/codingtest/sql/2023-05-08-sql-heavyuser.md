---
layout: post
title: "[Programmers] 헤비 유저가 소유한 장소 (MySQL)"
subtitle: Level 3
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [헤비 유저가 소유한 장소]

### 📖 문제 설명  

<p><code>PLACES</code> 테이블은 공간 임대 서비스에 등록된 공간의 정보를 담은 테이블입니다. <code>PLACES</code> 테이블의 구조는 다음과 같으며 <code>ID</code>, <code>NAME</code>, <code>HOST_ID</code>는 각각 공간의 아이디, 이름, 공간을 소유한 유저의 아이디를 나타냅니다. <code>ID</code>는 기본키입니다.</p>
<table class="table">
        <thead><tr>
<th>NAME</th>
<th>TYPE</th>
</tr>
</thead>
        <tbody><tr>
<td>ID</td>
<td>INT</td>
</tr>
<tr>
<td>NAME</td>
<td>VARCHAR</td>
</tr>
<tr>
<td>HOST_ID</td>
<td>INT</td>
</tr>
</tbody>
      </table>
<h5>문제</h5>

<p>이 서비스에서는 공간을 둘 이상 등록한 사람을 "헤비 유저"라고 부릅니다. 헤비 유저가 등록한 공간의 정보를 아이디 순으로 조회하는 SQL문을 작성해주세요.</p>

<h5>예시</h5>

<p>예를 들어, <code>PLACES</code> 테이블이 다음과 같다면</p>
<table class="table">
        <thead><tr>
<th>ID</th>
<th>NAME</th>
<th>HOST_ID</th>
</tr>
</thead>
        <tbody><tr>
<td>4431977</td>
<td>BOUTIQUE STAYS - Somerset Terrace, Pet Friendly</td>
<td>760849</td>
</tr>
<tr>
<td>5194998</td>
<td>BOUTIQUE STAYS - Elwood Beaches 3, Pet Friendly</td>
<td>760849</td>
</tr>
<tr>
<td>16045624</td>
<td>Urban Jungle in the Heart of Melbourne</td>
<td>30900122</td>
</tr>
<tr>
<td>17810814</td>
<td>Stylish Bayside Retreat with a Luscious Garden</td>
<td>760849</td>
</tr>
<tr>
<td>22740286</td>
<td>FREE PARKING - The Velvet Lux in Melbourne CBD</td>
<td>30900122</td>
</tr>
<tr>
<td>22868779</td>
<td>★ Fresh Fitzroy Pad with City Views! ★</td>
<td>21058208</td>
</tr>
</tbody>
      </table>
<ul>
<li>760849번 유저는 공간을 3개 등록했으므로 이 유저는 헤비유저입니다. </li>
<li>30900122번 유저는 공간을 2개 등록했으므로 이 유저는 헤비유저입니다.</li>
<li>21058208번 유저는 공간을 1개 등록했으므로 이 유저는 헤비유저가 아닙니다.</li>
</ul>

<p>따라서 SQL 문을 실행하면 다음과 같이 나와야 합니다.</p>
<table class="table">
        <thead><tr>
<th>ID</th>
<th>NAME</th>
<th>HOST_ID</th>
</tr>
</thead>
        <tbody><tr>
<td>4431977</td>
<td>BOUTIQUE STAYS - Somerset Terrace, Pet Friendly</td>
<td>760849</td>
</tr>
<tr>
<td>5194998</td>
<td>BOUTIQUE STAYS - Elwood Beaches 3, Pet Friendly</td>
<td>760849</td>
</tr>
<tr>
<td>16045624</td>
<td>Urban Jungle in the Heart of Melbourne</td>
<td>30900122</td>
</tr>
<tr>
<td>17810814</td>
<td>Stylish Bayside Retreat with a Luscious Garden</td>
<td>760849</td>
</tr>
<tr>
<td>22740286</td>
<td>FREE PARKING - The Velvet Lux in Melbourne CBD</td>
<td>30900122</td>
</tr>
</tbody>
      </table>

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. HOST_ID가 2개 이상인 데이터들만 추출하면되는 간단한 문제이다.
2. SUB 쿼리를 이용하여 HOST_ID를 GROUP BY하여 일치한 값만 추출한다.
3. ID 기준으로 오름차순 정렬

```roomsql
SELECT *
FROM PLACES
WHERE HOST_ID IN (
    SELECT HOST_ID
    FROM PLACES
    GROUP BY HOST_ID
    HAVING COUNT(HOST_ID) >= 2
)
ORDER BY ID ASC;
```





<br/>

[헤비 유저가 소유한 장소]:https://school.programmers.co.kr/learn/courses/30/lessons/77487
