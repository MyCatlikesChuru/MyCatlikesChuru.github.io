---
layout: post
title: Git 이전 Commit Message 수정하기
subtitle:
categories: GIT
tags: [GIT, GITHUB]
comments: true
published: true
---

### 📌 CLI로 변경  

만약 이전에 커밋메세지를 실수로 잘못 올렸다고할 경우  

```shell
git rebase -i HEAD~3        
```
를 입력해서 커밋메세지가 수정이 가능하다.   
`~3`은 가장최신부터 수정하고자하는 커밋 수량이다   

Pick이라고 되어있는 부분을 reword로 변경한 다음   
커밋메세지를 수정해주면 반영된다.  


<br/>  

### 📌 IntelliJ로 변경

IDE를 사용하면 vi,nano 편집기를 이용하는 것보다  
훨씬 수월하게 작업할 수 있다.  

하단의 Git 탭에서 커밋메세지를 변경하고자하는  
커밋을 눌러 오른쪽 클릭을하자

![image](https://user-images.githubusercontent.com/95069395/212041524-5d07ce20-07d1-4299-96e0-866fc2857dfb.png){: .align-left style="max-width: 100%"}

`Interactively Rebase from Here`를 눌러주자

![image](https://user-images.githubusercontent.com/95069395/212041533-03319280-6850-4b08-8cfe-f8918c7013d8.png){: .align-left style="max-width: 100%"}

그러면 Rebasing Commits 팝업이 노출되는데   
원하는 커밋 메시지 이름으로 변경한 다음에   

START REBASING을 눌러주면   
변경된 커밋 메세지로 반영된 모습을 확인할 수 있다.



<br/>
<br/>
<br/>
