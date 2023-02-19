---
layout: post
title: "[Programmers - Java] 프린터"
subtitle: 고득점 Kit (스택,큐 - Level 2)
categories: Algorithm
tags: [Algorithm, Programmers]
comments: true
published: true
---

## 📌 문제 : [프린터]  

### 📖 문제 설명  

<p>일반적인 프린터는 인쇄 요청이 들어온 순서대로 인쇄합니다. 그렇기 때문에 중요한 문서가 나중에 인쇄될 수 있습니다. 이런 문제를 보완하기 위해 중요도가 높은 문서를 먼저 인쇄하는 프린터를 개발했습니다. 이 새롭게 개발한 프린터는 아래와 같은 방식으로 인쇄 작업을 수행합니다.</p>
<div class="highlight"><pre class="codehilite"><code>1. 인쇄 대기목록의 가장 앞에 있는 문서(J)를 대기목록에서 꺼냅니다.
2. 나머지 인쇄 대기목록에서 J보다 중요도가 높은 문서가 한 개라도 존재하면 J를 대기목록의 가장 마지막에 넣습니다.
3. 그렇지 않으면 J를 인쇄합니다.
</code></pre></div>
<p>예를 들어, 4개의 문서(A, B, C, D)가 순서대로 인쇄 대기목록에 있고 중요도가 2 1 3 2 라면 C D A B 순으로 인쇄하게 됩니다.</p>

<p>내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 알고 싶습니다. 위의 예에서 C는 1번째로, A는 3번째로 인쇄됩니다.</p>

<p>현재 대기목록에 있는 문서의 중요도가 순서대로 담긴 배열 priorities와 내가 인쇄를 요청한 문서가 현재 대기목록의 어떤 위치에 있는지를 알려주는 location이 매개변수로 주어질 때, 내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 return 하도록 solution 함수를 작성해주세요.</p>

#### 제한사항

<ul>
<li>현재 대기목록에는 1개 이상 100개 이하의 문서가 있습니다.</li>
<li>인쇄 작업의 중요도는 1~9로 표현하며 숫자가 클수록 중요하다는 뜻입니다.</li>
<li>location은 0 이상 (현재 대기목록에 있는 작업 수 - 1) 이하의 값을 가지며 대기목록의 가장 앞에 있으면 0, 두 번째에 있으면 1로 표현합니다.</li>
</ul>

#### 입출력 예  

<table class="table">
        <thead><tr>
<th>priorities</th>
<th>location</th>
<th>return</th>
</tr>
</thead>
        <tbody><tr>
<td>[2, 1, 3, 2]</td>
<td>2</td>
<td>1</td>
</tr>
<tr>
<td>[1, 1, 9, 1, 1, 1]</td>
<td>0</td>
<td>5</td>
</tr>
</tbody>
      </table>

#### 입출력 예 설명  

<p>예제 #1</p>  
<p>문제에 나온 예와 같습니다.</p>

<p>예제 #2</p>  
<p>6개의 문서(A, B, C, D, E, F)가 인쇄 대기목록에 있고 중요도가 1 1 9 1 1 1 이므로 C D E F A B 순으로 인쇄합니다. </p>

<p><a href="http://www.csc.kth.se/contest/nwerc/2006/problems/nwerc06.pdf" target="_blank" rel="noopener">출처</a></p>



---

## 🗝 문제 풀이

문제내용이 얼핏 어려울지 몰라도, 천천히 읽어보면 답이보인다.   
문제 부터 파악을 해보자   

이 문제의 핵심은 중요도가 높은 문서는 프린트를 하고  
인쇄 진행을하는 순서의 문서가 중요도가 대기목록보다 낮으면   
대기목록의 마지막으로 보내지는게 핵심이다.  


