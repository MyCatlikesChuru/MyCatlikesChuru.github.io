---
layout: post
title: "[Programmers] 주식가격 (Java)"
subtitle: 고득점 Kit (스택,큐 - Level 2)
categories: Algorithm
tags: [Algorithm, Programmers]
comments: true
published: true
---

## 📌 문제 : [주식가격]  

### 📖 문제 설명  

<p>초 단위로 기록된 주식가격이 담긴 배열 prices가 매개변수로 주어질 때, 가격이 떨어지지 않은 기간은 몇 초인지를 return 하도록 solution 함수를 완성하세요.</p>

#### 제한사항    

<ul>
<li>prices의 각 가격은 1 이상 10,000 이하인 자연수입니다.</li>
<li>prices의 길이는 2 이상 100,000 이하입니다.</li>
</ul>

#### 입출력 예    
<table class="table">
        <thead><tr>
<th>prices</th>
<th>return</th>
</tr>
</thead>
        <tbody><tr>
<td>[1, 2, 3, 2, 3]</td>
<td>[4, 3, 1, 1, 0]</td>
</tr>
</tbody>
      </table>

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

해당 문제를 이중 for문을 이용해 풀면 간단하다.   
순환하는 값과 다음값을 비교해 작은 값이 있으면   
위치를 계산해서 리턴해주면 간단하게 풀리는 문제이다.  

이중 반복문을 사용하게 될 경우 O(n²)의 시간 복잡도를 가지며   
prices 배열의 길이가 늘어날수록 시간 복잡도가 배로 증가하기때문에 좋지 않다.   

해당 문제에서 요구하는 `Stack` 자료구조를 이용해 풀어보자.

<br/>  

우선 문제를 이해하기위해서 아래의 그림을 살펴보자  

