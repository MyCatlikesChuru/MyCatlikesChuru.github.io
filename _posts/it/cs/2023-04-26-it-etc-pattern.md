---
layout: post
title: "디자인 패턴 - 전략 패턴 [Java 예제]"
subtitle: 면접을 위한 CS 전공지식 노트
categories: CS
tags: [ComputerScience]
comments: true
published: true 
---

## 전략 패턴  

**전략 패턴(strategy pattern)은** 정책 패턴(policy pattern)이라고도 하며   
객체의 행위를 바꾸고 싶은 경우 직접 수정하지 않고 전략이라고 부르는 캡슐화한 알고리즘을   
컨택스트 안에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴이다.   

> 컨택스트?  
> > 프로그래밍에서의 컨텍스트는 상황, 맥락, 문맥을 의미한다.    
개발자가 어떠한 작업을 완료하는 데 필요한 모든 관련 정보를 말한다.

<br/>   

---

정의로만 보면 이해하기가 쉽지 않다.    
예제를 통해 살펴보자   

### 전략 패턴 적용 ❌

```java
public class Main {
    public static void main(String[] args) {
        List<Integer> data = List.of(1, 5, 3, 4, 2);

        // 오름차순 정렬
        List<Integer> sortedData = data.stream()
                .sorted()
                .collect(Collectors.toList());
        System.out.println(sortedData); // Output: [1, 2, 3, 4, 5]

        // 내림차순 정렬
        sortedData = data.stream()
                .sorted(Collections.reverseOrder())
                .collect(Collectors.toList());
        System.out.println(sortedData); // Output: [5, 4, 3, 2, 1]
    }
}
```

해당 코드는 정렬을 진행하는 코드이다.    
정렬이라는 전략에 오름차순과 내림차순이 있지만 캡슐화가되어있지도 않고  
정렬이라는 행위를 바꾸기위해서는 해당 코드를 복사해 붙여넣어줘야하므로   
다른 메서드에서 사용한다면 중복된 정렬 알고리즘 코드가 들어가게될 것 이다.   

<br/>   

### 전략 패턴 적용 ⭕️  

```java
interface SortingStrategy {
    List<Integer> sort(List<Integer> data);
}

class AscendingSortingStrategy implements SortingStrategy {
    @Override
    public List<Integer> sort(List<Integer> data) {
        data.sort(Integer::compareTo);
        return data;
    }
}

class DescendingSortingStrategy implements SortingStrategy {
    @Override
    public List<Integer> sort(List<Integer> data) {
        data.sort(Integer::compareTo);
        Collections.reverse(data);
        return data;
    }
}

class Sorter {
    private SortingStrategy sortingStrategy;

    public void setSortingStrategy(SortingStrategy sortingStrategy) {
        this.sortingStrategy = sortingStrategy;
    }

    public List<Integer> sort(List<Integer> data) {
        return sortingStrategy.sort(data);
    }
}

public class Main {
    public static void main(String[] args) {
        List<Integer> data = List.of(1, 5, 3, 4, 2);
        Sorter sorter = new Sorter();

        // 오름차순 정렬
        sorter.setSortingStrategy(new AscendingSortingStrategy());
        List<Integer> sortedData = sorter.sort(data);
        System.out.println(sortedData); // Output: [1, 2, 3, 4, 5]

        // 내림차순 정렬
        sorter.setSortingStrategy(new DescendingSortingStrategy());
        sortedData = sorter.sort(data);
        System.out.println(sortedData); // Output: [5, 4, 3, 2, 1]
    }
}
```

해당 코드에서는 오름차순과 내림차순이라는 전략을 클래스로 구현하였고   
`SortingStrategy`라는 인터페이스를 상속하여 `sort()`메서드를 구현하고 있다.  

`Sorter` 클래스에서 `setSortingStrategy()`에 매개변수로 전략을 담은   
`SortingStrategy` 구현체를 담아주게되면 해당 구현체에 따라 오름차순,내림차순 정렬 정책이 정해진다.   

