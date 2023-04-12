---
layout: post
title: "[Spring] Generic을 활용한 Class 타입 매개변수로 전달하기"
subtitle: Test 코드 리펙토링
categories: Spring
tags: [Spring, Java]
comments: true
published: true
---

### Generic - Class 타입을 매개변수로 전달   

#### 리펙토링 배경

테스트 코드를 작성 중 Jackson 라이브러리 ObjectMapper를 사용하여   
객체를 직렬화, 역직렬화를 진행하였다.   

해당 부분은 자주 사용하는 부분이기 때문에 Utils라는 클래스로 만들어   
관리하면 어떨까? 하는 생각에 클래스를 만들었고   

```java
public class ObjectMapperUtils {
    private static final ObjectMapper objectMapper = new ObjectMapper();

    // 코드 생략 ...

    public static LoginResponse actionsSingleResponseToLoginDto(ResultActions actions) throws Exception {
        String response = resultActionsToResponseAsString(actions);
        return objectMapper.readValue(response, LoginResponse.class);
    }

    public static MemberDto.Response actionsSingleResponseToMemberDto(ResultActions actions) throws Exception {
        String response = resultActionsToResponseAsString(actions);
        return objectMapper.registerModule(new JavaTimeModule()).readValue(response, MemberDto.Response.class);
    }
        
    // 코드 생략 ...
}
```

대략 위와 같은 형식으로 코드를 작성하였다.     
코드에서 주요 핵심 포인트는 `.readValue(response, 클래스타입);` 메서드 부분이다.   
메서드가 2개가 존재하는데 actions를 매개변수로 넘겨 String값 response를 받고   
ObjectMapper를 이용해 역직렬화를 시켜주는 동일한 기능의 메서드들이다.   

여기서 `LoginResponse.class`과 `MemberDto.Response.class` 부분과    
반환 타입 `LoginResponse`과 `MemberDto.Response` 부분만 통일화 시켜주면   
메서드 하나로 리펙토링이 가능하다고 생각했다.

<br/>   

#### 리펙토링 진행   

위와 같은 이유로 Class타입을 매개변수로 넘겨주고, 매개변수와 반환값을 제너릭으로   
어떤 타입이든 메서드 호출시 정하도록 해주면 되겠다고 생각하여 아래와 같이 리펙토링 하였다.   

```java
public class ObjectMapperUtils {
    private static final ObjectMapper objectMapper = new ObjectMapper();

    // 코드 생략 ...
    
    public static <T> T actionsSingleToDto(ResultActions actions, Class<T> responseClass) throws Exception {
        String response = resultActionsToResponseAsString(actions);
        return objectMapper.registerModule(new JavaTimeModule()).readValue(response, responseClass);
    }

    // 코드 생략 ...
    
}
```
코드에서 보면 알 수 있듯이 `Class<T>`를 이용해 매겨변수를 타입으로 넘겨주었고   
반환타입을 `<T> T`로 지정하여 매개변수를 넘길때 클래스의 타입을 반환하게 하였다.   

<br/>

실제로 메서드를 호출하는 테스트 코드는 아래와 같이 작성하였다.

```java
@Slf4j
class BuddyRecruitmentIntegrationTest extends BaseIntegrationTest {
    
    // 코드 생략 ...
    
    @Test
    @DisplayName("동행 작성 테스트")
    void BuddyRecruitmentIntegrationTest1() throws Exception {
       
        // ... 코드 생략 
        
        BuddyDto.ResponseRecruitment response = ObjectMapperUtils.actionsSingleToDto(actions,
                BuddyDto.ResponseRecruitment.class);
        
        // ... 코드 생략 
    }

    // 코드 생략 ...
    
}
```  
여기서 주목할 곳은 아래의 2번쨰 매개변수를   
`BuddyDto.ResponseRecruitment.class` 클래스 타입으로  
전달하게된다는 점이다. 이렇게 여러개의 메서드를 계속 늘려나갈 상황이 생겼는데    
제너릭을 활용해 하나의 메서드로 Utils 클래스의 불필요한 중복 메서드를 제거하였다.


<br/>
<br/>

