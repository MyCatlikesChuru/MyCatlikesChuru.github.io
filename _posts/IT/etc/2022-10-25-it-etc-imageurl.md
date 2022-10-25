---
layout: post
title: 구글 드라이브로 이미지 url 적용하기
subtitle: 
categories: IT
tags: [IT, 이미지 url, 구글 드라이브]
comments: true
published: true 
---

# 이미지 url 적용하기  
네이버 블로그나 Tistory 등...  
타 플랫폼의 블로그를 작성하시는 분들은 사진을 통해서  
올리기 때문에 해당 플랫폼이 알아서 관리를 해준다.  

하지만... 나와 같이 GitHub Page를 사용한다거나  
개인 블로그를 운영중인 분들은  
이미지를 올리기 위해 다른 방법을 찾아야한다.  

1. GitHub에 사진을 같이 올리고 , 경로로 사진표시  
-> 해당 방법은 GitHub 저장소가 초과하면 애매해진다..
2. 이미지를 url로 변경 시켜 관리하기  
-> 이미지를 url로 변경시키고 저장을 유지시켜주는 안전한 플랫폼이 필요

이렇게 떠올리게되어 나는 **2번**을 선택하게 되었고  

처음에는 다른 사람들이 올려놓은 [iFH 이미지 호스팅]과 같은  
홈페이지를 이용해 정리를 하였었는데 ....  

[iFH 이미지 호스팅]:https://ifh.cc/

불현듯 현재는 이 사이트에서 내사진을 저장해 관리하고 있는데  
이 사이트의 서비스를 종료한다던가? 사라지게된다면  
내가 여태까지 저장해놓은 모든 사진들은 허공속으로 가버리는 것이다..  

<br/>

### <span style="color:#0066CC">안전하게 저장하는 방법은 ?</span> 

다른 변경 호스팅 페이지를 이용하면 위에서 말했던 문제가 생길 수도 있기 때문에  
비교적으로 안전한 **"구글 드라이브를 통해 이미지 url 바꾸는 방법**을 설명하려한다.  


**1). 내가 변경하고자하는 이미지를 구글드라이브에 넣는다.**  
-> 넣은 이미지 파일을 마우스 오른쪽 클릭 !  
-> 필자는 스크린샷 2022-10~~ 뭐시기로된 프로그램 캡처사진을 넣었다.

<img src="https://lh3.googleusercontent.com/drive-viewer/AJc5JmQVcLiSu-ykhVbtab_etZPl1pHZEPtUPerbz_EIPeek-aTWiCg0enyE5L2b7We-d3nOX4bJC_o=w3024-h1728" align="left">


**2). 링크가 있는 모든 사용자로 바꿔준다.**  
-> 바꾼후 완료 클릭  
-> 링크 복사를 한다.

<img src="https://lh3.googleusercontent.com/drive-viewer/AJc5JmRWQGDSjQWsBm5kM9hE4Iulyr7vj2dHkp2XFx-LXxMuJbTHffLKx-B79RdD7llp3yh8fiIZ9LU=w3024-h1728" align="left">
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

**3). 내가 작성중인 블로그에 해당 링크를 붙여넣는다.**  
-> 보통 GitHub page를 작성하는 분들은 MarkDown 언어로 포스팅을 하기에  

```markdown
![example image](링크주소)
```  
와 같은 방법으로 주소를 넣어 이미지를 넣거나  

크기 조절이나 정령을 하고 싶으신 분들은

```html
<img src="링크주소" align="left">
```
html 문법으로 링크 주소를 넣어 작성하여 이미지를 넣어도 무방하다.  

<br/>

### <span style="color:#0066CC">파일이 로딩되지 않아요 !</span> 


여기서 만약 위에 1),2)번을 따라했지만 아래와 같이 사진 이미지가  
보이지 않고 파일로 나올경우?

<img src="https://lh3.googleusercontent.com/drive-viewer/AJc5JmTFm2_anq_vjKEzlpnDEC5kFnBj5i_vYGIALa0GC8bH4NstmFC88WmthnmEf-glizCiNxdObFA=w3024-h1728" align="left">  

