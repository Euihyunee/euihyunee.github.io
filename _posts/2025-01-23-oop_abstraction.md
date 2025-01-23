---
title: "[Java] OOP Part6. 추상화(abstraction)"
date: 2025-01-23 09:34 +0900
categories: [Java]
tags: [Java, OOP, abstraction]
---

> 객체 지향 프로그래밍에서 추상화(Abstract)는 복잡한 시스템을 단순화하고 필수적인 특징만을 강조하는 과정이라고 할 수 있습니다. 불필요한 세부사항을 제거하고 문제의 본질에 집중할 수 있게 해주는 핵심 개념입니다. 오늘은 객체 지행 프로그래밍의 핵심 개념인 추상화에 대해서 알아보겠습니다.

## 추상화의 개념

사전에 따르면 추상화는 복잡한 대상에서 핵심적인 개념이나 기능을 간추려내는 것을 의미합니다. 예를 들어 스마트폰으로 전화를 걸 때, 우리는 단순히 연락처를 탭하거나 번호를 누르기만 합니다. 통화 연결을 위한 복잡한 신호처리나 데이터 전송 과정은 추상화되어 있어 사용자가 신경 쓰지 않아도 됩니다.

객체 지향 프로그래밍에서 추상화는 복잡한 시스템을 단순화하고 핵심적인 특성만을 추출하는 과정입니다. 이는 현실 세계의 복잡한 객체나 프로세스를 프로그램에서 다루기 쉬운 형태로 모델링하는 것을 의미합니다. 쉽게 표현하자면 복잡한 시스템을 이해하기 쉬운 기본 틀이라고 할 수 있습니다. 이를 보고 개발자는 복잡한 시스템을 이해하고 일관된 구조와 명명 규칙을 유지할 수 있습니다.

개발자가 협업을 하며 시스템을 개발할 때, 모든 세부사항을 동시에 고려하는 것은 힘듭니다. 여기서 개발자는 추상화를 통해 시스템의 특정 부분에 집중할 수 있습니다. 구체적인 구현 대신 추상적인 부분만 보기에 특정 부분만 변경하거나 확장하는 것이 가능하여 유연성과 확장성을 증가시킬 수 있습니다.

마지막으로, 추상화는 시스템의 보안을 강화하는 데 기여합니다. 내부 구현 세부사항을 숨기고 필요한 인터페이스만을 노출함으로써, 시스템의 중요한 부분을 보호하고 무단 접근이나 조작을 방지할 수 있습니다.

> 이처럼 추상화는 복잡한 시스템을 관리하기 어렵거나 협업을 할 때 설계하는 기법에 가깝습니다. 복잡한 시스템을 단순화하고 핵심적인 특성만을 추출하여 시스템을 이해하고, 팀원이 독립적으로 작업할 수 있도록 합니다. <span style="color: red">추상화는 코드를 짜는 방법이 아닌 사고 방식이자 설계 원칙</span>이라고 할 수 있습니다.

## 추상화의 구현 방법

자바에서 추상화를 하는 방법은 `abstract` 키워드를 이용한 추상 클래스와 `Interface`가 있습니다. 각각의 방법에 대해 알아보겠습니다.

### abstract을 통한 추상화

자바에서는 `abstract` 키워드를 사용하여 추상 클래스를 선언하며, 내부에 `abstract`와 메서드를 같이 사용하여 <span style="color: #fff5b1">하나 이상의 추상 메서드를 포함</span>할 수 있습니다.

```java
// 추상 클래스
public abstract class Phone{
    private String company;
    private String chargeType;

    public Phone(String company, String chargeType){
        this.company = company;
        this.chargeType = chargeType;
    }
    // 추상 메서드
    public abstract void call();
}
public class ApplePhone extends Phone {
    public ApplePhone(String company, String chargeType){
        super(company, chargeType);
    }
    @Override
    public void call(){
        System.out.println("애플폰으로 전화를 겁니다.");
    }
}
```

위의 예제처럼 추상 클래스에는 생성자, 필드, 일반 메서드, 추상 메서드를 가질 수 있습니다. 추상 클래스는 관련 있는 클래스들의 공통 기능을 추출하여 만듭니다. 

추상 클래스 내에는 메서드가 두 개 있습니다. 일반 메서드와 추상 메서드입니다. 추상 메서드에는 구현부가 없어 상속을 통해서 오버라이드 해야합니다. 둘 다 오버라이드해서 사용할 수 있지만 추상 메서드는 자식 클래스에서 반드시 구현해야 하지만, 일반 메서드는 선택적으로 오버라이드할 수 있다는 차이가 있습니다. 

추상 클래스에는 생성자가 있지만 인스턴스 객체를 생성할 순 없습니다. 추상 클래스에도 공통된 필드를 가질 수 있는데 이를 초기화하는 데 사용하기 위해서 생성자가 존재합니다.

### 인터페이스를 통한 추상화

인터페이스는 추상 메서드와 상수만을 포함하는 추상화 타입입니다. 모든 메소드는 기본적으로 `public abstract`이며, 구현 클래스에서 이를 반드시 구현해야 합니다.

