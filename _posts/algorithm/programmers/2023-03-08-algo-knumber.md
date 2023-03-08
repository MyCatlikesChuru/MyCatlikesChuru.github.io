---
layout: post
title: "[Programmers - Java] K 번째수"
subtitle: 고득점 Kit (정렬 - Level 1)
categories: Algorithm
tags: [Algorithm, Programmers]
comments: true
published: true
---

## 📌 문제 : [K 번째수]  

### 📖 문제 설명  

<p>배열 array의 i번째 숫자부터 j번째 숫자까지 자르고 정렬했을 때, k번째에 있는 수를 구하려 합니다.</p>

<p>예를 들어 array가 [1, 5, 2, 6, 3, 7, 4], i = 2, j = 5, k = 3이라면</p>

<ol>
<li>array의 2번째부터 5번째까지 자르면 [5, 2, 6, 3]입니다.</li>
<li>1에서 나온 배열을 정렬하면 [2, 3, 5, 6]입니다.</li>
<li>2에서 나온 배열의 3번째 숫자는 5입니다.</li>
</ol>

<p>배열 array, [i, j, k]를 원소로 가진 2차원 배열 commands가 매개변수로 주어질 때, commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.</p>

#### 제한사항  

<ul>
<li>array의 길이는 1 이상 100 이하입니다.</li>
<li>array의 각 원소는 1 이상 100 이하입니다.</li>
<li>commands의 길이는 1 이상 50 이하입니다.</li>
<li>commands의 각 원소는 길이가 3입니다.</li>
</ul>

#### 입출력 예  

<table class="table">
        <thead><tr>
<th>array</th>
<th>commands</th>
<th>return</th>
</tr>
</thead>
        <tbody><tr>
<td>[1, 5, 2, 6, 3, 7, 4]</td>
<td>[[2, 5, 3], [4, 4, 1], [1, 7, 3]]</td>
<td>[5, 6, 3]</td>
</tr>
</tbody>
      </table>  

#### 입출력 예 설명    

<p>[1, 5, 2, 6, 3, 7, 4]를 2번째부터 5번째까지 자른 후 정렬합니다. [2, 3, 5, 6]의 세 번째 숫자는 5입니다.<br>
[1, 5, 2, 6, 3, 7, 4]를 4번째부터 4번째까지 자른 후 정렬합니다. [6]의 첫 번째 숫자는 6입니다.<br>
[1, 5, 2, 6, 3, 7, 4]를 1번째부터 7번째까지 자릅니다. [1, 2, 3, 4, 5, 6, 7]의 세 번째 숫자는 3입니다.</p>


#### 입출력 예 설명  

<ul>
<li>1초 시점의 ₩1은 끝까지 가격이 떨어지지 않았습니다.</li>
<li>2초 시점의 ₩2은 끝까지 가격이 떨어지지 않았습니다.</li>
<li>3초 시점의 ₩3은 1초뒤에 가격이 떨어집니다. 따라서 1초간 가격이 떨어지지 않은 것으로 봅니다.</li>
<li>4초 시점의 ₩2은 1초간 가격이 떨어지지 않았습니다.</li>
<li>5초 시점의 ₩3은 0초간 가격이 떨어지지 않았습니다.</li>
</ul>

<p>※ 공지 - 2019년 2월 28일 지문이 리뉴얼되었습니다.</p>  

---

## 🗝 문제 풀이  

### ☀️ 풀이 방법  

이번 문제이해해는 크게 어려운 점은 없었다.  
주어진 배열에 `i`,`j`의 길이만큼 추출한다음 그 배열에서  
`k`번째의 숫자를 새로운 배열에 만들어 담아 반환하면 되는 문제이다.

문제 풀이를 위해서 그림으로 설명해보자

![image](https://user-images.githubusercontent.com/95069395/223741523-81c10d79-a5fc-4441-8d50-4c73fc79c709.png){: .align-left style="max-width: 100%"}  

우선 반환되는 배열을 `int[] answer`선언해주었다. 반환하는 배열의 길이는  
`int[][] commands` 2차원 배열의 길이만큼 만들어주면된다.   
즉 3개의 배열이 존재하기 때문에 3칸으로 만들어준다.  

이제 2차원 배열을 순회하면서 배열의 길이를 추출하면된다.

![image](https://user-images.githubusercontent.com/95069395/223741531-7953e433-aa93-4d68-a6fe-9f48221acdd9.png){: .align-left style="max-width: 100%"}  

첫번째 배열 `i = 2`, `j = 5`, `k = 3`으로 `int[] array`배열에서   
2번째 부터 5번째까지 추출하면 `[5,2,6,3]`과 같이 추출할 수 있다.  
추출된 배열중 낮은값부터 `k`번째인 3번째를 추출하기 위해서는 `.sort`로 내림차순으로   
배열을 정령한다음 3번째 값을 `int[] asnwer` 배열 첫번째에 넣는다.

<br/>  

이후 과정은 동일하다.

![image](https://user-images.githubusercontent.com/95069395/223741537-733833a0-a3cd-4586-af89-63f7e5350507.png){: .align-left style="max-width: 100%"}   

2번째 배열 순회를하고 같은 과정을 거치는데 한가지 다른점이 있자면   
배열의 길이가 1개일 때는 `.sort`를 진행할 필요가 없기 때문에 생략하게 하였다.  

<br/>

이렇게 마지막까지 순회를 하면

![image](https://user-images.githubusercontent.com/95069395/223741543-42080ea7-b831-4e43-acf5-494b809633cc.png){: .align-left style="max-width: 100%"}    

최종적으로 `int[] answer`에 문제요구사항에 맞는 값이 들어가고   
반환해주게된다면, 문제 풀이는 끝난다.

<br/>

### 👨🏻‍💻 코드

```java
import java.util.*;

class Solution {
    public int[] solution(int[] array, int[][] commands) {

        // 반환할 배열을 만든다.
        int[] answer = new int[commands.length];

        // commands 배열 길이만큼 순회를 한다.
        for (int i=0; i<commands.length; i++) {
            int[] cutArray = Arrays.copyOfRange(array, commands[i][0]-1, commands[i][1]);
            if(cutArray.length >=2) Arrays.sort(cutArray);
            answer[i] = cutArray[commands[i][2]-1];
        }
        
        return answer;
    }
}
```

해당 코드에서는 `Arrays.copfOfRange();` 메서드의 사용법만 알면   
크게 어렵지 않은 코드이다. 위에 문제 풀이에서는 인덱스값이아닌 1번부터 진행하여   
해당 부분만 신경써서 배열에 맞는 값을 추출하면 끝!  



<br/>

[K 번째수]:https://school.programmers.co.kr/learn/courses/30/lessons/42748
