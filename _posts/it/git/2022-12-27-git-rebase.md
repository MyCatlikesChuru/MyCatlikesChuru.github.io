---
layout: post
title: Git pull rebase 사용해 병합하기
subtitle:
categories: GIT
tags: [GIT, GITHUB]
comments: true
published: true
---


자주 사용하는 `git pull --rebase`에 대해 정리해보려한다.

협업을 같이하다보면 지속적으로 업데이트를 하기 위해서  
pull을 하거나 remote를 가져오기위해 fetch를 진행한다.

`dev/back` 이라는 브랜치가 백엔드끼리  
통합을 해서 사용하는 브랜치라고 가정하자.

내가 작업하는 브랜치는 `feat/BE/security`라고 하였을때  
누군가 `dev/back`에 작업을 하고 Pull Reqeust하여 Merge를 했다고  
가정을 해보면. 현재 `dev/back` 브랜치의 프로그램이 가장 최신본이다.

내가 작업하고있는 브랜치를 최신화 시키고 싶을때  
`dev/back`에 있는 브랜치를 Pull해오면 된다.

<br/>

❓ 하지만 다른 사람이 작업한 부분과, 내가 작업한 부분이 겹쳤다면 ❓

```shell
git pull origin dev/back
```
위와 같이 명령을 해보아도 충돌이 나면서 작업이 진행 되지 않을 것이다.

그럴 경우에는
```shell
git pull origin dev/back --rebase
```
![image](https://user-images.githubusercontent.com/95069395/217531072-d4b655ed-f047-411b-af49-a08eb274b0b3.png){: .align-left style="max-width: 100%"}

위와 같이 입력을 해주면, 파일이 합쳐진 것이고  
이제 합쳐진 파일에서 겹친 부분을 수정을 해주면 된다.   
참고로 브랜치 이름이 e1604ec 이렇게 바뀐 모습을 볼 수 있다.

수정을 다완료 하고 나면

```shell
git rebase --continue
```

![image](https://user-images.githubusercontent.com/95069395/217531087-2ad4738a-4cc0-408a-a48b-6c3dbdda892b.png){: .align-left style="max-width: 100%"}

를 입력해줘서 다음 rebase할 커밋으로 넘어가주면 된다.  
만약 다음에 수정할게 없다면

<br/>

아래와 같이 원래 브랜치 `feat/BE/security`로 바뀐 모습을 볼 수 있다.   
이제 rebase완료된 파일을 `feat/BE/security` 브랜치로 올려주면 된다.

```shell
git push origin feat/BE/security
```
를 입력해주면 push를 할 수 있는데

아래와 같은 에러에 마주하였을 경우에는

![image](https://user-images.githubusercontent.com/95069395/217531097-98d18b4c-42a4-430e-9832-00bd654669c9.png){: .align-left style="max-width: 100%"}

합쳐진게 전부 잘 수정했다고 가정하면
```shell
git push origin feat/BE/security -ff
```
를 입력해 레파지토리에 강제로 push할 수 있다.

<br/>
<br/>
<br/>




