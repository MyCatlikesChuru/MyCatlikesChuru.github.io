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

![image](https://user-images.githubusercontent.com/95069395/217406036-e87e3db5-5a64-49b9-a6f8-d9c45828fb08.png){: .align-left style="max-width: 100%"}

워크 밴치에서 Database -> Reverse Engineer를 클릭해준다.

![image](https://user-images.githubusercontent.com/95069395/217406037-a380c35a-7e5a-4326-9a69-11e014492ce5.png){: .align-left style="max-width: 100%"}

설정을 진행한 다음에 continue를 눌러준다.

![image](https://user-images.githubusercontent.com/95069395/217406038-1522e0cd-ee4e-4eb2-b19d-0a4ca030bf7d.png){: .align-left style="max-width: 100%"}

쭉쭉 진행

![image](https://user-images.githubusercontent.com/95069395/217406039-fde63f69-2c68-4f20-a846-533b2c4822ab.png){: .align-left style="max-width: 100%"}

EER 다이어그램으로 표시하고 싶은  
스키마를 선택해줍니다.

![image](https://user-images.githubusercontent.com/95069395/217406041-6dbd1761-76b6-4598-a804-a51e0d2b7422.png){: .align-left style="max-width: 100%"}

계속 진행해주시면 됩니다.

![image](https://user-images.githubusercontent.com/95069395/217406044-ef9c7002-d27e-441c-aac3-8747b3939458.png){: .align-left style="max-width: 100%"}

Excute를 눌러줍니다.

![image](https://user-images.githubusercontent.com/95069395/217406047-6ced2b51-4c88-4e79-9117-a8efde6858de.png){: .align-left style="max-width: 100%"}

Continue 버튼을 눌러줍니다.

![image](https://user-images.githubusercontent.com/95069395/217406048-dd589dbd-6718-4dea-9768-d67a26b092f2.png){: .align-left style="max-width: 100%"}

그러면 최종적으로 EER Diagram 화면이 표시되는 모습을 볼 수 있습니다.  
@ManyToOne, @OneToMany 등으로 연관관계 맵핑을  
제대로 진행했는지 다이어그램으로 확인해 볼 수 있다.