<br/><br/><br/><br/><br/><br/>

이렇게 이미지를 사용하지 못하는 경우는  
아래와 같은 방법으로 사진 주소를 다시 검색 할 수 있다.

<br/>

먼저 아까 위에서 복사했던 주소를  
Google Chrome 브라우저에서 **새탭**으로 열어주면  
아래와 같이 구글드라이브로 연동된 사진이 나오게 될 것이다.   

<img src="https://lh3.googleusercontent.com/drive-viewer/AJc5JmQY7wKQYjWRwh8o6PanbsXgp_0EbT97NG5HgCB1bLfSPI0ljEjzN6uT_BQfnsukgdH81SCmJ7E=w3024-h1728" align="left">

<br/>

위에 사진이 나왔다면 아래의 순서대로 진행하면 된다.



1. 브라우저에 사진이 나온다.
2. 크롬 브라우저 개발자 도구를 연다  
   -> 경로 : 크롬 브라우저 상단 우측 **⫶** 로된 버튼 클릭 -> 도구 더보기 -> 개발자 도구  
   **tip) 맥북 단축키 : ⌥ + ⌘ + i  
   tip) 윈도우 단축키 : F12**

3. 개발자 도구에서 **찾기 기능**(맥북:⌘+F, 윈도우:Ctrl+F)를 연다.
4. 찾기 기능이 열리면 위 사진과 같이 중간에 타이핑을 할 수 있는 창이 생긴다.  
   **img** 라고 **입력** 엔터를 눌러 위치를 찾는다.
5. 엔터를 누르다보면 위에 사진과 같이 url 주소가 나온다.  
   -> 해당 주소에 마우스를 가져다대면 사진에 하늘색으로 표시가됨
6. 해당 주소를 복사해서 사용하면 된다!

<img src="https://lh3.googleusercontent.com/drive-viewer/AJc5JmQau70WaZx0XThiMXSN914-WF8Y-TVNlWxtTuLmQWoXqyTii9qeT4G6COxAN1CXH-PL4eHE0Lg=w3024-h1728" align="left">

<br/>

위의 내용을 따라하면 된다. 따라하면서 위에 사진과 동일하게   
주소를 찾아주면 그 주소가 이미지 url 주소이다.  

그 주소를 가지고 이제 블로그나 내가 사용하려는 곳에  
이용하면 되는 것이다!!  

<br/>

### <span style="color:#0066CC">폴더를 공유로 설정 !</span>

참고로 해당하는 사진마다 일일히 공유를 눌러줄 수는 없으니까  
내가 관리하려는 폴더에 위에서 공유를 하면 하위 데이터는  
전부 공유로 바뀌게된다.


<img src="https://lh3.googleusercontent.com/drive-viewer/AJc5JmRzYYtAjIhzT6vvIjHZ-tIrBxLgSBHbHCqwqwrUIuA4MchYwWltF7PFaz0ROvRlBCXXEvOCadA=w3024-h1728" align="left">

<br/>


<img src="https://lh3.googleusercontent.com/drive-viewer/AJc5JmQK6pfkh96AtgcxVx4TR0g7yWopx7nBubGMiNLL7BtjHnPKAVOTyw8ROJreFSNHeF7ilHIKSIk=w3024-h1728" align="left">

<br/>

이렇게 내가 블로그에 작성하려는 이미지를  
폴더 별로 잘 모아놓았다가  
전체를 공유로 바꾸든, 일부분만 바꾸든 적용해서  
그 이미지를 개발자도구를 통해 주소로 꺼낼 수 있다.  

---    

<br/>

이상 구글 드라이브를 통해  
이미지를 url로 가져올 수 있는 방법을 설명 드렸다.

사실 추후에 이렇게 사용하는 부분을 막을지? 아닐지는 잘 모르겠다.  
하지만 구글이라는 대형 플랫폼을 통해서 관리를 한는 측면.  
그리고 아이클라우드나, 구글드라이브같이 우리가 널리 사용하는  
서비스이기 때문에  데이터가 날라갈 걱정을 할 필요는 없으니  

조금 안심된 블로그 이미지를 사용할 수 있을 듯한? 기분이다 !!  

