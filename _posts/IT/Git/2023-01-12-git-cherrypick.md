---
layout: post
title: Git Cherry-Pick으로 Commit 옮기기
subtitle:
categories: GIT
tags: [GIT, GITHUB]
comments: true
published: true
---

### 📌 Cherry-Pick   

작업을 하던중 다른 브랜치에 Commit을 해버렸다...    

**팀원 모두가 개발 중인 브랜치** : `develop`  
**현재 내가 개발하는 브랜치** : `feat/BE/ljh`

이렇게 두가지로 운영중이었다.  

![image](https://user-images.githubusercontent.com/95069395/212029237-9092798b-deca-43ea-8a8d-d99591ca7963.png){: .align-left style="max-width: 100%"}

사진에서 볼 수 있듯이... `develop` 브랜치에 커밋을 두개나 해버린상황  
#14, #3 으로된 제목의 커밋이다..  
`feat/BE/ljh`  브랜치로 커밋을 옮겨야하는 상황이다.

해당 커밋을 복사해서 옮길 수 있는 방법이 Cherry-Pick이다.  
본격적으로 사용법을 알아보자

<br/>

### 📌 Commit Hash 값

첫번째로 내가 잘못올릴 커밋의 해쉬값을 알아야한다.  

내가 커밋을 잘못올린 브랜치(`develop`)에서  
`git log`를 입력해 커밋의 해쉬값을 알 수 있다.

```shell
commit 7525f9a220700884994e53e90babc345d199e10e (HEAD -> develop)
Author: Lee JaeHyeok <dhfif718@gmail.com>
Date:   Thu Jan 12 18:02:48 2023 +0900

    #14 - Responseder, TokenPrvider 역할 추가
    
    1. 로그인 시 응답 가능하도록 Responder 추가
    2. JWT 관련 예외발생시 Json 객체로 반환안되는 문제 수정
    3. HttpServletRequest 객체로 AccessToken 가져오도록 TokenProvider에게 역할 이전
       (추후 RefreshToken으로 AccessToken 발급시 통일성있게 사용을 위함)


commit cd49c93bd4905f6d1f4a19d8438ab4c4d6b6d15b
Author: Lee JaeHyeok <dhfif718@gmail.com>
Date:   Thu Jan 12 17:58:46 2023 +0900

    #3 - LoginResponseDto 추가
    
    1. 로그인 시 응답 가능하도록 ResponseDto 추가

```

나는 총 2개의 커밋을 했고  
첫번째 커밋은 `7525f9a220700884994e53e90babc345d199e10e`로  
`7525f9a`와 같이 7자리로 표현할 수도 있다.

두번째 커밋은 `cd49c93bd4905f6d1f4a19d8438ab4c4d6b6d15b`이다.

이 두가지 커밋을 이제 `feat/BE/ljh`라는  
브랜치로 옮기고 싶은 상황이다.

<br/>  

### 📌 Cherry-Pick 사용하여 커밋 옮기기

먼저 브랜치 체크아웃을 진행하자.
```shell
git checkout feat/BE/ljh
```

<br/>  

이제 하나씩 Cherry-Pick 명령어를 사용해 옮겨보자
```shell
git cherry-pick cd49c93bd4905f6d1f4a19d8438ab4c4d6b6d15b
```
를 입력해주면되고, 만약 실행하였을때 정상적으로   
완료가되면 이상이 없겠지만, 혹시나   

아래와 같이 conflict가 발생하게 될 수 있다.

```shell
Auto-merging server/src/main/java/codestates/frogroup/indiego/domain/member/entity/dto/MemberDto.java
CONFLICT (content): Merge conflict in server/src/main/java/codestates/frogroup/indiego/domain/member/entity/dto/MemberDto.java
error: could not apply cd49c93... #3 - LoginResponseDto 추가
hint: After resolving the conflicts, mark them with
hint: "git add/rm <pathspec>", then run
hint: "git cherry-pick --continue".
hint: You can instead skip this commit with "git cherry-pick --skip".
hint: To abort and get back to the state before "git cherry-pick",
hint: run "git cherry-pick --abort".
```
hint 안내에 따라 진행할수가 있다.   
우선 CONFLICT가 발생한 부분을 로그로 알려준다.  
나와 같은 경우는 `MemberDto` 충돌이 나왔다고 위에 나와있다.

해당 클래스로 가면 인텔리제이에서 충돌난 부분을 표시해준다.   
충돌난 부분을 코드를 수정해 우선 해결해주고  
`git add .`를 진행후 `git cherry-pick --continue`를 진행하면된다.  
마치 rebase하는 과정과 매우 흡사하다.

충돌부분이 전부 해결되었으면 nano편집기로 저장할건지를 물어본다.  
ctrl + s, ctrl + x 를 눌러줘 저장을해준다.

<br/>

완료를 했다면.. `git log`를 입력하면   
`develop`브랜치에 있던 커밋이 `feat/BE/ljh`로 복사된 것을   
볼 수 있을 것이다 !!!!!

```shell
commit a5d3ef8d6110d838c896ba3125c351eb5b611623 (HEAD -> feat/BE/ljh)
Author: Lee JaeHyeok <dhfif718@gmail.com>
Date:   Thu Jan 12 17:58:46 2023 +0900

    1. 로그인 시 응답 가능하도록 ResponseDto 추가

```

해쉬값을 보아하니 복사보다는 새로 만들어진 느낌이다.   
이렇게 옮기려고하는 커밋을 모두 옮기면 된다.  


<br/>  


### 📌 잘못올린 커밋 삭제  

`develop` 브랜치에 잘못올린 커밋을 삭제해줘야한다.   
Cherry-Pick을 했다고 자동으로 삭제가되진 않는다.

`develop` 브랜치로 체크아웃을하여

```shell
git reset HEAD^
```

를 입력하면 가장 최근인 HEAD의 커밋이 삭제되어진다.   

혹시나 인텔리제이를 사용하고 있다면  
하단에 Git에 들어가서

![image](https://user-images.githubusercontent.com/95069395/212040809-3a14a459-78b8-42a4-9558-b42c83cd9f30.png){: .align-left style="max-width: 100%"}

삭제하려고 하는 커밋에 오른쪽 마우스를 클릭해  
Drop Commit을 눌러주면 된다.




<br/>
<br/>
<br/>
