---
layout: post
title: "함수형 프로그래밍이란? - Java"
subtitle: 
categories: CS
tags: [ComputerScience]
comments: true
published: true 
---

### 프로그래밍 패러다임  

프로그래머에게 프로그래밍의 관점을 갖게 해주는 역할을 하는 개발 방법론이다.  
어떤 언어는 특정한 패러다임을 지원한다. (ex: 자바=객체지향, 하스켈=함수형)   
여러 패러다임을 지원하는 언어도 존재한다. (ex: 파이썬, C++, 자바스크립트)  
자바는 jdk 1.8부터 함수형 프로그래밍 패러다임을 지원하기 위해 람다식, 생성자 레퍼런스  
메서드 레퍼런스를 도입했고, 선언형 프로그래밍을 위해 스트림 같은 표준 API들도 추가하였다.

프로그래밍 패러다임은 크게 아래와 같이 나뉜다.  

선언형 -> 함수형  
명령형 -> 객체지향형, 절차지향형  

<br/>  

### 함수형 프로그래밍  

#### 함수형 프로그래밍의 특징

무엇을 풀어내는가에 집중하는 패러다임이다.  
프로그램은 함수로 이루어진 것이다라는 명제가 담겨있는 패러다임이기도 하다.  

객체지향 프로그래밍이 객체간 메세지와 협력 관계의 정의로 이루어져있다면, 함수형 프로그래밍은  
단순히 함수들의 조합으로 이루어 진다. 함수들의 조합이라는 말은 쉽지만 실제로 함수형 코딩을  
잘 하는데 꽤 높은 러닝커브를 요구한다. 객체지향의 4대원칙, SOILD와 같이 함수형 프로그래밍에도  
몇 가지 특징들이 있다. 아래에서 알아보자.

![image](https://user-images.githubusercontent.com/95069395/223608704-9f5ca7e8-bbbb-4774-b25c-5e8dc858f488.png){: .align-left style="max-width: 70%"}  
[그림출처 - https://warpgate3.tistory.com]

<br/>

**1). Immutable Data (불변하는 데이터)**  
데이터가 함수 밖에서 변형하지 않는 원칙이다. 다만, 함수의 반환 값은 함수 내에서 수행된 작업을 반영해야한다.  



**2). Pure Functions (순수함수)**  
함수형 프로그래밍의 이상은 순수함수라고 알려져 있다. <span style="color:#F04965">**입력 파라미터에만 의존하며 부수 효과(side effect)를 일으키지 않는 함수**</span>를 뜻한다. 순수함수를 통해 불변하는 데이터의 특징을 가질 수 있다. 이러한 차이가 객체지향 프로그래밍과 다른 점이다. 객체의 메서드가 객체의 상태와 상호작용을 하며, 외부 상태가 함수 내에서 조작되고는 한다.   

순수함수의 예제를 살펴보자 
```java
public class Pure {
    public String greeting(String name) {
        return "Hello " + name;
    }
}
```
`greeting()`이라는 메서드가 호출되었을 때, 매개변수의 따라 리턴되어 지는 값이 달라진다.  
호출할 때 매개변수가 동일하면 아무리 호출해도 동일한 값이 반환되고 함수의 값이 밖에서 변형되거나   
하는일은 발생하지 않는 코드이다. 즉 데이터가 불변하는 특징을 가지고 있다.  

<br/>  

그럼 순수함수의 반대 예제를 살펴보자

```java
public class NonPure {
    private String name = "이재혁";
    
    public String greeting() {
        return "Hello " + name;
    }
}
```
필드 변수에 "이재혁"이라는 `name`값이 String 형태로 지정되어 있다.   
여기서 중요한점은 name이라는 값을 조작하게 된다면 함수의 호출시 반환값이 변경되어진다.  
즉, 전역변수의 값이 조작 되면 부수효과(side effect)가 발생된다.

<br/>

**3). No foreach (반복문 사용 X)**  
for, while문과 같은 반복문을 사용하지 않는다. 반복문 안에는 가변적인 값들과 처리에 대한 코드가 섞여 있다. 함수형 프로그래밍에서는 반복문 대신에 map,filter 같은 함수를 매개변수로 받는 메서드를 이용한다.   

예제로 살펴보자, 우선 일반적인 for문의 사용 경우다.
```java
public class Noiterate {
    List<String> names = List.of("홍씨", "김씨", "이씨");

    public void getName() {
        for (int i = 0; i < names.size(); i++) {
            System.out.println(names.get(i));
        }
    }
}
```
위와 같이 for문을 사용해 리스트의 이름을 출력하였다.

<br/>  

함수형 프로그래밍 예제를 살펴보자

```java
public class Noiterate {
    List<String> names = List.of("홍씨", "김씨", "이씨");

    public void getName() {
        names.forEach((name) -> {
            System.out.println(name);
        });
    }
}
```
`Iterable<T>` 인터페이스를 상속하고 있는 List에서  
`forEach(Consumer<? super T> action)`를 호출하여  
`Consumer<T>` 함수형 인터페이스를 이용해 반복문을 이용한 예제이다.

<br/>

**4). High Order Function (고차함수)**   
함수를 인자로 받거나 함수를 반환 값으로 이용할 수 있는 것을 말한다.  
클로저라는 개념도 나오는데 클로저는 부모 함수가 실행되었더라도 부모 함수의   
변수에 접근할 수 있는 내부 함수(inner function)을 뜻한다.   

