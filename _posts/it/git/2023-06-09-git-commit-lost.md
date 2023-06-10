---
layout: post
title: Git 유실된 Commit 복원하기
subtitle:
categories: GIT
tags: [GIT, GITHUB]
comments: true
published: true
---

### 📌 문제상황

사이드 프로젝트 진행중   
Rebase를 진행하던 중에 이전 버젼으로   
강제 Push를 하여 내가 작업했던 커밋이 날라가버린 경우가 생겼다.   


![image](https://github.com/MyCatlikesChuru/MyCatlikesChuru.github.io/assets/95069395/0e4ba446-ce57-4214-975f-3631f87d287a){: .align-left style="max-width: 100%"}

`9d46a0c` 커밋이 작업된 커밋이고, `c953c9c` 커밋이 Rebase한 커밋이다.    
정말 멍청하게도 `git rebase --skip` 명령어를 사용하면서 발생한 문제였다.   

그럼 원격에는 commit이 남아있는데 어떻게 다시 원복시킬 수 있을까?   

<br/>

### ✅ 문제해결

git reflog라는 명령어가 있다.    
로컬 저장소에서 HEAD의 업데이트 기록을 출력해주는 명령어이다.  

```shell
git reflog
```

위와 같이 커멘드를 실행하게되면 모든 브랜치의 이력을 볼 수 있다.    
특정 브랜치만 지정한느 것도 가능하다.


![image](https://github.com/MyCatlikesChuru/MyCatlikesChuru.github.io/assets/95069395/cc1dcfff-3984-462a-ae33-d52544ea92f2){: .align-left style="max-width: 100%"}

위와 같이 지정하고 처음에 유실했던 커밋 ID인 `9d46a0c`가 있다는 걸 확인했다.   


그리고 현재 작업위치인 HEAD의 포인터를 변경해주는    
git reset 명령어를 사용해 

```shell
git reset --hard 9d46a0c
```

와 같이 명령어를 입력하면 유실되었던 커밋을 되찾을 수 있을 것이다.   







<br/>
<br/>
<br/>

✨ 참고 블로그 
- [letmecompile님 블로그]
- [seosh817님 블로그]

[letmecompile님 블로그]:https://www.letmecompile.com/git-restore-lost-commits/
[seosh817님 블로그]: https://seosh817.tistory.com/297