---
layout: post
title: "[Programmers] 소수 찾기 (Java)"
subtitle: 고득점 Kit (완전탐색 - Level 2)
categories: Algorithm
tags: [Algorithm, Programmers]
comments: true
published: true
---

## 📌 문제 : [소수 찾기]

### 📖 문제 설명

<p>한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.</p>

<p>각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.</p>

#### 제한사항

<ul>
<li>numbers는 길이 1 이상 7 이하인 문자열입니다.</li>
<li>numbers는 0~9까지 숫자만으로 이루어져 있습니다.</li>
<li>"013"은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.</li>
</ul>

#### 입출력 예

<table class="table">
        <thead><tr>
<th>numbers</th>
<th>return</th>
</tr>
</thead>
        <tbody><tr>
<td>"17"</td>
<td>3</td>
</tr>
<tr>
<td>"011"</td>
<td>2</td>
</tr>
</tbody>
      </table>

#### 입출력 예 설명

<p>예제 #1<br>
[1, 7]으로는 소수 [7, 17, 71]를 만들 수 있습니다.</p>

<p>예제 #2<br>
[0, 1, 1]으로는 소수 [11, 101]를 만들 수 있습니다.</p>

<ul>
<li>11과 011은 같은 숫자로 취급합니다.</li>
</ul>

---

## 🗝 문제 풀이

### ☀️ 풀이 방법

이번문제는 String으로 주어지는 문자열을 주어지는데   
문자열 하나씩 만들 수 있는 모든 조합을 소수를 찾아 소수의   
개수를 반환하는 문제이다.   

예제로 `17`이라는 값이 주어졌을때  
`1`,`17`,`7`,`71`과 같이 총 4개의 조합을 구할 수 있고   
이 중에 `17`,`7`,`71`이 소수기 때문에 정답은 소수의 개수인 `3`을 리턴하면 된다.   

여기서 만약 조합을 구하는 길이가 정해져 있다면   
다중 for문을 이용해 풀 수 있지만 지금과 같은 경우에는   
numbers라는 문자열이 1~7개의 길이를 가진 예제가 주어지기 때문에   
재귀를 통한 완전탐색으로 풀어야겠다라는 결론에 도달했다.  

<br/> 

`012`라는 문자열이 주어졌다고 가정하고 아래의 순서를 살펴보자    
해당 예제의 조합에서는 `2`라는 값만 소수이기 때문에 리턴값은 `1`이어야한다.  
_(편의상 true=⭕️ / false=❌ 로 표현하겠습니다.)_

![image](https://user-images.githubusercontent.com/95069395/225528468-38bd539f-096f-411f-bc3f-c7e3edb77f9a.gif){: .align-left style="max-width: 100%"}

우선 주어진 문자열을 `int[] arr`라는 배열로 쪼개었고   
재귀를 통해 탐색한 곳을 관리하는 `boolean[] visit`라는 배열을 만들어   
탐색할 때 해당 배열의 조건을 이용해 조합을 구하였다.    
재귀를 호출하면서 `Set<Integer> answer`에 소수인 값들은 담도록하였고   
중복된 값이 들어갈 수 있기 때문에 Collection의 Set을 이용하였다.

위의 그림에서 알 수 있듯이   
arr 배열을 순회할때 재귀함수를 호출하고 호출된 재귀함수에서   
또다시 순회하면서 재귀함수를 호출하는 반복적인 모습을 볼 수 있다.    

문제 풀이를 위한 5가지를 살펴보자    
1. arr에서 순회할때 재귀함수가 호출되면 true로 체크한다.  
2. 방문하지 않은 인덱스만 재귀한다.  
3. 재귀할때 현재 본인 인덱스 값을 String 타입 number 변수에 더해 매개변수로 넘겨준다.   
4. 순회한 인덱스는 false로 변경한다.     
5. 재귀 탈출조건은 number 길이와 arr 배열길이가 같을때 입니다.

해당 조건을 토대로 코드를 살펴보면서 이해해보자.

<br/>

### 👨🏻‍💻 코드

```java
import java.util.*;

class Solution {
    public int solution(String numbers) {

        // 만들수 있는 조합 중 소수의 갯수를 구하는 문제
        // String 길이가 달라지기 때문에 재귀로 풀어야하는 문제
        String[] arr = numbers.split("");
        boolean[] visit = new boolean[arr.length];
        Set<Integer> answer = new HashSet<>(); // 소수인 값만 담게되는 HashSet

        for (int i = 0; i < arr.length; i++) {
            recursion(arr, arr[i], answer, i, visit);
            visit[i] = false;
        }
        return answer.size();
    }

    public void recursion(String[] arr, String number, Set<Integer> answer, int index, boolean[] visit) {
        visit[index] = true; // 해당 index 재귀 호출시 방문 체크\
        int num = Integer.parseInt(number);  // String -> int
        if (isPrime(num)) answer.add(num); // 소수인 숫자는 Set에 추가 (중복인 것은 제거)
        if (number.length() == arr.length) return; // 탈출조건 , number의 문자열길이와 배열길이가 같아질때

        for (int i = 0; i < arr.length; i++) {
            if (!visit[i]) {
                recursion(arr, number + arr[i], answer, i, visit); // number값을 이어붙여서 호출
                visit[i] = false;
            }
        }
    }

    // 소수일경우 true, 아닐경우 false
    public boolean isPrime(int num) {
        if (num == 2) return true;
        if (num % 2 == 0 || num == 1) return false;
        for (int i = 3; i <= Math.sqrt(num); i++) {
            if (num % i == 0) {
                return false;
            }
        }
        return true;
    }
}
```

우선 재귀함수인 `recursion` 메서드를 하나 만들었고   
소수를 판단해 소수면 true를 반환하는 메서드인 `isPrime`을 만들었다.  

처음부터 보면 arr배열을 순회하는 for문이 존재하고   
재귀함수를 호출한다. 0번 인덱스부터 조합을 구하기위해서 재귀함수가 호출되고   
재귀함수를 호출한 후에 방문여부를 표시하는 `visit[index]`의 값을 true 설정한다.   
그리고 소수인지판단해 Set에 값을 저장하고 아니면 if문을 통과하게 되어진다.   

재귀함수 탈출조건을 설정해주고, 본격적으로 조합을 구하기 위한 재귀함수안에   
for문을 한번 더 실행 시킨다. 방문하지않은 곳만 다시 재귀함수를 호출하기 위해서   
if문으로 `!visit[i]`라는 조건을 걸어 재귀함수를 호출하게된다.   

이렇게 0번 인덱스에 false로 되어있는 곳을 방문하게되고   
재귀함수가 호출될때 `number + arr[i]`를 하여 현재 탐색중인 문자열을   
계속 이어붙이면서 현재 어떤 값을 찾고 있는지 알 수 있고 해당조건을 통해서   
재귀함수를 탈출하는 조건으로도 사용할 수 있다.   

이렇게 쭉 재귀함수가 탐색을 마치고 탈출조건에 걸리게되면   
`visit[i] = false;`를 통해 방문했던 곳을 다시 방문하지 않았다고 변경하고   
탐색중이던 for문을 순회할떄는 다시 원래상태의 visit배열이 되어진다.   
이러한 원리로 완전탐색을 통해 조합을 구한뒤 풀이에 맞는 값을 반환해주면 문제해결!  

재귀라는 개념이 아직 어색하고 잘 보이지 않기에 연습이 많이 필요해보인다.



<br/>

[소수 찾기]:https://school.programmers.co.kr/learn/courses/30/lessons/42839
