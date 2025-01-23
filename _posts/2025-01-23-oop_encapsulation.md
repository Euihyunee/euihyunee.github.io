---
title: "[Java] OOP Part6. 캡슐화(Encapsulation)"
date: 2025-01-23 12:17 +0900
categories: [Java]
tags: [Java, OOP, encapsulation]
---

## 캡슐화

캡슐화라는 이름은 우리가 일상생활에서 흔히 볼 수 있는 캡슐 알약에서 유래했습니다. 우리가 편히 먹는 알약 캡슐은 내부의 약물을 외부 환경으로부터 보호하고, 복잡한 내용물을 모아놓고 캡슐로 포장했습니다. 

이와 같이 캡술화는 데이터와 그 데이터를 처리하는 메서드를 하나로 묶는 것을 의미합니다. 이는 객체의 내부 구현을 외부로부터 숨기고, 오직 정의된 인터페이스를 통해서만 객체에 접근할 수 있도록 하는 메커니즘입니다. 

## 캡슐화의 주요 특징

캡슐화의 목적은 필드와 메서드를 묶고, 이를 외부로부터 숨기는 역활을 합니다. 여기에는 다음과 같은 특징 때문에 사용합니다.

- 정보 은닉: 객체의 내부 상태를 외부로부터 숨겨, 외부에서의 직접적인 접근을 제한합니다.
- 데이터 보호: 객체의 내부 데이터를 보호하여 무결성을 유지합니다.
- 인터페이스 제공: 객체와의 상호작용은 public 메서드를 통해서만 이루어집니다.
- 모듈화: 객체의 내부 구현을 외부와 독립적으로 만들어 재사용성을 높입니다.

이제 캡슐화에 이러한 특징이 있다는 것을 알았습니다. 하지만 이것을 "왜 써야하는지?"에 대해서는 잘 이해되지 않습니다. 예시를 들어 설명해보겠습니다.

```java
public class Product{
    public double price;
}
// 비즈니스 로직
public double discount(Product p){
    return p.price * 0.9; // 10% 할인
}
```

위와 같은 코드가 있습니다. 여기서 'Example'의 객체는 내부가 아니라 외부 코드에 의존하고 있습니다. 여기서 이제 문제가 시작합니다. 만약 여기서 요구사항이 변경된다면 어떻게 코드를 변경할까요? 물론 비즈니스 로직이 조금밖에 없을 때는 일일히 수정해도 됩니다. 하지만 쇼핑몰에서 이벤트를 하여 각 상품에 대한 할인 로직을 전부 변경해야 한다고 생각해보면 이는 올바르지 않습니다.
모든 상품에 대한 로직을 수정할 필요가 있습니다. 또한, `public`인 'price'는 모든 곳에서 접근할 수 있기에 가격이 0보다 낮은 가격도 들어올 수 있습니다. 다음으로 수정해보겠습니다.

```java
public class Product {
    private double price;

    public Product(double price) {
        this.price = price;
    }
    public double getPrice() {
        return price;
    }
    public void setPrice(double price) {
        if(price < 0){
            throw new IllegalArgumentException("Invalid price: " + price);
        }
        this.price = price;
    }
    public double discount(int discount){
        return getPrice() * (100-discount)/100.0;
    }
}
// 비즈니스 로직
public double discount(Product p, int rate){
    return p.discount(rate);
}
```

이렇게 바꾸어 보았습니다. 먼저 비즈니스 로직을 살펴보겠습니다. 비즈니스 로직에서는 상품과 할인율을 매개변수로 받아오면 구체적인 로직을 다시 짤 필요없이 상품과 할인율만 입력하면 내부의 메서드는 가려진 채 기능을 수행할 수 있습니다. 이후에도 할인율이 변경된다면 매개변수에 들어오는 할인율만 바꾸어주면 됩니다. 또한 `private`로 변경된 'price'는 가격을 변경할 때 `setPrice` 메서드를 사용하여 검증을 통한 유효성 검사를 합니다. 이를 통해 데이터가 유효하게 유지하는 **데이터 무결성**도 유지할 수 있게 됩니다.

이런 방식은 외부에서 구현하는 것이 아닌 객체에게 처리를 맡기기에 객체도 다른 곳에 의존할 필요없이 기능의 변경이 필요하다면 객체 내부에서 해결하고, 다른 객체와의 결합도 또한 줄여줍니다. 외부에서는 객체의 `public` 메서드와 상호작용하기에 API 역활을 하며, 객체의 메서드만 이용하면 되기에 코드 재사용성을 늘리고 코드 중복을 줄일 수 있습니다.

## 자바에서의 캡슐화 구현 방법

자바에서는 캡슐화를 이용하는 다양한 방법들이 있습니다.

### 클래스 필드 캡슐화

위 예시에서 설명했듯이 private로 필드를 선언하고 외부에서의 직접적인 접근은 `Getter와 Setter` 메서드로 구현합니다.

```java
public class Product{
    private double price;

    public void setPrice(double price) {
        if(price < 0){
            throw new IllegalArgumentException("Invalid price: " + price);
        }
        this.price = price;
    }
    public double getPrice() {
        this.price = price;
    }
}
```

이러한 필드 캡슐화는 getter와 setter를 통해서 데이터 검증을 통한 데이터 무결성을 유지합니다. 이뿐만 아니라 필요에 따라 getter와 setter만 제공하여 읽기 전용 또는 쓰기 전용 속성을 만들 수 있습니다.

### 클래스 메서드 캡슐화

메서드에 대한 캡슐화도 있다. 메서드의 내부 구현이 복잡할 경우 이를 캡슐화하여 외부에서는 간단한 인터페이스만 제공할 수 있습니다. 

