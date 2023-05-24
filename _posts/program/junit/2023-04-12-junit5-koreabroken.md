---
layout: post
title: "[Junit5] ResultActions 응답 객체 한글 깨짐 문제"
subtitle: Test 코드 에러핸들링
categories: Junit5
tags: [Junit5, TestCode]
comments: true
published: true
---

**배경**

- Intergration 테스트 코드 작성 중 Dto객체의 담긴 데이터를 검증 진행중 한글 깨짐 현상을 발견

```
expected: "ë°íë§ ë°°í¸ ëí êµ¬í´ì"
but was: "바하마 배편 동행 구해요"
```

<br/>

**오류 내용**

- 테스트 코드 실행 시 response 값이 깨져서 나오는 현상 발생
- `mockMvc.perform(post(url))` 진행시 반환값인  `ResultActions actions`의
  `actions.andReturn().getResponse().getContentAsString();` 반환할 경우    
  한글이 깨져서 나오는 Content를 String으로 가져오는 현상을 발견.
- 한글이 깨진상태로 Jackson 라이브러리를 통해 역직렬화 진행시 깨진상태로
  테스트 케이스를 진행하기 때문에 테스트 케이스를 통과하지 못함.
- 현재 ResultActions를 처리하기 위한 메서드로 아래와 같이 객체를 가져옴.

```java
private static String resultActionsToResponseAsString(ResultActions actions) throws UnsupportedEncodingException {
        String response = actions.andReturn()
                .getResponse()
                .getContentAsString()
                .substring(8);
        return response.substring(0, response.length() - 1);
}
```

<br/>

**해결 방법**

- .getContentAsString(); 메서드에 StandardCharsets.UTF_8을 추가하여 오류 해결

```java
private static String resultActionsToResponseAsString(ResultActions actions) throws UnsupportedEncodingException {
        String response = actions.andReturn()
                .getResponse()
                .getContentAsString(StandardCharsets.UTF_8) // <- 이부분이 추가되어짐
                .substring(8);
        return response.substring(0, response.length() - 1);
}
```