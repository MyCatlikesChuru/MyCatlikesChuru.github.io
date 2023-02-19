---
layout: post
title: "[Spring] Security Test 코드 작성시 UserDetails 설정"
subtitle: 
categories: Spring
tags: [Spring, JUnit]
comments: true
published: true
---

오늘 겪은 문제를 정리해보려한다.  
Spring Security + JWT + OAuth2 인증,인가를 사용하여  
정상적으로 API 작동까지 Postman으로 마친 상태이다.  

문제는 Rest Docs를 적용하기 위해  
Test 코드를 작성중 Spring Security 문제와 부딪히게 된 것 !!  

<br/>

#### 📌 문제 발생 (403 Forbidden)

기존과 동일하게 POST 요청을 보내 보았다.
```java
    @Test
    @DisplayName("질문 작성 API : createQuestion")
    void createQuestion() throws Exception {

        QuestionPostDto questionPostDto = (QuestionPostDto) StubData.MockQuestion.getRequestBody(HttpMethod.POST);
        Question question = StubData.MockQuestion.getSingleResponseBody(1L);
        List<Tag> tagList = StubData.MockTag.getSingleResponseBody();
        Member member = StubData.MockMember.getSingleResponseBody(1L);

        given(tagService.findTags(Mockito.any(QuestionPostDto.class))).willReturn(tagList);
        given(memberService.findByMember(Mockito.anyLong())).willReturn(member);
        given(questionService.postQuestion(Mockito.any(Question.class))).willReturn(question);
        String content = toJsonContent(questionPostDto);
        ResultActions actions = mockMvc.perform(postRequestBuilder(getUrlCreateQuestion(), content));

        actions
                .andExpect(status().isOk());
    }
```
복잡해보일 수 있겠지만... 단순히 메서드화 시켜서  
여러군대에서 범용성있게 사용하려고 나타낸 것이다.  

중요한 부분은 `mockMvc.perform();`에서 Json 객체를 담고  
Url을 만들어 보내준다. 내용을 살펴 보면   
```java
public interface ControllerTestHelper<T> {

    ...

    default RequestBuilder postRequestBuilder(String url,
                                              String content) {
        return post(url)
                .accept(MediaType.APPLICATION_JSON)
                .contentType(MediaType.APPLICATION_JSON)
                .content(content);
    }
    ...
}
```
이렇게 단순히 인터페이스로 만들어 사용하고 있다.  
URL 같은 경우는 다른 클래스에서 `getUrlCreateQuestion()`  
메서드를 이용해 만들어 넘기도록 했다.  

메세지는 아래와 같이 요청과 응답이 왔다.

```java
MockHttpServletRequest:
      HTTP Method = POST
      Request URI = /questions/ask/post
       Parameters = {}
          Headers = [Content-Type:"application/json;charset=UTF-8", Accept:"application/json", Content-Length:"187"]
             Body = {"email":"dhfif718@gmail.com","questionTitle":"질문 제목 입니다.","questionProblemBody":"질문 내용 1","questionTryOrExpectingBody":"질문 내용 2","tag":[{"tagName":"java"}]}
    Session Attrs = {org.springframework.security.web.csrf.HttpSessionCsrfTokenRepository.CSRF_TOKEN=org.springframework.security.web.csrf.DefaultCsrfToken@25f14e93}
```


```java
MockHttpServletResponse:
           Status = 403
    Error message = Forbidden
          Headers = [X-Content-Type-Options:"nosniff", X-XSS-Protection:"1; mode=block", Cache-Control:"no-cache, no-store, max-age=0, must-revalidate", Pragma:"no-cache", Expires:"0", X-Frame-Options:"DENY"]
     Content type = null
             Body = 
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```
여기서 이제 403 `Forbidden` 문제가 발생했고...  
어떻게 해결을 할지 찾아 보던 중  `@WithMockUser`어노테이션을  
이용해 인증을 통과시켜 줄 수 있고, `mockMvc.perform()` 메서드에  
`post`호출시 `.with(csrf();`를 붙여주면 csrf 토큰을 생성할 수 있다.    

<br/>

#### 📌 @WithMockUser, with(csrf()); 설정