고차함수와 특징을 포함한 자바 예제코드를 살펴보자  
```java
public class HigherOrderFunctionExample {
    public static void main(String[] args) {
        // 함수형 인터페이스 Function을 사용하여 고차 함수를 구현합니다.
        Function<Integer, Function<Integer, Integer>> add = x -> y -> x + y;
        Function<Integer, Function<Integer, Integer>> subtract = x -> y -> x - y;

        // 함수를 호출하여 결과를 출력합니다.
        System.out.println(add.apply(2).apply(3));       // 5
        System.out.println(subtract.apply(5).apply(2));  // 3
    }
}
```
위에코드는 고차함수의 함수를 반환값으로 이용할 수 있는 예제이다.  
`add.applay(2);`를 호출하게 될 경우 `Function<Integer, Integer>`타입의 메서드로 반환하게된다.  
반환된 함수를 다시 `.add(3);`하였을 경우 `y -> x + y`를 계산한 Integer타입으로 반환하게되어   
이전에 호출했을때 매개변수로 전달된 2값과 더해져 5라는 값이 출력되게 되어진다.  

즉, `Function<Integer, Integer>`는 정수값을 받아 반환하는 함수이고,  
해당함수를 반환값으로 이용하고 있어, 고차함수라고 할 수 있다.


<br/>

❗️**일급객체란?**    
고차 함수를 쓰기 위해서는 해당 언어가 일급객체라는 특징을 가져야한다.

일급객체의 충족 조건 3가지
1. 모든 일급 객체는 변수나 데이터에 담을 수 있어야 한다.
2. 모든 일급 객체는 함수의 파라미터로 전달 흘 수 있어야 한다.
3. 모든 일급 객체는 함수의 리턴값으로 사용 할 수 있어야 한다.  

자바의 람다 표현식을 배우다보면 '일급객체'라는 단어를 접하게 된다.   
뜻은 사용할 때 다른 요소들과 아무런 차별이 없다는걸 뜻한다.   
일급 객체는 어떠한 특정 언어에 국한되는 문법 단어가 아니다. 프로그래밍 언어론의 개념이다.  

Java에서의 일급객체 를 알아보자.  
첫번째, 객체는 변수나 데이터에 담을 수 있어야한다.  
```java
public class Main {
    public static void hello(){
        System.out.println("Hello World");
    }
    
    public static void main(String[] args) {
        Object a = hello; // 메서드를 변수에 할당 불가능
    }
}
```
위의 예제를 보면 메서드는 변수에 할당하거나 그럴수가 없다.   

<br/>

두번쨰, 객체는 함수의 파라미터로 전달 할 수 있어야 한다.  
```java
public class Main {

    public static void hello(){
        System.out.println("Hello World");
    }
    
    public static void print(Object func) {
    	func();
    }

    public static void main(String[] args) {
        print((Object) hello); // static 메서드를 함수 매개변수로 전달 불가능
    }
}
```
자바는 메서드를 다른메서드의 매개변수로 전달이 불가능하다.  

<br/>

세번째 객체는 함수의 리턴값으로 사용 할 수 있어야 한다.   
```java
public class OneObject {

    public static void hello(){
        System.out.println("Hello World");
    }

    public static hello print((Object) func) {
        return func; // 리턴값을 메서드 자체를 반환이 불가능
    }

    public static void main(String[] args) {
        hello result = print((Object) hello);
    }
}
```
역시 자바는 메서드 자체를 리턴값을 메서드 자체를 반환하는 행위가 불가능하다.

이러한 관점에서 자바의 메서드는 함수인데 일급 객체라고 부르지 않고, 자바스트립트의 함수는  
일급 객체라 부르는지 차이를 볼 수 있다.  

자바에서는 람다식, 익명 클래스에서 변수나 매개변수에 할당 할 수 있고,   
리턴값으로도 사용할 수 있기때문에 일급 객체의 요건을 충족한다.

<br/>

#### 함수형 프로그래밍의 장점과 한계  

✅ **장점**
1. 헷갈리는 문제와 오류를 피할 수 있다.  
2. 테스트와 디버깅에 용이하다.
3. 더 나은 모듈화와 짧은 코드를 제공한다.
4. 개발 생산성이 높일 수 있다.
5. 가독성이 높은 코드를 작성하기 쉽다.
6. 부수효과를 제거할 수 있다.
7. 함수 단위의 코드 재사용이 쉽다.
8. 프로그램의 동작을 예측하기 쉽다.

✅ **한계점** 
1. 개념을 이해하기 쉽지 않다.
2. 프로그래밍을 진행하며 수 많은 함수들을 파악하고 유지 관리하기 힘들어진다.
3. 광범위한 환경 설징이 필요할 수 이싿.
4. 함수가 많아질수록 함수를 조합하기 복잡해지면 꾸준한 리펙토링이 필요할 수 있다.



<br/>
<br/>

✨참고 블로그  
[https://warpgate3.tistory.com], [https://code-lab1.tistory.com], [https://inpa.tistory.com]

<br/>



[https://warpgate3.tistory.com]:https://warpgate3.tistory.com/entry/%EC%9E%90%EB%B0%94%EC%BD%94%EB%93%9C%EB%A1%9C-%EB%B3%B4%EB%8A%94-%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-Functional-Programming-in-Java    
[https://code-lab1.tistory.com]:https://code-lab1.tistory.com/245  
[https://inpa.tistory.com]:https://inpa.tistory.com/entry/CS-%F0%9F%91%A8%E2%80%8D%F0%9F%92%BB-%EC%9D%BC%EA%B8%89-%EA%B0%9D%EC%B2%B4first-class-object  
[그림출처 - https://warpgate3.tistory.com]:https://warpgate3.tistory.com/entry/%EC%9E%90%EB%B0%94%EC%BD%94%EB%93%9C%EB%A1%9C-%EB%B3%B4%EB%8A%94-%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-Functional-Programming-in-Java  