![image](https://user-images.githubusercontent.com/95069395/219957856-f278355c-33bd-49b1-b466-0888d9d3265e.png){: .align-left style="max-width: 100%"}

위의 그림대로 우선 프로그래밍을 진행하였다.   

`대기목록 -> 인쇄진행 -> 확인후 대기목록 or 프린트완료 List로 이동`  
이렇게를 큰틀로 잡고 진행하였고 첫번째 예제인 `[2, 1, 3, 2]`를 한번 봐보자

![image](https://user-images.githubusercontent.com/95069395/219957560-50050212-afe0-4621-95c9-cc4cdda5d21c.gif){: .align-left style="max-width: 100%"}

위의 GIF처럼 중요도가 높으면 인쇄 List로 이동하고  
중요도가 낮으면 대기목록 맨뒤로 이동을 한다.   

최종적으로 반환해야하는 값은 Location에 따라 결정된다.   
Locaiton과 같은 경우는 처음에 목록이 만들어졌을때의 목록위치다.   
제한사항에서 보면 0부터 시작한다는 것을 알 수 있고  

예를들어 Location = 2 라고 가정할 경우   
문서이름 A,B,C,D라고 정해졌으면 0,1,2,3의 위치로  
C문서를 가르키게된다. 즉, C문서가 몇번째로 인쇄가 되어지냐?    
라는게 이번 문제풀이의 답이 되어지겠다.    
위에 그림에서 보듯이 C는 첫번째로 반환되어지기 때문에 return = 1이 된다.

<br/>

좀 더 쉽게 두번째 예제를 보자
`[1, 1, 9, 1, 1, 1]`

![image](https://user-images.githubusercontent.com/95069395/219958614-ac20ff5a-b105-431b-85fb-0f03a16cc3f4.png){: .align-left style="max-width: 100%"}

처음에 문서가 이렇게 있을 것이다. Location이 0이라는 뜻은   
A라는 문서를 가르키는 것이고, 최종적으로 인쇄가 완료된 후면

<br/>

![image](https://user-images.githubusercontent.com/95069395/219958616-d79f0380-bd2d-4fb2-9dbd-49692dfe7729.png){: .align-left style="max-width: 100%"}

위에 처럼 인쇄가 되어질 것이다.   
그러면 최종적으로 A라는 문서가 어느 위치에 있는지?  
1부터 시작되어 5번째로 인쇄가 되어지기 때문에  
5라는 값을 반환해주면 된다.   

여기까지 문제 이해와 설명부분이였고 코드로 살펴보자  

```java
public int solution(int[] priorities, int location) {

    // 문제의 핵심.
    // 높은 숫자가 중요도가 높은 것이다.
    // 대기문서에서 꺼낸 문서가 대기열에 있는 문서보다 중요도가 낮으면 맨뒤로간다.
    Queue<Integer> important = new LinkedList<>(); // 우선순위를 담는 Queue
    Queue<Integer> document = new LinkedList<>(); // 문서를 위치를 담는 Queue
    List<Integer> result = new ArrayList<>(); // 프린트를 완료한 List
    int maxDoc = 0;

    // 우선순위, 문서를 Queue에 담는다.
    for (int i=0; i<priorities.length; i++) {
        important.add(priorities[i]);
        document.add(i);
    }

    while (important.size() != 0) { // Queue의 담긴게 없으면 반복문을 탈출
        Integer print = important.poll(); // 대기열의 중요도를 하나씩 빼온다
        Integer target = document.poll(); // 대기열의 문서위치를 하나씩 빼온다
        if(important.size() >= 1) {
            maxDoc = important.stream().mapToInt(i -> i).max().getAsInt(); // 현재 우선순위가 가장 높은 숫자
        }
        if(print >= maxDoc) {
            result.add(target); // 우선순위가 높을경우 프린트 완료 List에 문서위치를 담는다.
        } else {
            important.add(print); // 우선순위가 아닐경우 우선순위를 마지막에 넣는다.
            document.add(target); // 우선순위가 아닐경우 문서위치를 마지막에 넣는다.
        }
    }

    return result.indexOf(location) + 1;
}
```
더 깔끔한 풀이가 분명 가능할 것 같다.   
우선 내가 풀이한 방법을 설명해보자면  

Queue를 2개를 만들었다.
1. 우선순위를 담는 Queue  
2. 문서의 위치를 담는 Queue  

그리고 최종적을 인쇄가 완료되면 담는 List를 만들어주었다.  
문서의 위치 Queue는 0부터 채워넣어주었다.   
Queue를 이용에 앞에서부터 poll();하여 해당 값이  
현재 가지고 있는 Queue에서 가장크거나 큰값이랑 같은지 비교를하여  
통과를하면 인쇄완료 List에 문서의 위치를 저장하는 방식이다.   

최종적으로 문서의 위치가 저장되면 location 값과 같은  
index를 가져와 +1을 하여 반환해주었다.


[프린터]:https://school.programmers.co.kr/learn/courses/30/lessons/42587
