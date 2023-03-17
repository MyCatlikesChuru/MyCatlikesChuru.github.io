---
layout: post
title: "[Programmers] 흉부외과 또는 일반외과 의사 목록 출력하기 (MySQL)"
subtitle: Level 1
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [흉부외과 또는 일반외과 의사 목록 출력하기] 

### 📖 문제 설명  

<p>다음은 종합병원에 속한 의사 정보를 담은<code>DOCTOR</code> 테이블입니다. <code>DOCTOR</code> 테이블은 다음과 같으며 <code>DR_NAME</code>, <code>DR_ID</code>, <code>LCNS_NO</code>, <code>HIRE_YMD</code>, <code>MCDP_CD</code>, <code>TLNO</code>는 각각 의사이름, 의사ID, 면허번호, 고용일자, 진료과코드, 전화번호를 나타냅니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>DR_NAME</td>
<td>VARCHAR(20)</td>
<td>FALSE</td>
</tr>
<tr>
<td>DR_ID</td>
<td>VARCHAR(10)</td>
<td>FALSE</td>
</tr>
<tr>
<td>LCNS_NO</td>
<td>VARCHAR(30)</td>
<td>FALSE</td>
</tr>
<tr>
<td>HIRE_YMD</td>
<td>DATE</td>
<td>FALSE</td>
</tr>
<tr>
<td>MCDP_CD</td>
<td>VARCHAR(6)</td>
<td>TRUE</td>
</tr>
<tr>
<td>TLNO</td>
<td>VARCHAR(50)</td>
<td>TRUE</td>
</tr>
</tbody>
      </table>
<hr>

<h5>문제</h5>

<p><code>DOCTOR</code> 테이블에서 진료과가 흉부외과(CS)이거나 일반외과(GS)인 의사의 이름, 의사ID, 진료과, 고용일자를 조회하는 SQL문을 작성해주세요. 이때 결과는 고용일자를 기준으로 내림차순 정렬하고, 고용일자가 같다면 이름을 기준으로 오름차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p><code>DOCTOR</code> 테이블이 다음과 같을 때</p>
<table class="table">
        <thead><tr>
<th>DR_NAME</th>
<th>DR_ID</th>
<th>LCNS_NO</th>
<th>HIRE_YMD</th>
<th>MCDP_CD</th>
<th>TLNO</th>
</tr>
</thead>
        <tbody><tr>
<td>루피</td>
<td>DR20090029</td>
<td>LC00010001</td>
<td>2009-03-01</td>
<td>CS</td>
<td>01085482011</td>
</tr>
<tr>
<td>패티</td>
<td>DR20090001</td>
<td>LC00010901</td>
<td>2009-07-01</td>
<td>CS</td>
<td>01085220122</td>
</tr>
<tr>
<td>뽀로로</td>
<td>DR20170123</td>
<td>LC00091201</td>
<td>2017-03-01</td>
<td>GS</td>
<td>01034969210</td>
</tr>
<tr>
<td>티거</td>
<td>DR20100011</td>
<td>LC00011201</td>
<td>2010-03-01</td>
<td>NP</td>
<td>01034229818</td>
</tr>
<tr>
<td>품바</td>
<td>DR20090231</td>
<td>LC00011302</td>
<td>2015-11-01</td>
<td>OS</td>
<td>01049840278</td>
</tr>
<tr>
<td>티몬</td>
<td>DR20090112</td>
<td>LC00011162</td>
<td>2010-03-01</td>
<td>FM</td>
<td>01094622190</td>
</tr>
<tr>
<td>니모</td>
<td>DR20200012</td>
<td>LC00911162</td>
<td>2020-03-01</td>
<td>CS</td>
<td>01089483921</td>
</tr>
<tr>
<td>오로라</td>
<td>DR20100031</td>
<td>LC00010327</td>
<td>2010-11-01</td>
<td>OS</td>
<td>01098428957</td>
</tr>
<tr>
<td>자스민</td>
<td>DR20100032</td>
<td>LC00010192</td>
<td>2010-03-01</td>
<td>GS</td>
<td>01023981922</td>
</tr>
<tr>
<td>벨</td>
<td>DR20100039</td>
<td>LC00010562</td>
<td>2010-07-01</td>
<td>GS</td>
<td>01058390758</td>
</tr>
</tbody>
      </table>
<p>SQL을 실행하면 다음과 같이 출력되어야 합니다.</p>
<table class="table">
        <thead><tr>
<th>DR_NAME</th>
<th>DR_ID</th>
<th>MCDP_CD</th>
<th>HIRE_YMD</th>
</tr>
</thead>
        <tbody><tr>
<td>니모</td>
<td>DR20200012</td>
<td>CS</td>
<td>2020-03-01</td>
</tr>
<tr>
<td>뽀로로</td>
<td>DR20170123</td>
<td>GS</td>
<td>2017-03-01</td>
</tr>
<tr>
<td>벨</td>
<td>DR20100039</td>
<td>GS</td>
<td>2010-07-01</td>
</tr>
<tr>
<td>자스민</td>
<td>DR20100032</td>
<td>GS</td>
<td>2010-03-01</td>
</tr>
<tr>
<td>패티</td>
<td>DR20090001</td>
<td>CS</td>
<td>2009-07-01</td>
</tr>
<tr>
<td>루피</td>
<td>DR20090029</td>
<td>CS</td>
<td>2009-03-01</td>
</tr>
</tbody>
      </table>
<hr>

<h5>주의사항</h5>

<p>날짜 포맷은 예시와 동일하게 나와야합니다.</p>


> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---  

문제는 현재 `FIRST_HALF` 테이블에서 총주문량이 큰값을 내림차순으로 정렬하고   
총주문량이 같다면 출하번호 기준으로 오름차순으로 정렬하는 문제이다.

## 🗝 문제 풀이

```RoomSql
SELECT 
    D.DR_NAME, 
    D.DR_ID, 
    D.MCDP_CD, 
    DATE_FORMAT(D.HIRE_YMD, '%Y-%m-%d') AS HIRE_YMD
FROM DOCTOR D
WHERE D.MCDP_CD = 'CS' OR D.MCDP_CD = 'GS'
ORDER BY HIRE_YMD DESC, DR_NAME ASC;
```

DATE_FORMAT을 이용해 날짜형식을 변경해준다.   
그리고 흉부외과(CS), 일반외과(GS)인 경우만 걸러내야하기 때문에   
WHERE절을 이용해 조건을걸어 값을 필터링해주자

ORDER BY를 통해 내림,오름차순 정렬을 해주면 되는데   
여기서 정렬조건은 2개이다. 고용날짜(`HIRE_YMD`)를 기준으로 내림차순을 하고   
만약 고용날짜가 같을 경우 의사이름(`DR_NAME`)을 기준으로 오름차순 정렬을 해야한다.   

ORDER BY 문법에 첫번째 정렬값이 같다면 콤마(,)로 다음 조건을 적어주면   
해당 기준으로 정렬을 진행해준다.

 


<br/>

[흉부외과 또는 일반외과 의사 목록 출력하기]:https://school.programmers.co.kr/learn/courses/30/lessons/132203
