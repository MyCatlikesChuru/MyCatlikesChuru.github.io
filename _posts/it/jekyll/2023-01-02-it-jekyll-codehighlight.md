---
layout: post
title: Jekyll Github IO 블로그 코드 하이라이트
subtitle: Jekyll yat 테마 적용 및 꾸미기
categories: Jekyll
tags: [Jekyll, yat, 테마적용, GitHub Pages]
comments: true
published: true 
---

현재 내가 운영중인 Github.io 블로그의  
테마는 YAT 이라는 테마이다.

이전에 [YAT 테마 댓글 기능, 파비콘 추가]하는 방법까지 알아보았다.  
이제 기본으로 제공하는 Code Highlight(코드 하이라이트)   
변경하는 법을 알아보려한다.



<br/>

### Jekyll 코드 하이라이트 적용

코드 하이라이트 적용은 매우 간단하다.  


나와 같은 경우에는 [highlight.js demo] 사이트에서   
마음에 드는 코드하이라이트의 이름만 설정해주면 된다.  


```text
_includes -> extensions -> comments -> code-highlight.html
```
해당 경로의 `code-highlight.html` 파일에서   

```html
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/11.2.0/styles/atom-one-dark.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/11.2.0/highlight.min.js"></script>
```
link 태그 맨끝에 `atom-one-dark.min.css`로 되어있는 모습을 볼 수 있다.   
기존에는 `deault.min.css`로 되어있을 텐데, 이부분을 위 사이트에서  
마음에 드는 코드 하이라이트 이름으로 가져와 주면된다.  

다만 주의할점은 띄어쓰기가 되어있느느 부분은  
`-`를 붙여서 처리해주면 된다.  

혹시나 버전에따라 적용이 안되어질 수 있으니  
잘 확인해서 적용해보자 !!

[YAT 테마 댓글 기능, 파비콘 추가]: https://mycatlikeschuru.github.io/jekyll/2022/10/22/it-jekyll-comment&favicon.html
[highlight.js demo]: https://highlightjs.org/static/demo/