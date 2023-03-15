---
layout: post
title: "[Programmers] 자동차 대여 기록에서 장기/단기 대여 구분하기 (MySQL)"
subtitle: Level 1
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [자동차 대여 기록에서 장기/단기 대여 구분하기] 

### 📖 문제 설명  

<p>다음은 어느 자동차 대여 회사의 자동차 대여 기록 정보를 담은 <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블입니다. <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블은 아래와 같은 구조로 되어있으며, <code>HISTORY_ID</code>, <code>CAR_ID</code>, <code>START_DATE</code>, <code>END_DATE</code> 는 각각 자동차 대여 기록 ID, 자동차 ID, 대여 시작일, 대여 종료일을 나타냅니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>HISTORY_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
<tr>
<td>CAR_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
<tr>
<td>START_DATE</td>
<td>DATE</td>
<td>FALSE</td>
</tr>
<tr>
<td>END_DATE</td>
<td>DATE</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<hr>

<h5>문제</h5>

<p><code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블에서 대여 시작일이 2022년 9월에 속하는 대여 기록에 대해서 대여 기간이 30일 이상이면 '장기 대여' 그렇지 않으면 '단기 대여' 로 표시하는 컬럼(컬럼명: <code>RENT_TYPE</code>)을 추가하여 대여기록을 출력하는 SQL문을 작성해주세요. 결과는 대여 기록 ID를 기준으로 내림차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p>예를 들어 <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블이 다음과 같다면</p>
<table class="table">
        <thead><tr>
<th>HISTORY_ID</th>
<th>CAR_ID</th>
<th>START_DATE</th>
<th>END_DATE</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>4</td>
<td>2022-09-27</td>
<td>2022-11-27</td>
</tr>
<tr>
<td>2</td>
<td>3</td>
<td>2022-10-03</td>
<td>2022-11-04</td>
</tr>
<tr>
<td>3</td>
<td>2</td>
<td>2022-09-05</td>
<td>2022-09-05</td>
</tr>
<tr>
<td>4</td>
<td>1</td>
<td>2022-09-01</td>
<td>2022-09-30</td>
</tr>
<tr>
<td>5</td>
<td>3</td>
<td>2022-09-16</td>
<td>2022-10-15</td>
</tr>
</tbody>
      </table>
<p>2022년 9월의 대여 기록 중 '장기 대여' 에 해당하는 기록은 대여 기록 ID가 1, 4인 기록이고, '단기 대여' 에 해당하는 기록은 대여 기록 ID가 3, 5 인 기록이므로 대여 기록 ID를 기준으로 내림차순 정렬하면 다음과 같이 나와야 합니다.</p>
<table class="table">
        <thead><tr>
<th>HISTORY_ID</th>
<th>CAR_ID</th>
<th>START_DATE</th>
<th>END_DATE</th>
<th>RENT_TYPE</th>
</tr>
</thead>
        <tbody><tr>
<td>5</td>
<td>3</td>
<td>2022-09-16</td>
<td>2022-10-13</td>
<td>단기 대여</td>
</tr>
<tr>
<td>4</td>
<td>1</td>
<td>2022-09-01</td>
<td>2022-09-30</td>
<td>장기 대여</td>
</tr>
<tr>
<td>3</td>
<td>2</td>
<td>2022-09-05</td>
<td>2022-09-05</td>
<td>단기 대여</td>
</tr>
<tr>
<td>1</td>
<td>4</td>
<td>2022-09-27</td>
<td>2022-10-26</td>
<td>장기 대여</td>
</tr>
</tbody>
      </table>
<hr>

<h5>주의사항</h5>

<p><code>START_DATE</code>와 <code>END_DATE</code>의 경우 예시의 데이트 포맷과 동일해야 정답처리 됩니다.</p>

---

## 🗝 문제 풀이


```RoomSql
SELECT 
    c.HISTORY_ID, 
    c.CAR_ID, 
    DATE_FORMAT(c.START_DATE, '%Y-%m-%d') AS START_DATE, 
    DATE_FORMAT(c.END_DATE, '%Y-%m-%d') AS END_DATE,
    CASE 
        WHEN DATEDIFF(END_DATE,START_DATE) + 1 >= 30 THEN '장기 대여' 
        ELSE '단기 대여' END AS RENT_TYPE
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY c
WHERE START_DATE BETWEEN '2022-09-01' AND '2022-09-30'
ORDER BY HISTORY_ID DESC;
```

여기서 생각해봐야할 것들을 정리해본다.

1. DATE 형식을 YYYY-MM-DD로 변경하여 출력할 것
2. 대여시작일 30일을 기준으로 장기대여, 단기대여를 구분하여 RENT_TYPE 칼럼추가
3. 9월에 속하는 대여기간만 조회해야한다.
4. 최종 출력된 목록에서 HISTORY_ID 기준으로 내림차순 정렬하기

1번은 `DATA_FORMAT`을 사용해 변경해주었고

2번은 CASE 문법을 이용해서 일수의 차이를 구한다음 구분이 가능하다.    
시간의 차이를 구할때는 `DATEDIFF(종료날짜,시작날짜)`를 사용해 차이를 구해주었고   
차이의 +1을 해야 한달이 된다는 사실을 까먹지말자!  

3번은 `BETWEEN` 문법으로 9월에 해당하는 것들만 찾고  

마지막으로 `ORDER BY`를 통해서 정렬해주면 문제는 통과!

 


<br/>

[자동차 대여 기록에서 장기/단기 대여 구분하기]:https://school.programmers.co.kr/learn/courses/30/lessons/151138