다음과 같이 많은 메서드들이 필요하고 복잡하게 수행되어야 하는 경우를 보시겠습니다.

```java
public class PaymentProcessor {
    private boolean validateAmount(double amount) {
        return amount > 0;
    }
    private boolean authorizePayment(double amount) {
        // 복잡한 인증 로직
        return true;
    }
    private void deductFunds(double amount) {
        // 자금 차감 로직
    }
    private void sendConfirmation() {
        // 확인 메시지 전송 로직
    }
}
// 비즈니스 로직
void processPayment(PaymentProcessor p,double amount){
    if (validateAmount(amount) && authorizePayment(amount)) {
            deductFunds(amount);
            sendConfirmation();
    }
}
```

이렇게 불필요하게 많은 수의 메서드를 노출시켰을 때 젤 먼저 혼란스럽습니다. public이 너무 많아 어떤 메서드를 언제 사용해야 할지 결정하기 어렵습니다. 또한 각 public 메서드는 각각이 외부 코드에서 사용될 수 있어, 내부 구현을 변경하기 어려워집니다. 여기서 메서드 캡슐화를 적용시켜보면 다음과 같습니다.

```java
public class PaymentProcessor {
    public void processPayment(double amount) {
        if (validateAmount(amount) && authorizePayment(amount)) {
            deductFunds(amount);
            sendConfirmation();
        }
    }
    private boolean validateAmount(double amount) {
        return amount > 0;
    }
    private boolean authorizePayment(double amount) {
        // 복잡한 인증 로직
        return true;
    }
    private void deductFunds(double amount) {
        // 자금 차감 로직
    }
    private void sendConfirmation() {
        // 확인 메시지 전송 로직
    }
}
```

사용할 'processPayment'만 public으로 제공되고 나머지 메서드들은 private으로 캡슐화되어 있습니다. 이를 통해 사용자는 복잡한 내부 로직을 알 필요 없이 간단히 결제를 처리할 수 있으며, 내부 구현의 변경이 외부에 영향을 미치지 않게 됩니다.

### 타입 캡슐화

타입 캡슐화는 구체적인 타입을 숨기고 추상화된 인터페이스를 통해 객체와 상호작용하도록 하는 기법입니다. 

```java
// 추상화된 인터페이스
interface Shape{
    double calculateArea();
}
// 구체적인 구현 클래스
class Circle implements Shape{
    private double radius;

    public circle(double radius) {
        this.radius = radius;
    }
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}
// 타입 캡슐화를 사용하는 클래스
class ShapeProcessor{
    public void processShape(Shape shape){
        System.out.println("Area: " + shape.area);
    }
}
```
이 예시에서 'ShqpeProcessor' 클래스는 구체적인 'Circle' 클래스 대신 'Shape' 인터페이스를 사용합니다. 이를 통해 구현 세부사항을 숨기고 인터페이스의 다양한 구현 클래스들을 넣을 수 있어 코드의 유연성과 재사용성을 높입니다. 또한 변경에 더 강한 설계를 할 수 있습니다.

타입 캡슐화는 상하 관계에 있는 상속, 추상 클래스에서 사용할 수 있습니다. 그렇다고 상하 관계에서만 사용되는 것은 아닙니다. 디자인 패턴인 팩토리 메서드 패턴과 의존성 주입 등은 상하 관계를 요구하지 않고 타입 캡슐화를 구현할 수 있습니다.

**🏭팩토리 메서드를 통한 타입 캡슐화**

```java
class ShapeFactory{
    public static shape createCircle(double radius){
        return new Circle(radius);
    }
}
// 사용
Shape circle = ShapeFactory.createCircle(5.0);
```

이 방식은 객체 생성 로직을 캡슐화하여 구체적인 타입을 숨깁니다.

### 디자인 패턴 캡슐화

디자인 패턴은 객체 지향 프로그래밍에서 자주 발생하는 문제들에 대한 일반화된 해결책입니다. 캡슐화에서 주로 사용하는 디자인 패턴은 다음과 같습니다.

1. Singleton 패턴: 클래스의 인스턴스가 하나만 생성되도록 보장하며, 이 인스턴스에 대한 전역 접근점을 제공합니다.
2. Builder 패턴: 복잡한 객체의 생성 과정을 캡슐화하여 단계적으로 객체를 생성하고 구성합니다.
3. Command 패턴: 요청을 객체로 캡슐화하여 매개변수화하고, 요청을 큐에 저장하거나 로깅하고 실행을 지연시킬 수 있습니다. 
4. State 패턴: 객체의 내부 상태에 따라 행동을 변경할 수 있게 캡슐화합니다.
5. Strategy 패턴: 알고리즘을 정의하고 각각을 캡슐화하여 교환해서 사용할 수 있게 합니다.

## 마치며 

캡츌화는 객체 지향 프로그래밍의 핵심 원칙 중 하나로, 데이터와 그 데이터를 처리하는 메서드를 하나의 단위로 묶어 외부로부터 보호하는 메커니즘입니다. 이는 정보 은닉, 데이터 보호, 모듈화 등의 이점을 제공합니다. 

개발자가 캡슐화의 개념과 적용 방법을 깊이 이해하는 것은 매우 중요합니다. 캡슐화는 단순히 코드를 숨기는 것 이상으로, 소프트웨어 설계의 품질을 크게 향상시키는 핵심 도구입니다. 개발자는 캡슐화의 원칙을 깊이 적재적소에 잘 적용시켜 더 나은 코드를 작성할 수 있으며, 효율적이고 유지보수가 용이한 소프트웨어 시스템을 구축하는 데 필수적입니다.