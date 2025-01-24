---
title: "[Java] OOP Part8. Composition 이해하기"
date: 2025-01-24 10:04 +0900
categories: [Java]
tags: [Java, OOP, Compostion]
---

> 객체 지향 프로그래밍에서 Compostion 합성은 객체 관계를 정의하고 책임을 관리하는 데 사용되는 기본적인 디자인 원칙입니다. OOP에선 코드를 재사용하기 위한 방법으로 크게 상속과 합성이 있습니다. 상속과 합성의 차이와 합성을 어떻게 활용하는 지에 대해 알아보겠습니다.


## Compostion 합성의 개념

합성(Compostion)은 **상속에 의존하는 것**보다 더 간단한 객체를 결합하여 복잡한 기능을 구축하는 `접근 방식`입니다. 'has-a' 관계에서 사용할 수 있습니다. 예를 들어, "자동차에 엔진이 있다"는 것은 자동차 클래스에서 엔진 클래스를 확장하는 것보다 내부적으로 엔진 인스턴스를 보유한다는 것을 의미합니다. 이러한 설계는 요구 사항이 변경될 때 결합을 줄이고 시스템을 보다 유연하게 만들 수 있는 장점이 있습니다.

## 상속(Inheritance)이란?

합성에 대해 설명하기 전에 먼저 [🔗상속](https://euihyunee.github.io/posts/oop_inheritance/)에 대해 간략하게 설명하겠습니다. 상속은 <span style="color:red">코드의 재사용을 늘려주고, 변화에 대한 유연성과 확장성에 유리하다는 장점</span>을 가집니다. 하지만 이는 상속을 잘 사용했을 경우입니다. 

상속은 간단하게 말하자면 상위 클래스에 중복된 필드와 메서드를 구현하고, 하위 클래스에서 물려받아 재사용하는 개념이다. 상속은 <span style="color:red">IS-A(~은 A이다.)</span>관계를 가집니다. 

예를 들어 "전기차는 자동차이다" 같은 관계를 IS-A 관계라고 합니다.

```java
class Car{
    public void start(){
        System.out.println("시동");
    }
    public void stop(){
        System.out.println("정지");
    }
}
class ElectricCar extends Car{
    ...
}
```

전기차 클래스는 자동차를 상속받았으므로 자동차 클래스의 메서드를 사용할 수 있습니다.

```java
Car car = new ElectricCar();

car.start();
car.stop();
```

## 합성(Compostion)이란?

합성 또한 코드를 재사용하는 개념입니다. 다만 상속과의 차이점은 <span style="color:red">중복되는 필드와 메서드를 다른 클래스에서 구현하고, 이 객체를 사용하는 곳에 주입하여 사용하는 방식</span>이다. 합성은 <span style ="color:red">HAS-A(~은 A를 가진다.)</span>관계에서 많이 사용합니다.

예시로 "자동차는 엔진을 가진다." 같은 관계를 HAS-A 관계라고 합니다.

```java
class Engine{

    public void start(){
        System.out.println("Engine started");
    }
}

class Car{
    private Engine engine; // Compostion

    public Car(Engine engine){
        this.engine = engine;
    }

    public void start(){
        engine.start();
    }
}
```

상속과 합성은 코드를 재사용하고 유연성과 확장에 유리한 공통된 특징이 있습니다. 상속과 합성은 각각의 장단점이 있기에 상황에 따라 적절한 방법을 선택해야 합니다. <span style="color:red">일반적으로 합성이 더 유연하고 확장 가능한 설계를 제공하므로, 가능한 경우 합성을 선호하는 것이 좋습니다.</span> 합성에 비해 상속이 안 좋은 이유는 상속의 강한 결합도 때문이라고 할 수 있습니다. 

- 캡슐화 약화
- 유연성과 확장성 저하
- 클래스 폭발 문제
- 변경의 어려움

## 캡슐화 약화

상속을 사용하면 부모 클래스의 내부 구현이 자식 클래스에 노출되어 캡슐화가 약화될 수 있습니다. 반면 합성은 객체의 내부 구현을 숨기고 인터페이스를 통해 상호작용하므로 캡슐화를 강화합니다.

```java
// Engine 클래스
class Engine {
    public void ignite() {
        System.out.println("Engine ignited");
    }
    
    public void injectFuel() {
        System.out.println("Fuel injected");
    }
    void start(){
        injectFuel();
        ignite();
    }
}
// 상속 사용
class Car extends Engine{

    @Override 
    pubic void injectFuel(){
        System.out.println("전용 연료 주유");
    }

    @Override
    void start(){
        // 엔진의 내부 구현에 의존
        this.injectFuel();
        super.ignite(); 
    }
}
// 합성 사용
class Car{
    private Engine engine;

    public Car(Engine engine){
        this.engine = engine;
    }

    void start(){
        engine.start(); // 엔진의 인터페이스만 사용
    }
}
```

얼핏 보면 비슷한 구조이고 사용하는 데에도 문제가 없어 보입니다. 하지만 상속은 'Engine'의 내부 구현에 의존합니다. 이는 'Engine'이 변경되었을 때 예기치 않은 동작을 발생시킬 수 있습니다. 

예를 들어 'Engine'의 'ignite' 메서드가 다음과 같이 변경되었을 경우 'Car'에서는 연료 주입을 두 번하고 바뀌는 문제가 발생합니다.

```java 
class Engine{
    public void ignite(){
        injectFuel();
        System.out.println("Engine ignited");
    }
     public void injectFuel() {
        System.out.println("Fuel injected");
    }
    void start(){
        injectFuel();
        ignite();
    }
}
class Car extends Engine{

    @Override 
    pubic void injectFuel(){
        System.out.println("전용 연료 주유");
    }

    @Override
    void start(){
        injectFuel(); // 전용 연료 주유
        super.ignite(); // 다시 연료 주입 후 ignite();
    }
}
```

반면에 합성은 Engine 클래스 구현부를 수정해도 캡슐화가 되어있기 때문에 객체의 내부 구현을 숨기고 인터페이스를 통해 상호작용할 수 있습니다.

## 유연성과 확장성 저하

상속은 강한 결합도를 가지기에 유연성과 확장성이 떨어집니다. 예시로 부모 클래스인 'Engine'에 연료의 종류가 추가되고 'injectFuel'에 연료의 종류를 매개변수로 받는다고 가정해보겠습니다. 

```java
class Engine{
    private String fuelType;

    // 생성자 추가
    public Engine(String fuelType){
        this.fuelType = fuelType;
    }

    public void injectFuel(){} // 로직 변경

    public void ignite(){}
    
}
// 상속 사용
class Car extends Engine{
    
    // 부모 클래스 변경으로 인한 코드
    public Car(String fuelType){
        super(fuelType);
    }
    void start(){
        super.injectFuel();
        super.ignite();
    }
}
// 합성 사용
class Car{
    private Engine engine;

    public Car(Engine engine){
        this.engine = engine;
    }

    void start(){
        engine.start(); // 엔진의 인터페이스만 사용
    }
}
```

다음과 같이 부모 클래스의 변경으로 인해 자식 클래스 또한 변경을 해줘야 합니다. 자식 클래스가 많아지면 많아질 수록 그에 따른 수정을 모두 해줘야 하기에 유연성과 확장성이 떨어집니다. 이에 반해 합성은 필드가 추가되어 'Engine' 클래스에 필드가 추가되어도 별도의 수정없이 그대로 사용할 수 있습니다.

그리고, 다른 문제로 상속은 컴파일 시점에 결정되어 유연성이 떨어집니다. 반면 합성은 런타임 시점에 결정되어 객체를 유연하게 관리할 수 있습니다.

예시로, 엔진 타입이 인터페이스로 여러 개 있고, 하위 클래스의 자동차는 자신에게 맞는 엔진을 상속받는다고 가정하겠습니다.

```java
// 엔진 인터페이스
interface Engine{
    void start();
}
// 전기 엔진
class ElectricEngine extends Engine{}
// 가솔린 엔진
class GasolineEngine extends Engine{}
// 상속
class ElectricCar extends ElectricEngine{}
class GasolineCar extends GasolineEngine{}
// 합성
class Car{
    private Engine engine;
    
    // 런타임에 엔진을 변경할 수 있는 메서드
    public void setEngine(Engine engine){
        this.engine = engine;
    }
}
// main
// 전기 엔진으로 자동차 생성
ElectricCar car = new ElectricCar();
// 엔진을 가솔린으로 변경하기 위해 객체 새로 생성
GasolineCar car2 = new GasolineCar();
// 합성은 엔진 교체 가능
Car car3 = new Car(new ElectricEngine());
car3.setEngine(new GasolineEngine());
```

이렇게 상속은 컴파일 시점에 결정되기에 런타임에 객체를 교체할 수 없습니다. 반면 합성은 런타임 중 객체를 교체할 수 있어 동적으로 객체를 교체하는 것이 가능합니다.

물론, 컴파일 시점 결정은 유연성이 떨어지지만 성능적으로 빠른 실행 속도와 코드의 안정성을 높일 수 있습니다. 그렇기에 상속과 합성을 사용하기 전에 이러한 문제들을 고려해야 합니다.

## 클래스 폭발 문제

상속을 과도하게 사용하면 클래스 계층이 복잡해지고 클래스 수가 폭발적으로 증가할 수 있습니다. 합성은 이러한 문제를 해결하고 더 단순한 구조를 유지할 수 있습니다.

상속에서는 부모와 자식 클래스 간의 결합도가 강하게 묶여있기 때문에 새로운 기능이나 수정할 때 유연하게 대응하기 어렵습니다. 이는 여러 기능을 조합해야 하는 경우, 상속을 사용하면 모든 가능한 조합에 대해 새로운 클래스를 만들어야 합니다.

자동차에서 새로운 기능을 추가할 때마다 모든 조합에 대해 새로운 클래스를 만드는 예시를 보겠습니다.

```java
// 상속 사용 - 클래스 폭발
class SportsCar extends Car{}
class LuxurySportsCar extends SportsCar{}
class ElectricLuxurySportsCar extends LuxurySportsCar{}
// ... 더 많은 조합들

// 합성 사용 - 단순한 구조
class Car{
    private Engine engine;
    private Body body;
    private Features features;
}
interface Engine{}
class ElectricEngine implements Engine{}

interface Body{}
class ElectricBody implements Body{}
...
```

위 예시에서 상속은 자동차가 슈퍼카 -> 력셔리 슈퍼카 -> 전기 슈퍼카 ... 계속해서 새로운 기능이 추가될 때마다 새로운 클래스가 나옵니다. 반면, 합성은 새로운 기능이나 특성을 추가할 때 인터페이스에 대한 새로운 구현 클래스를 만듭니다. 

물론, 합성 방식에서도 새로운 구현 클래스가 늘어나지만 구현부를 수정해도 기존 클래스를 변경하지 않아도 됩니다. 또한 각 구현 클래스가 독립적으로 변경이 가능하기에 유연함과 확장성이 상속에 비해 좋아집니다. 단순히 말하자면 상속은 클래스가 많아지면 많아질 수록 부모 클래스의 변경이 쉽지 않고, 합성은 구현 클래스가 독립적이기에 수정이 필요한 구현 클래스만 수정해서 다시 사용할 수 있습니다.

## 마치며 

이번 글에서 Java에서 객체 지향 프로그래밍(OOP)의 중요한 개념인 합성(Compostion)에 대해 상속과 비교하며 설명했습니다. 

합성과 상속은 객체 지향 프로그래밍에서 코드 재사용과 유연한 설계를 위한 강력한 도구입니다. 하지만 상속과 비교했을 때 합성은 향상된 캡슐화, 유연성과 확장성, 단순한 구조, 느슨한 결합 등의 이점으로 일반적으로 합성을 사용하는 것이 좋아보입니다. 

하지만 상속이 더 적합한 상황도 있으므로, 설계 시 각 접근 방식의 장단점을 신중히 고려해야 합니다. 단순히 코드 재사용을 위해 상속을 사용하는 것이 아니라 명확한 IS-A 관계, 부모 클래스의 변경이 적고 잘 설계되어 있는지, 자식 클래스가 부모 클래스를 완전히 대체하는 등을 보고 코드 재사용성, 유연성, 그리고 유지보수성을 모두 고려하여 선택해야 합니다. 

대부분의 경우 "상속보다는 합성을 선호하라"는 원칙을 따르되, 특정 상황에서는 상속이 더 명확하고 적절한 해결책일 수 있습니다. 따라서 객체 지향 설계에서 합성과 상속의 이해를 통해서 견고하고 유연한 소프트웨어 시스템을 구축하는 게 가장 이상적입니다.