![image](https://user-images.githubusercontent.com/95069395/220566032-10c63ebf-fc8b-48ce-bab4-3830706e8c81.png){: .align-left style="max-width: 100%"}

prices라는 가격이 담긴 배열을 주어준다음에 해당 가격이 언제 떨어지는지 체크하는 문제이다.    
떨어지지 않을 경우에는 배열 끝까지 시간초를 더한값을 담아줘야한다.   
해당 예제에서는 3번째 그림인 3에서 2로 넘어갈때 가격이 떨어지므로 1이란 값이 반환된 것이다.  


<br/>  

이제 문제를 이해했으니, Stack을 이용해 문제를 풀어보자  

![image](https://user-images.githubusercontent.com/95069395/220570853-d38f6244-e89d-48a3-8678-e81489c86221.png){: .align-left style="max-width: 100%"}

answer라는 배열을 만들어 리턴할 것인데, 어떻게 만들어줘야할지 정해보자  

1. **주식가격이 떨어지지 않는 가격들** -> `[배열길이 - 1 - 인덱스번호]`  
2. **주식가격이 떨어진 가격들** -> `[떨어진 가격의 인덱스번호 - 높은가격의 인덱스번호]`  

이렇게 정의할 수 있을 것 같다. 여기서 <span style="color:#F04965">**중요한건 주식가격이 떨어지지** </span>  
<span style="color:#F04965">**않는 가격들만 Stack 안에 저장할 예정이다.**</span>  
즉, 떨어진 가격은 Stack에서 제외시킬 것이다.


여기서 1번을 보충 설명하보자면 현재 `int[] prices`의 배열의 길이는 `5`이다.  
만약 주식가격이 떨어지지 않았다면 배열의 끝까지 초를 더할 것이다.  
그렇다는건 (배열의 길이 - 1)에서 본인의 인덱스번호만 뺴주게된다면   
몇초가 걸리는지 계산을 할 수 있다.

<br/>

처음부터 시작해보자

![image](https://user-images.githubusercontent.com/95069395/220564078-60d4352f-fed8-4dd6-ab48-f6853d835f73.png){: .align-left style="max-width: 100%"}

Stack에는 떨어지지않는 가격의  
인덱스 번호를 차례대로 쌓아올릴 예정이다.

<br/>


![image](https://user-images.githubusercontent.com/95069395/220564084-969fd14d-7cd6-4d42-9241-cf4c507375e8.png){: .align-left style="max-width: 100%"}

첫번쨰로 전에 있는 값들을 while문을 돌려 비교할 것이다.  
현재는 아무런 값이 없기때문에 다음 가격으로 넘어갈 것이다.   

넘어가기전에  가격이 `1`인 인덱스 번호인 `0`값을 Stack에 저장한다.

<br/>

![image](https://user-images.githubusercontent.com/95069395/220564090-9b076766-0a72-4a44-81ef-fb7375b2402f.png){: .align-left style="max-width: 100%"}

두번째 사이클이다. 첫번째와 동일하게   
숫자 `2`값의 전에 값중 높은값이 있는지 찾아낸다.  
만약 없을 경우 또 다음 가격으로 넘어간다.  

물론 똑같이 인덱스 번호를 Stack에 저장한다.

<br/>    

![image](https://user-images.githubusercontent.com/95069395/220564093-4dcbc4a3-8d79-47c1-bc91-cc994f7dcadb.png){: .align-left style="max-width: 100%"}

세번째 사이클도 두번째 사이클과 동일하다.

<br/>


![image](https://user-images.githubusercontent.com/95069395/220570858-bb8b794c-f4a6-49ec-a79d-c8da3d78ec61.png){: .align-left style="max-width: 100%"}

네번째 사이클에서는 숫자 `2`인 값보다 높은 값(`3`)이 존재한다.   
높은 값이 존재하므로 Stack에서는 높은 가격의 인덱스 번호를 `pop();`으로 제거한다.    
(앞에 있는 0,1인덱스의 값도 비교해서 높은가격이 있다면 제거한다.)

그리고 `int[] answer` 배열에는 가격이 몇초안에 떨어졌는지 담아줘야한다.   
처음에 얘기했던 공식을 적용하면 된다. 여기서는   

떨어진 가격 : `2` -> 인덱스 번호 : `3`  
높은가격 : `3` -> 인덱스 번호 : `2`   
와 같이 볼 수 있고 `떨어진 가격 - 본인 인덱스번호(높은가격)`의 차이인 `1`초를   
반환하는 배열에 담아줄 수 있다.
 
<br/>

![image](https://user-images.githubusercontent.com/95069395/220564100-5f78c105-1835-4357-a63e-bcdbf05a1f3f.png){: .align-left style="max-width: 100%"}

그리고 다음 사이클에서 비교할 수 있게, Stack에   
낮은 가격이였던 2의 인덱스값 `3`을 저장해준다.   

위에서도 얘기했듯이 가격의 떨어진 인덱스들만 Stack에서 제외시키는 작업이다.  
즉, 낮은 가격인 2의 값도 다음 사이클에서 비교할 수 있게 저장해줘야한다.

<br/>


![image](https://user-images.githubusercontent.com/95069395/220564102-3b24ed7d-41be-4742-8f11-140ea961385b.png){: .align-left style="max-width: 100%"}

이후부터는 전의 사이클들과 동일하게 진행되고   
마지막 사이클이기 때문에 Stack에 떨어지지 않은 가격의   
인덱스 번호들만 남은 상태이며, `int[] answer` 배열에는 떨어진 가격의   
시간이 담겨 있기 때문에 이제 남은 작업은 떨어지지 않은 가격을 배열에 담아주면된다.

<br/>

![image](https://user-images.githubusercontent.com/95069395/220564105-7cb76078-2c4d-40b8-b7dc-1525755d179c.png){: .align-left style="max-width: 100%"}

담아주는 과정은 맨위에서 설명했던 내용과 동일하다.    
떨어지지 않은 가격들은 배열을 끝까지 순회하기 때문에   
`[배열길이 - 1 - 인덱스번호]`를 한값을 `int[] answer`배열에 담아주면된다.   
`Stack` 자료구조 특성상 마지막의 값부터 `peek()`,`pop()`해올 수 있기 때문에    
배열의 마지막부터 값을 계산해서 반환해주면 문제 풀이는 끝난다.

<br/>  

### 👨🏻‍💻 코드

```java
import java.util.*;

class Solution {
    public int[] solution(int[] prices) {
        int[] answer = new int[prices.length]; // 반환할 배열
        Stack<Integer> stack = new Stack<>();

        for (int i = 0; i < prices.length; i++) {
            // 값이 떨어지는 주식의 시간을 저장
            while (!stack.isEmpty() && prices[i] < prices[stack.peek()]) {
                answer[stack.peek()] = i - stack.peek(); // 반환배열에 떨어지는 시간 초 저장
                stack.pop(); // 값이 떨어진 초는 제거
            }
            stack.push(i); // 값이 떨어지지않은 주식의 시간초를 저장
        }

        while (!stack.isEmpty()) {
            // 값이 떨어지지않은 주식들, 마지막 인덱스부터 차례대로 빼오기
            int last = stack.pop();
            answer[last] = prices.length - last - 1;
        }
        return answer;
    }
}
```

여기서 유의 깊게 봐야할 부분은 `stack.push(i);`  
순회가 끝나면 Stack에 인덱스값을 저장해주는 부분이다.   

그리고 while문의 조건을 통해서 현재 순회하는 인덱스의 값보다   
전에 Stack에 쌓여있는 인덱스 번호의 값중 큰값이 존재한다면   
`answer[stack.peek()] = i - stack.peek();`를 하여 초값을 계산하고   
`stack.pop();`하여 Stack에서 제거시켜주게된다. 위에 그림에서 설명했던 부분과 동일하다.

<br/>

[주식가격]:https://school.programmers.co.kr/learn/courses/30/lessons/42584