또한 `private SortingStrategy sortingStrategy;`으로 정렬전략은 캡슐화가 되어있다.  

최종적으로 Main 메서드에서 `Sorter` 인스턴스를 생성해 `.setSortingStrategy()`를 통해   
오름차순, 내림차순 클래스를 전략에 맞게 생성해준다면 전략패턴이 적용된 코드라고 할 수 있다.   

---   

<br/>   

### Spring에서 전략패턴?


그럼 Java 진영에서 사용하는 프레임워크인   
SpringFramework에서는 어떤걸 전략패턴이라고 할 수 있을까?   

우리가 자주 구현한 Spring Security에서 `UserDetailsService`를 예로 들을 수 있을 것이다.  


- **예제**

```java
public interface UserDetailsService {

    /**
     * Locates the user based on the username. In the actual implementation, the search
     * may possibly be case sensitive, or case insensitive depending on how the
     * implementation instance is configured. In this case, the <code>UserDetails</code>
     * object that comes back may have a username that is of a different case than what
     * was actually requested..
     * @param username the username identifying the user whose data is required.
     * @return a fully populated user record (never <code>null</code>)
     * @throws UsernameNotFoundException if the user could not be found or the user has no
     * GrantedAuthority
     */
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;

}

```

해당 인터페이스를 가지고 사용자 이름을 기반으로 사용자 정보를 가져오는   
`loadUserByUsername`메서드를 정의하여 사용한다.   


해당 인터페이스를 상속받아 구현하여, 각기 다른 방식으로   
사용자 인증을 처리하는 전략 클래스를 만들 수있다.

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Optional<User> user = userRepository.findByUsername(username);
        if (!user.isPresent()) {
            throw new UsernameNotFoundException("User not found");
        }
        return new CustomUserDetails(user.get());
    }
}
```

위의 예제로는 `loadUserByUsername` 메서드는 사용자의 이름을 기반으로   
데이터베이스에서 사용자정보를 가져와 `CustomUserDetails` 객체로 반환하게된다.   

`CustomUserDetailsService`라는 우리가 만든 클래스는   
위와 같이 데이터베이스에서 사용자정보를 이용한 전략을 가지고 있지만   

만약 데이터베이스가 아니라 다른 전략이 생긴다고 가정하면
`UserDetailsService`를 상속받아 클래스를 만들어 코드를 작성해
`CustomUserDetails`를 반환해주면 다른 전략을 사용할 수 있게된다.  ㄴ

즉, 전략패턴이 적용된 예제로 코드의 재사용성과 유연성을 높일 수 있게된다.   


<br/>   

### 전략패턴의 장점   

위에서 볼 수 있듯이 전략패턴을 적용하게되면 새로운 전략이 추가된다고 가정할때   
OCP(개방 폐쇠의 원칙)을 지키면서 설계가 가능해진다. 또한 다른 서비스에서 정렬을 사용한다면   
`Sorter`클래스 `.setSortingStrategy()`를 이용해서 정렬전략을 사용하기 떄문에   
캡슐화한 알고리즘이 컨택스트 안에서 바뀌게되어 중복된 알고리즘을 작성할 필요가 없게된다.  

> OCP (개방 폐쇠의 원칙)
> > 기존의 코드를 변경하지 않으면서, 기능을 추가할 수 있도록 설계가 되어야 한다는 원칙

<br/>   

### 전략패턴의 단점   

디자인 패턴에 익숙하다면 전략패턴의 코드가 편하겠지만, 일반적으로 짧은 코드에서는   
전략패턴을 적용하지않은 코드가 보기 편할 수 있다.   

즉, 모든 상황에서 전략패턴이 사용되는 것은 유용하지 않다. 컨텍스트에 적용되는 알고리즘이  
하나이거나 두개인 경우는 분기를 타는 것이 편한 경우도 있다. 그러나 요구사항의 변경으로   
변경될 여지가 있고 변화의 형태가 다양함이 어느정도 보장될 때 전략패턴을 고려해보는 것이 좋을 것 같다.   





<br/>  
<br/>