```java
    @Test
    @WithMockUser
    @DisplayName("질문 작성 API : createQuestion")
    void createQuestion() throws Exception {

        QuestionPostDto questionPostDto = (QuestionPostDto) StubData.MockQuestion.getRequestBody(HttpMethod.POST);
        Question question = StubData.MockQuestion.getSingleResponseBody(1L);
        List<Tag> tagList = StubData.MockTag.getSingleResponseBody();
        Member member = StubData.MockMember.getSingleResponseBody(1L);

        given(tagService.findTags(Mockito.any(QuestionPostDto.class))).willReturn(tagList);
        given(memberService.findByMember(Mockito.anyLong())).willReturn(member);
        given(questionService.postQuestion(Mockito.any(Question.class))).willReturn(question);
        String content = toJsonContent(questionPostDto);
        ResultActions actions = mockMvc.perform(postRequestBuilder(getUrlCreateQuestion(), content));

        actions
                .andExpect(status().isOk());
    }
```
```java
public interface ControllerTestHelper<T> {

    ...

    default RequestBuilder postRequestBuilder(String url,
                                              String content) {
        return post(url)
                .accept(MediaType.APPLICATION_JSON)
                .contentType(MediaType.APPLICATION_JSON)
                .content(content)
                .with(csrf());
    }
    ...
}
```
위에 클래스에서 `@WithMockUser`를 테스트 메서드에 붙여주고  
`mockMvc.perform()`의 `post()`의 `.with(csrf());`를 붙여 주었다.  

테스트를 실행 시켜 보면  
아래와 같은 요청 응답 메세지가 만들어진다.
```java
MockHttpServletRequest:
      HTTP Method = POST
      Request URI = /questions/ask/post
       Parameters = {_csrf=[b9be6017-6b52-464b-b5e1-6f480569099b]}
          Headers = [Content-Type:"application/json;charset=UTF-8", Accept:"application/json", Content-Length:"187"]
             Body = {"email":"dhfif718@gmail.com","questionTitle":"질문 제목 입니다.","questionProblemBody":"질문 내용 1","questionTryOrExpectingBody":"질문 내용 2","tag":[{"tagName":"java"}]}
    Session Attrs = {SPRING_SECURITY_CONTEXT=SecurityContextImpl [Authentication=UsernamePasswordAuthenticationToken [Principal=org.springframework.security.core.userdetails.User [Username=user, Password=[PROTECTED], Enabled=true, AccountNonExpired=true, credentialsNonExpired=true, AccountNonLocked=true, Granted Authorities=[ROLE_USER]], Credentials=[PROTECTED], Authenticated=true, Details=null, Granted Authorities=[ROLE_USER]]]}
```
Parameters를 확인해보면 csrf로 토큰이 생긴것을 확인할 수 있다.   
설정하지 않고 하였을때는 위에의 Parameters를 보면 알 수 있듯이  
비어있는 모습을 볼 수 있다.

하지만 여기서 또 하나의 문제가 발생한다.  
UserDetails의 설정이 되지 않아  

```java
java.lang.ClassCastException
```
클래스 타입 관련 에러가 발생했고...
```java
MockHttpServletResponse:
           Status = 500
    Error message = null
          Headers = [Vary:"Origin", "Access-Control-Request-Method", "Access-Control-Request-Headers", Content-Type:"application/json", X-Content-Type-Options:"nosniff", X-XSS-Protection:"1; mode=block", Cache-Control:"no-cache, no-store, max-age=0, must-revalidate", Pragma:"no-cache", Expires:"0", X-Frame-Options:"DENY"]
     Content type = application/json
             Body = {"status":500,"message":"Internal Server Error","fieldErrors":null,"violationErrors":null}
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```
응답 메세지에는 Error Handler로 작업한  
Body가 담겨져 500번 상태코드로 오류가 발생한 것을 볼 수있다..  

<br/>

#### 📌 왜 예외가 발생했을까?

UserDetails를 테스트 코드 패키지에 설정하지 않으면

```text
java.lang.ClassCastException: class org.springframework.security.core.userdetails.User cannot be cast to class com.example.stackoverflowclone.global.security.auth.dto.TokenPrincipalDto (org.springframework.security.core.userdetails.User and com.example.stackoverflowclone.global.security.auth.dto.TokenPrincipalDto are in unnamed module of loader 'app')
```
위와 같은 에러가 발생할 수 있다. 그 이유는  
기존에 시큐리티 프로그램을 작성할 때  
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new LoginMemberIdResolver());
        resolvers.add(new LoginMemberEmailResolver());
    }

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry
                .addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("*");
    }
}
```
`WebMvcConfigurer`를 상속받아 `addArgumentResolvers()`를   
오버라이딩해 어노테이이션을 만들어 구현해주었었다.

```java
public class LoginMemberIdResolver implements HandlerMethodArgumentResolver {

