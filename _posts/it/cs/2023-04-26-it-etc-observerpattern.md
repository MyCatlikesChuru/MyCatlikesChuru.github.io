---
layout: post
title: "디자인 패턴 - 옵저버 패턴 [Java 예제]"
subtitle: 면접을 위한 CS 전공지식 노트
categories: CS
tags: [ComputerScience]
comments: true
published: true 
---

## 옵저버 패턴  

**옵저버 패턴(Observer pattern)은** 주체가 어떤 객체의 상태 변화를 관찰하다가   
상태 변화가 있을 때마다 메서드 등을 통해 옵저버 목록에 있는 옵저버들에게 변화를 알려주는 패턴이다.   

![image](https://user-images.githubusercontent.com/95069395/234535516-a59b6c8b-411c-43ed-ac8e-fb960590051f.png){: .align-left style="max-width: 70%"}

**주체**란? 객체의 상태 변화를 보고 있는 관찰자이며   

**옵저버**들이란? 이 객체의 상태 변화에 따라 전달되는 메서드 등을 기반으로   
추가 변화사항이 생기는 객체들을 의미한다.

해당 패턴에 대해서 말로서는 이해하는건 어렵지 않았다.   
하지만 코드로서 어떻게 작성을할 수 있는지?

예제로 알아보자   

<br/>

### 옵저버 패턴 예제   

Java에서 옵저버 패턴을 구현하는 가장 간단한 방법 중 하나는  
`java.util.Observable` 클래스와 `java.util.Observer` 인터페이스를 사용하는 것이다.   

이번에는 위의 클래스,인터페이스를 사용하지 않고 직접 구현하는 것으로 해보자  

![image](https://user-images.githubusercontent.com/95069395/234568110-81338dd0-bf4d-4e99-bcb9-c2cb20333b9f.png){: .align-left style="max-width: 70%"}  

예제로는 주식가격이 변동이 생길때 주주(옵저버)들에게 변동사항을 알려주는 예제다.

<br/>

✅ **Observer 설계**  
우선 주주들을 정해줄 옵저버 인터페이스를 정의해보자

```java
public interface StockObserver {
    void update(String stockName, double stockPrice);
}
```
해당 인터페이스는 주식이름, 주식가격이 update(변경)될는 것을 의미한다.   
변경이 되면 주주에게 주체가 알려줘야한다.   

<br/>  

인터페이스를 구현한 클래스를 만들었다.

```java
public class ConsoleStockObserver implements StockObserver {
    @Override
    public void update(String stockName, double stockPrice) {
        System.out.println("Stock price of " + stockName + " has changed to " + stockPrice);
    }
}
```

콘솔로서 주주(옵저버)들에게 알려주는 역할을 하는 메서드이다.   

<br/>   

✅ **Subject 설계**  
이제 주체(Subject)인 주식시장를 구현해보자

```java

public class StockMarket {
    private Map<String, Double> stockPrices = new HashMap<>();
    private ArrayList<StockObserver> observers = new ArrayList<>();

    // Observer를 추가
    public void addObserver(StockObserver observer) {
        observers.add(observer);
    }

    // Observer를 삭제
    public void removeObserver(StockObserver observer) {
        observers.remove(observer);
    }

    // 주식가격 설정 & 변동  
    public void setStockPrice(String stockName, double stockPrice) {
        stockPrices.put(stockName, stockPrice);
        notifyObservers(stockName, stockPrice);
    }

    // 옵저버에게 주식가격이 변동되었음을 알림
    private void notifyObservers(String stockName, double stockPrice) {
        for (StockObserver observer : observers) {
            observer.update(stockName, stockPrice);
        }
    }
}
```  
주식시장(StockMarket)이라는 주체를 만들어주었다.    
해당 주체는 주식가격(객체)이 변동이 생길때를 확인해 주주(옵저버)들에게 알려주는 역할을 한다.   

<br/>   

아래의 실행문을 보고 이해해보자   

```java
public class Main {
    public static void main(String[] args) {
        StockMarket market = new StockMarket(); // 주체 생성
        ConsoleStockObserver observer = new ConsoleStockObserver(); // 옵저버 생성

        market.addObserver(observer);  // 주체에 옵저버 추가
        market.addObserver(observer);  // 주체에 옵저버 추가
        
        market.setStockPrice("LG전자", 108400.0); // 가격 변동
        market.setStockPrice("삼성전기", 139100.0); // 가격 변동
        // 출력
        // Stock price of LG전자 has changed to 108400.0
        // Stock price of 삼성전기 has changed to 139100.0

        market.removeObserver(observer);
    }
}
```

주체인 주식시장 인스턴스가 만들어졌고   
해당 주체에 옵저버가 추가되었다. (옵저버는 콘솔로 변동을 알려준다.)   

이제 주식시장에서 주식 2개의 변동이 생겼고 (LG전자, 삼성전기)   
변동이 생길때 마다 `notifyObservers()`메서드로 인해서 저장된 옵저버들의   
구현해둔 `update()`를 통해서 콘솔을 통해 주식이름과 얼마로 가격이 변동되었는지 알릴 수 있는 것이다.   

<br/>  

### Spring에서의 옵저버 패턴  

SpringFramework에서는 `@EventListener`를 이용해 간단하게   
옵저버패턴을 구현할 수 있다. 해당 어노테이션을 사용하면 이벤트가   
발생할 때 마다 해당메서드가 자동으로 호출된다.   

간단한 예제로 주문이 생성될 때마다 Order 객체를 생성하고  
이를 처리하는 리스너 클래스를 만들어주는 예제를 보자  

```java
@Getter
@Setter
public class Order {
    private String orderId;
    private String customerName;
    private double amount;
}

@Service
public class OrderService {
    private final ApplicationEventPublisher publisher;

    public OrderService(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    public void createOrder(String orderId, String customerName, double amount) {
        // 주문 생성 코드 생략...

        Order order = new Order(); // <- 주문 생성
        order.setOrderId(orderId);
        order.setCustomerName(customerName);
        order.setAmount(amount);

        OrderCreatedEvent event = new OrderCreatedEvent(this, order); // <- 해당주문으로 이벤트 객체생성
        publisher.publishEvent(event); // @EventListener 호출
    }
}

public class OrderCreatedEvent {
    private final Object source;
    private final Order order;

    public OrderCreatedEvent(Object source, Order order) {
        this.source = source;
        this.order = order;
    }

    public Order getOrder() {
        return order;
    }
}

@Component
public class OrderCreatedEventListener {
    @EventListener
    public void handleOrderCreatedEvent(OrderCreatedEvent event) {
        Order order = event.getOrder();
        System.out.println("Order created: " + order.getOrderId() + ", " + order.getCustomerName() + ", " + order.getAmount());
    }
}
```

`@EventListener`를 메서드에 붙여 정의를 해놓은 다음  
해당 클래스 `OrderCreatedEventListener`를 빈으로 등록하면  

`ApplicationEventPublisher`로 만든 객체로 `.publishEvent();`에   
호출할 객체를 넣어서 실행시키면 리스너측 메서드가 호출되게 되어진다.   

즉, 정리하자면

> 옵저버 = `handleOrderCreatedEvent() 메서드`  
> 주체 = `ApplicationEventPublisher 클래스`     
> 변경감지 = `publisher.publishEvent(event);`

<br/>   

### 옵저버 패턴의 장점   

1. 느슨한 결합도: 옵저버 패턴은 객체 간의 결합도를 낮추는 느슨한 결합(loose coupling)을 제공합니다. 즉, Subject 객체와 Observer 객체가 서로 독립적으로 존재하며, 변경사항이 발생하더라도 서로 영향을 미치지 않습니다. 이는 객체 간의 유연성(flexibility)을 높여줍니다.
2. 확장성: 옵저버 패턴은 새로운 Observer 클래스를 추가하거나 기존 Observer 클래스를 수정하지 않고도 Subject 객체의 동작을 확장할 수 있습니다. 이는 애플리케이션의 확장성을 높여줍니다.
3. 이벤트 기반 시스템 구현에 용이: 옵저버 패턴은 이벤트 기반(event-driven) 시스템의 구현에 용이합니다. 이는 애플리케이션에서 발생하는 이벤트에 대해 Subject 객체가 이벤트를 발행하고, 이를 구독한 Observer 객체가 이벤트를 수신하면서 처리할 수 있기 때문입니다.  



<br/>

### 옵저버 패턴의 단점  

1. 오버헤드: 옵저버 패턴은 객체 간의 통신이 빈번하게 일어나기 때문에, 불필요한 오버헤드가 발생할 수 있습니다. 이는 대규모 시스템에서 성능 저하를 초래할 수 있습니다.  
2. 디버깅의 어려움: 옵저버 패턴은 객체 간의 상호작용이 많이 일어나기 때문에, 디버깅이 어려울 수 있습니다. 이는 Observer 객체들 간의 상호작용이 복잡해지면서, 버그를 찾기 어려워지기 때문입니다.



<br/>  
<br/>

---

✨ 참고한 자료  
- [ChatGPT 선생님]
- [pjh3749님의 블로그]
- [면접을 위한 CS 전공지식 노트]

<br/> 
<br/> 

[ChatGPT 선생님]: https://chat.openai.com/
[pjh3749님의 블로그]:https://pjh3749.tistory.com/266
[면접을 위한 CS 전공지식 노트]:http://www.yes24.com/Product/Goods/108887922