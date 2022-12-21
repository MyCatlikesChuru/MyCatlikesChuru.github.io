---
layout: post
title: MySQL EER 다이어그램 만드는법
subtitle: 
categories: DB
tags: [DB, MySQL]
comments: true
published: true
---

mysql을 로컬로 연결한 다음  
연관관계 맵핑이 제대로 되어진지 확인을 하고 싶어  
EER 다이어 그램을 그려주는 기능을 사용해보려한다. 

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOYP0fQrmNAzvt44jM3q1Et2sG_V4E9GqAJGCu-W4sKhMubVYnEcUGzis00H57nfRzdoVy5xVSXQv4X_vAbgIxBcTgP=w1109-h921){: .align-left style="max-width: 100%"}

워크 밴치에서 Database -> Reverse Engineer를 클릭해준다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gP0zs_9MFTLo-MHuS9FPm_HsJ6_YNUggGHBwOxhN61Ft7SbD6ic_M14WrgWHI8UME4DxG0RHdUomxyTraJIR7NzvNxSkQ=w1109-h921){: .align-left style="max-width: 100%"}

설정을 진행한 다음에 continue를 눌러준다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPscP5oY0PMeTDUsQS1kKvD1r3hG10L-PEHzW7pTZK6Xot231dfv96wzKIxO2kqzO9WXLa0pxu6TIWxyDcsos1GVFVYZQ=w1109-h921){: .align-left style="max-width: 100%"}

쭉쭉 진행

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPcPaDrNT08DemVCSislXXYTO9rc4w2qQjwuOKirkbzqYzvTW4TAAOVKJ9r7sT8h7W0DwuZgfVn1FpJlZimjzNqchNkHA=w1920-h921){: .align-left style="max-width: 100%"}

EER 다이어그램으로 표시하고 싶은  
스키마를 선택해줍니다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOCcK5kxVJ7annhfMjE_M9fU6TKrpLb_oq0w1Q9uPoyPC1HUF2tStTOzOQyJoGqPN00zG-3p5w-AqAhsin33u3E0cHh=w1920-h921){: .align-left style="max-width: 100%"}

계속 진행해주시면 됩니다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPMJUuXItWoog3cNtrvYUCmRuZIQE_6lX5Dj2r0PTxtX54xWDIVIza67950MG8VublQ_4Il2JjaQCQJTikDQltUtOIclQ=w1920-h921){: .align-left style="max-width: 100%"}

Excute를 눌러줍니다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPBfp0S8a2KUpRBbzLTcRLCKe5FUetyQDEvUZKKt_c3ecwA5Mbpye2UsIw7FZxq55FSS21KMJrerY6aSy6bNwJ31-k8dQ=w1920-h921){: .align-left style="max-width: 100%"}

Continue 버튼을 눌러줍니다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gMVJw0WqySpUAV7fD1xhefV-vo4mDPIOupuzYdz9-Vc4O3KS9yjd7Epj3AcUq4lnc345g23GckEg0RtZdOQO2diYL1q=w1109-h921){: .align-left style="max-width: 100%"}

그러면 최종적으로 EER Diagram 화면이 표시되는 모습을 볼 수 있습니다.  
@ManyToOne, @OneToMany 등으로 연관관계 맵핑을  
제대로 진행했는지 다이어그램으로 확인해 볼 수 있다.