    ...
    
    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
                                  NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {

        Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();

        if (principal == "anonymousUser") {
            return -1L;
        }

        TokenPrincipalDto castedPrincipal = (TokenPrincipalDto) principal; // <---- ClassCast 예외 발생 지점

        return castedPrincipal.getId();
    }
}
```
해당 클래스에서 예외 발생지점은 위에 주석처리한 부분이다.  
Test 코드를 실행할 경우에  
기본적으로 `User` 객체가 담겨져서 오게된다.
```java
org.springframework.security.core.userdetails.User [Username=user, Password=[PROTECTED], Enabled=true, AccountNonExpired=true, credentialsNonExpired=true, AccountNonLocked=true, Granted Authorities=[ROLE_USER]]
```
와 같은 형태로 객체가 출력되었다.

나는 `TokenPrincipalDto`라는 클래스를 만들어  
```java
@Getter
@Setter
@AllArgsConstructor
public class TokenPrincipalDto {
    private long id;
    private String email;
}
```
```java
Authentication authentication = new UsernamePasswordAuthenticationToken(new TokenPrincipalDto(id, email), null, authorities);
```
과 같은 방식으로 Principal을 만들어 주었는데  
테스트 코드에서는 해당 부분이 설정되어 있지 않아 발생하는  
ClassCastException 타입변환 예외 였다.

<br/>  

#### 📌 Test 코드 작성시 UserDetails 하기  

그렇다면 테스트 환경에서도 동일하게  
Principal에 `TokenPrincipalDto`객체를 만들어 넣어주면 된다.  

만드는 방법을 차근차근 알아보자  
```java
@Retention(RetentionPolicy.RUNTIME)
@WithSecurityContext(factory = WithMockCustomUserSecurityContextFactory.class)
public @interface WithMockCustomUser {
    String  username() default "dhfif718@naver.com";

}
```
우선 어노테이션을 만들어 커스터 마이징해줬다.  
우선 username에 대한 정보를 강제로 입력해 두었다.  

```java
public class WithMockCustomUserSecurityContextFactory implements WithSecurityContextFactory<WithMockCustomUser> {
    @Override
    public SecurityContext createSecurityContext(WithMockCustomUser customUser) {
        SecurityContext context = SecurityContextHolder.createEmptyContext();
        List<GrantedAuthority> grantedAuthorities = new ArrayList();
        grantedAuthorities.add(new SimpleGrantedAuthority("USER"));
        UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(
                new TokenPrincipalDto(1L , customUser.username()), null, grantedAuthorities);
        authentication.setDetails(customUser.username());
        context.setAuthentication(authentication);
        return context;
    }
}
```
`WithSecurityContextFactory`를 상속받아  
`createSecurityContext();`를 구현해준다음에  

우리가 테스트하려고 했던 메서드에  
```java
    @Test
    @DisplayName("질문 작성 API : createQuestion")
    @WithMockCustomUser
//    @WithMockUser
    void createQuestion() throws Exception {

        QuestionPostDto questionPostDto = (QuestionPostDto) StubData.MockQuestion.getRequestBody(HttpMethod.POST);
        Question question = StubData.MockQuestion.getSingleResponseBody(1L);
        List<Tag> tagList = StubData.MockTag.getSingleResponseBody();
        Member member = StubData.MockMember.getSingleResponseBody(1L);

        log.info("questionPostDto = {}", questionPostDto);

        given(tagService.findTags(Mockito.any(QuestionPostDto.class))).willReturn(tagList);
        given(memberService.findByMember(Mockito.anyLong())).willReturn(member);
        given(questionService.postQuestion(Mockito.any(Question.class))).willReturn(question);
        String content = toJsonContent(questionPostDto);
        ResultActions actions = mockMvc.perform(postRequestBuilder(getUrlCreateQuestion(), content));

        actions
                .andExpect(status().isOk());
    }
```
기존에 쓰던 `@WithMockUser`를 주석처리해주고, 커스터마이징해서 만든  
어노테이션은 `@WithMockCustomUser`를 테스트 메서드에 붙여주면  
테스트가 정상적으로 진행되는 모습을 볼 수 있을 것이고  

MockHttpServletRequest 요청에  
Session Attrs에 아래와 같이 우리가 만든  
`TokenPrincipalDto` 객체로 만들어진 모습을 볼 수 있다.

```java
Session Attrs = {
        SPRING_SECURITY_CONTEXT=SecurityContextImpl 
        [Authentication=UsernamePasswordAuthenticationToken 
            [Principal=com.example.stackoverflowclone.global.security.auth.dto.TokenPrincipalDto@3dd591b9, Credentials=[PROTECTED],Authenticated=true, Details=dhfif718@naver.com, Granted Authorities=[USER]]
        ]
}
```

<br/>
<br/>
<br/>


[Test코드 작성시 UserDetails 참고 레퍼런스], [csrf 설정 참고 레퍼런스]


[Test코드 작성시 UserDetails 참고 레퍼런스]: https://sejoung.github.io/2020/11/2020-11-19-spring-security/#spring-security-test-code-%EC%9E%91%EC%84%B1%EC%8B%9C-UserDetails%EA%B0%80-%ED%95%84%EC%9A%94%ED%95%A0%EB%95%8C
[csrf 설정 참고 레퍼런스]: https://jungguji.github.io/2020/06/09/Spring-Controller-Test%EC%8B%9C-CSRF-%EC%84%A4%EC%A0%95/