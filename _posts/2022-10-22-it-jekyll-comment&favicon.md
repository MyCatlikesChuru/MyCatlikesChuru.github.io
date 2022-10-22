---
layout: post
title: Jekyll GitHub Page 댓글, 파비콘 꾸미기
subtitle: Jekyll yat 테마 적용 및 꾸미기
categories: IT
tags: [Jekyll, yat, 테마적용, ]
comments: true
published: true 
---

# Jekyll 테마 적용
<br/>

![example image](https://jekyllrb.com/img/octojekyll.png)  

<br/>


오늘은 Github Page를 통해 첫번째로 하는 포스팅이다.  
MarkDown 문법도 어색하고 모든게 서툴지만 천천히 블로그 개설 준비를 하려한다.  

Jekyll 이란?  
텍스트 변환 엔진으로, 마크업 언어로 작성하면 미리 정의해놓은 규칙에따라 정적 웹사이트를 만들어준다.  
우리가 직접 테마를 만들 수도 있지만,, 아직 초보 개발자인 나한테 모든게 어렵게 느껴진다.  
그 첫걸음을 하기위해 우선 다른 개발자분들이 만들어 놓은 테마를 이용할 생각이다.  

나는 그중에서 yat이라는 테마를 사용하기로 했다.  
[Jekyll yat 테마 사이트]  
[Jekyll yat 테마 제작자 github]  

현재 내가 사용하고 있는 블로그의 테마다.  
Jekyll관련 셋업이랑 테마를 Git Page에 적용하는 방법  
로컬 환경에서 작업하는법 등은 다른글을 통해 작성해 보겠다.

[Jekyll yat 테마 사이트]: https://jekyll-themes.com/jekyll-theme-yat/
[Jekyll yat 테마 제작자 github]: https://github.com/jeffreytse/jekyll-theme-yat
[cell image]: https://jekyllrb.com/img/octojekyll.png

<br/>

## Jekyll yat 테마 Utterances 댓글 기능 추가

![example image](https://ifh.cc/g/BpXV1h.png)  
위와 같은 댓글 기능을 추가하는 방법이다.  

위에서 다운 받은 yat 테마는 기본적으로 댓글 기능이 따로 있지는 않는다.  
하지만 개발자가 여러가지 기능을 사용할 수 있게 미리 개발을 해두어서 우리는  
그 기능을 지원하는 것만 설정하면 댓글을 사용할 수 있다.  

Disqus comments, Gitment comments, Utterances comments  
이렇게 3가지 댓글 기능을 지원해주고 있고, 이중에서 나는 Utterances 코맨트  
기능을 사용하려한다. 아래를 쭉 따라해보자

<br/> 

![example image](https://ifh.cc/g/YV9G0L.png)  
GitHub에 Repository를 하나 생성해야한다.  
이름은 아무렇게나 지어도 상관없다. 중요한점은 public로 만들어야한다.

<br/> 

![example image](https://ifh.cc/g/C0C4x3.png)  
Repository를 만들었으면, Quick Setup화면이 보여질 텐데  
<span style="color:#0066CC">**'creating a new file'**</span>을 클릭 !

<br/> 

![example image](https://ifh.cc/g/nskSGt.png)  
클릭을 하면 위와같은 화면을넘어오는데  
빨간색 박스의 내용을 그대로 입력해준다.  
origins의 주소에는 내가 댓글 기능을 추가할 blog를 추가하면된다.  
나는 내 Github Page를 추가 !
```
{
  "origins": ["https://mycatlikeschuru.github.io"]
}
```

<br/> 

![example image](https://ifh.cc/g/HHFBBK.png)  
[utterances] 사이트를 접속해 install을 눌러 주면  
위와 같은 화면이 나오게되는데 Only select repositories에서  
아까 위에서 만들었던 Repository CommentTest를 선택해주고  
Rquest를 눌러 주면 완료. 이 이후에 따로 페이지가 발생하게 되는데  
무시해도 상관 없다.

[utterances]: https://github.com/apps/utterances
<br/>

![example image](https://ifh.cc/g/Cbkygc.png)  
나는 IntelliJ를 통해서 MarkDown을 작성 중이고  
대부분 VScode를 이용해서 작성하는 것 같다.  
여하튼,, 작성 툴을 이용해 _config.yml 설정파일에서  

utterances 항목을 찾아주면된다.  
아마 처음에 테마를 받으면 # 으로 주석처리가 되어있을 것이다.  
주석 처리를 풀어준 다음에. 오른쪽의 네모박스와 같이 입력해주면 되는데  
여기서 repo -> 이부분만 아까 우리가 만들어준 repository로 적용하면  
<span style="color:#F04965">(*주의: utterances 밑에 repo,issue_term 등등,, 맨앞을 공백으로 해야 적용됨*)</span>

<br/>  

![example image](https://ifh.cc/g/M2V3ps.png)  
그렇게되면 이렇게 ! GitHub처럼 댓글을 사용할 수 있는  
기능이 이렇게 똭! 하고 사용이 가능합니다~  
주로 우리가 올리는 글 위주가 IT관련이라 GitHub로그인이 필요한  
utterances를 통해서 댓글 기능을 추가해보았다~  

참고로 disqus도 적용해 보았는데 별다른 오류 없이 잘동작되었었다.


<br/>

## Jekyll yat 파비콘 추가

![example image](https://ifh.cc/g/pgWVFk.jpg)  
파비콘(favicon)은 우리가 웹사이트에서 보면  

<img src="https://ifh.cc/g/xAF0Qj.png" align="left">
<br/>  

위와 같이 웹사이트 홈페이지의 이름과 같이 나오는 사진이다.

yat 테마는 favicon을 적용하면 우리 블로그 왼쪽 위에  
프로필 사진 마냥 귀엽게 표시도되고 웹 TAB에서도 파비콘 아이콘이  
적용되는 모습을 볼 수 있다.

<br/>

![example image](https://ifh.cc/g/L2lDHM.png)  
파비콘 적용 방법은 정말 매우 간단하다.  
_config.yml 파일에서 favicon: ""이라고 되어있는 부분에  
자원 경로를 넣어주면된다. 나와 같은 경우는 assets의 images 폴더에  
내 파비콘 사진을 두었고 그 경로를 오른쪽과 같이 지정해서  
파비콘이 정상적으로 적용된 모습을 확인 할 수 있었다.


<hr/>

오늘은 Jekyll 테마 중 yat이라는 테마를 커스터 마이징하는  
글을 올려 보았다. 기존에 Tistory를 이용할 때는  
그냥 이미지와 글,코드 올리는 것들이 간단했는데  
아무래도 Markdown언어로 작성하다보니 확실히 시간소요가 더 되는 것 같다.  
그래도 앞으로 사용하다보면 빨라질거라 생각하며 첫 포스팅을 마친다. 😃