---
layout: post
title: "[Java] Object Mapper 역직렬화 문제 (final 키워드로 인한 기본생성자 사용불가)"
subtitle: Test 코드 에러핸들링
categories: Java
tags: [Java, TestCode]
comments: true
published: true
---

**배경**

- 테스트 코드 작성중 String → Dto 객체로  역직렬화하는 과정에서 실패케이스 발생

```java
public class BuddyDto {

    @Getter
    @Builder
    @AllArgsConstructor
    public static class ResponseRecruitment {
        private final String title;
        private final String content;
        private final String travelNationality;
        private final LocalDate travelStartDate;
        private final LocalDate travelEndDate;
        private final Long viewCount;
        private final Long commentCount;
        private final String nickname;
        private final String memberImage;
    }
}
```

<br/>

**오류 내용**

- 오류 내용은 기본 생성자와 같은 생성자가 존재하지 않는다는 에러가 발생.

```java
Cannot construct instance of `com.frog.travelwithme.domain.buddyrecuirtment.controller.dto.BuddyDto$ResponseRecruitment` (no Creators, like default constructor, exist): cannot deserialize from Object value (no delegate- or property-based Creator)
```

- 현재 DTO에 final로 정의하여 @NoArgsConstructor를 사용할 수 없는 상황
  
<br/>

**해결 방법**

- @JsonCreator, @JsonProperty을 이용하여 ObjectMapper가 역직렬화를 실행할 수 있도록
  어노테이션을 붙여준다.

```java
public class BuddyDto {
		@Getter
    @Builder
    public static class ResponseRecruitment {

        private final String title;
        private final String content;
        private final String travelNationality;
        private final LocalDate travelStartDate;
        private final LocalDate travelEndDate;
        private final Long viewCount;
        private final Long commentCount;
        private final String nickname;
        private final String memberImage;

        @JsonCreator
        private ResponseRecruitment(@JsonProperty("title") String title,
                                    @JsonProperty("content") String content,
                                    @JsonProperty("travelNationality") String travelNationality,
                                    @JsonProperty("travelStartDate") LocalDate travelStartDate,
                                    @JsonProperty("travelEndDate") LocalDate travelEndDate,
                                    @JsonProperty("viewCount") Long viewCount,
                                    @JsonProperty("commentCount") Long commentCount,
                                    @JsonProperty("nickname") String nickname,
                                    @JsonProperty("memberImage") String memberImage){
            this.title = title;
            this.content = content;
            this.travelNationality = travelNationality;
            this.travelStartDate = travelStartDate;
            this.travelEndDate = travelEndDate;
            this.viewCount = viewCount;
            this.commentCount = commentCount;
            this.nickname = nickname;
            this.memberImage = memberImage;
        }
    }
}
```

<br/>

✨ 참고 블로그

- [https://velog.io/@park2348190/Jackson-ObjectMapper에서-기본-생성자-없이-Deserialization-하기](https://velog.io/@park2348190/Jackson-ObjectMapper%EC%97%90%EC%84%9C-%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%97%86%EC%9D%B4-Deserialization-%ED%95%98%EA%B8%B0)

