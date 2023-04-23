---
layout: post
title: "[Programmers] 루시와 엘라 찾기 (MySQL)"
subtitle: Level 2
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [루시와 엘라 찾기]

### 📖 문제 설명  

<p><code>ANIMAL_INS</code> 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다. <code>ANIMAL_INS</code> 테이블 구조는 다음과 같으며, <code>ANIMAL_ID</code>, <code>ANIMAL_TYPE</code>, <code>DATETIME</code>, <code>INTAKE_CONDITION</code>, <code>NAME</code>, <code>SEX_UPON_INTAKE</code>는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.</p>
<table class="table">
        <thead><tr>
<th>NAME</th>
<th>TYPE</th>
<th>NULLABLE</th>
</tr>
</thead>
        <tbody><tr>
<td>ANIMAL_ID</td>
<td>VARCHAR(N)</td>
<td>FALSE</td>
</tr>
<tr>
<td>ANIMAL_TYPE</td>
<td>VARCHAR(N)</td>
<td>FALSE</td>
</tr>
<tr>
<td>DATETIME</td>
<td>DATETIME</td>
<td>FALSE</td>
</tr>
<tr>
<td>INTAKE_CONDITION</td>
<td>VARCHAR(N)</td>
<td>FALSE</td>
</tr>
<tr>
<td>NAME</td>
<td>VARCHAR(N)</td>
<td>TRUE</td>
</tr>
<tr>
<td>SEX_UPON_INTAKE</td>
<td>VARCHAR(N)</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<p>동물 보호소에 들어온 동물 중 이름이 Lucy, Ella, Pickle, Rogan, Sabrina, Mitty인 동물의 아이디와 이름, 성별 및 중성화 여부를 조회하는 SQL 문을 작성해주세요. </p>

<h5>예시</h5>

<p>이때 결과는 아이디 순으로 조회해주세요. 예를 들어 <code>ANIMAL_INS</code> 테이블이 다음과 같다면</p>
<table class="table">
        <thead><tr>
<th>ANIMAL_ID</th>
<th>ANIMAL_TYPE</th>
<th>DATETIME</th>
<th>INTAKE_CONDITION</th>
<th>NAME</th>
<th>SEX_UPON_INTAKE</th>
</tr>
</thead>
        <tbody><tr>
<td>A373219</td>
<td>Cat</td>
<td>2014-07-29 11:43:00</td>
<td>Normal</td>
<td>Ella</td>
<td>Spayed Female</td>
</tr>
<tr>
<td>A377750</td>
<td>Dog</td>
<td>2017-10-25 17:17:00</td>
<td>Normal</td>
<td>Lucy</td>
<td>Spayed Female</td>
</tr>
<tr>
<td>A353259</td>
<td>Dog</td>
<td>2016-05-08 12:57:00</td>
<td>Injured</td>
<td>Bj</td>
<td>Neutered Male</td>
</tr>
<tr>
<td>A354540</td>
<td>Cat</td>
<td>2014-12-11 11:48:00</td>
<td>Normal</td>
<td>Tux</td>
<td>Neutered Male</td>
</tr>
<tr>
<td>A354597</td>
<td>Cat</td>
<td>2014-05-02 12:16:00</td>
<td>Normal</td>
<td>Ariel</td>
<td>Spayed Female</td>
</tr>
</tbody>
      </table>
<p>SQL문을 실행하면 다음과 같이 나와야 합니다. </p>
<table class="table">
        <thead><tr>
<th>ANIMAL_ID</th>
<th>NAME</th>
<th>SEX_UPON_INTAKE</th>
</tr>
</thead>
        <tbody><tr>
<td>A373219</td>
<td>Ella</td>
<td>Spayed Female</td>
</tr>
<tr>
<td>A377750</td>
<td>Lucy</td>
<td>Spayed Female</td>
</tr>
</tbody>
      </table>
<hr>

<p>본 문제는 <a href="https://www.kaggle.com/aaronschlegel/austin-animal-center-shelter-intakes-and-outcomes" target="_blank" rel="noopener">Kaggle의 "Austin Animal Center Shelter Intakes and Outcomes"</a>에서 제공하는 데이터를 사용하였으며 <a href="https://opendatacommons.org/licenses/odbl/1.0/" target="_blank" rel="noopener">ODbL</a>의 적용을 받습니다.</p>


> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. WHERE IN절을 이용해 이름 찾기
2. ID순으로 오름차순 정렬

```roomsql
SELECT ANIMAL_ID,
    NAME,
    SEX_UPON_INTAKE
FROM ANIMAL_INS
WHERE NAME IN ('Lucy','Ella','Pickle','Rogan','Sabrina','Mitty')
ORDER BY ANIMAL_ID ASC;
```


<br/>

[루시와 엘라 찾기]:https://school.programmers.co.kr/learn/courses/30/lessons/59046