```java
// 인터페이스
public interface Phone{
    String krNumber = "+82";
    public void call();
}
public class ApplePhone implements Phone {
    private String company;
    private String chargeType;

    public ApplePhone(String company, String chargeType){
        this.company = company;
        this.chargeType = chargeType;
    }
    @Override
    public void call(){
        System.out.println("애플폰으로 전화를 겁니다.");
    }
}
```

위 예제처럼 인터페이스에는 상수와 메서드만을 가질 수 있습니다. 메서드는 구현부가 없으며 앞에 `abstract`가 생략되었다고 생각하시면 됩니다. 그렇기에 반드시 구현 클래스에서 이를 구현해야 합니다. 

'krNumber'는 필드이지만 인터페이스의 모든 필드는 자동으로 `public static final`이 됩니다. 인터페이스는 객체의 행위를 정의하는 목적이기에 상수를 사용하는 것은 권장드리지 않습니다. 특수한 목적이 없는 한 상수를 사용하는 구체 클래스에 정의하거나 열거형(enum) 등의 대안을 사용하는 것을 권장합니다.

## 추상화와 다른 OOP 개념과의 관계

추상화는 객체 지향 프로그래밍의 핵심 개념 중 하나로, 다른 OOP 개념들과 밀접한 관계를 가지고 있습니다. 추상화와 다른 OOP 개념들의 관계를 살펴보겠습니다.

### 캡슐화와 추상화

추상화와 캡슐화는 서로 보완적인 관계에 있습니다.

- 추상화는 복잡한 시스템을 단순화하여 필수적인 특징만을 표현합니다.
- 캡슐화는 이러한 추상화된 데이터와 기능을 하나의 단위로 묶고 내부 구현을 숨깁니다.

```java
public abstract class Vehicle{
    private int speed;

    public abstract void accelerate();

    public int getSpeed(){
        return speed;
    }
    public void setSpeed(int speed){
        this.speed = speed;
    }
}
public class Car extends Vehicle{
    @Override
    public void accelerate(){
        setSpeed(getSpeed()+10);
    }
}
```

이 예시에서 'Vehicle' 클래스는 추상화를 통해 모든 차량의 공통 특성을 정의합니다. 'speed' 변수를 'private'로 선언하여 사용자는 엑셀을 밣으면 속도가 더 붙는다는 걸 알지만 내부 데이터와 구현 세부사항을 모르게 하는 캡슐화를 이룹니다.

### 상속과 추상화

추상화는 상속을 통해 구현될 수 있습니다.
- 추상 클래스나 인터페이스를 정의하여 ***공통적인 특성***을 추상화합니다.
- 하위 클래스들이 이를 상속받아 구체적인 구현을 반드시 제공합니다.

```java
class Lamborghini{
    int speed;
    void accelerate(){speed += 10;}
    void sportMode(){...} // 고유 모드
}
class HyundaiElectric{
    int speed;
    void accelerate(){speed += 10;}
    void electricMode(){...} // 고유 모드
}
class Tesla{
    int speed;
    void accelerate(){speed += 10;}
    void valetMode(){...} // 고유 모드
}
```

추상화는 상속을 이루기에 위 예시들의 공통된 속성을 추상 클래스에 묶어 **코드의 중복 제거 및 재사용성**을 할 수 있습니다.

### 다형성과 추상화

추상화를 통해 정의된 공통 인터페이스 또는 추상 클래스를 사용하여 다형성을 구현할 수 있습니다. 이를 통해 **코드의 유연성과 확장성**이 향상됩니다.

```java
// Main
Vehicle car1 = new Lamborghini();
Vehicle car2 = new HyundaiElectric();
car1.accelerate();
car2.accelerate();

List<Vehicle> carList = new ArrayList<>();
carList.add(car1);
carList.add(car2);

for(Vehicle car : carList){
    car.accelerate();
}
```

업캐스팅을 통하여 다양한 하위 클래스들을 컬렉션에 묶어 사용할 수 있으며, 같은 메서드로 다른 기능을 컬렉션 프레임워크인 List, Set, Map 등을 사용해 다양한 객체를 동일한 방식으로 다룰 수 있습니다. 이를 통해 개발자는 **다형성**을 통해 특정 구현에 종속되지 않고 유연한 코드를 작성할 수 있습니다.

## 마치며 

추상화는 단수니 코드를 작성하는 방법이 아닌, 시스템을 설계하고 이해하는 데 필수적인 사고 방식이자 설계 원칙입니다. 추상화를 통해 개발자는 복잡한 시스템을 더 쉽게 이해하고 관리할 수 있으며, 협업 시 일관된 구조와 명명 규칙을 유지할 수 있습니다.

추상화는 캡슐화, 상속, 다형성 등 다른 OOP 개념들과 밀접한 관계를 가지고 있습니다. 이들 개념을 적절히 조합하여 사용함으로써, 개발자는 더 효율적이고 유지보수가 용이한 코드를 작성할 수 있습니다.

이러한 이유로 추상화는 객체 지향 프로그래밍의 근간을 이루는 중요한 개념입니다. 개발자는 이를 통해 더 나은 소프트웨어 설계와 구현을 하기 위해 추상화에 대한 이해는 필수적입니다.