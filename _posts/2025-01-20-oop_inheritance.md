---
title: "[Java] OOP Part5. 상속에 대하여(작성중)"
date: 2025-01-20 13:44 +0900
categories: [Java]
tags: [Java, OOP, Inheritance, Super, 상속]
---
> 자바 개발자에게 상속은 객체 지향 프로그래밍의 핵심 개념 중 하나로, 코드의 재사용성과 확장성을 크게 향상시키는 강력한 도구입니다. 상속을 통해 개발자는 기존 클래스의 속성과 메소드를 새로운 클래스에서 활용할 수 있어, 효율적이고 체계적인 코드 구조를 만들 수 있습니다. 오늘은 상속에 대한 개념과 사용방법 등에 대해 알아보겠습니다.

## 자바 상속의 기초

📋 상속의 개념과 의미 

Java 프로그래밍에서 상속은 객체 지향 프로그래밍의 중요한 개념입니다. 상속은 한 클래스가 다른 클래스의 속성(메서드 및 속성)을 획득하는 프로세스입니다. 즉, 기존의 클래스에 기능을 추가하거나 재정의하여 새로운 클래스를 만들 수 있습니다. 

상속을 사용하면 정보를 계층적 순서로 관리할 수 있으며, 캡슐화와, 추상화를 구현할 수 있습니다.

다른 클래스의 속성을 상속받는 클래스를 서브클래스(sub class, ,하위 클래스, 파생 클래스, 자식 클래스)라고 하며, 기존에 정의되어 있는 클래스를 상위 클래스(super class, 부모 클래스, 기초 클래스)라고 합니다.

📋 상속의 필요성과 장점 

상속이 필요한 이유 중 가장 큰 이유는 **재사용성과 확장성** 때문입니다. 개발을 할 때 비슷한 코드를 계속해서 작성하는 불필요를 줄일 수 있습니다. 이를 단순히 코드를 복사한다는 개념보다는 상속을 통해서 공통 기능이 변경되었을 때 모든 클래스를 수정하는 것이 아닌 상위 클래스만 수정할 수 있습니다. 또한 상속을 통해서 기존 클래스의 기본 기능을 받는 것뿐만 아리라 기능을 추가할 수 있어 확장성에 있어서도 유용합니다.

✅다음은 상속의 장점입니다. 

- **코드 재사용성 향상**  
상속을 통해 기존 클래스의 속성과 메서드를 새로운 클래스에서 재사용하는 것이 가능하다. 이는 개발 시간과 비용을 줄이는 데 도움이 됩니다. 
- **확장성 제공**  
상속을 사용하면 기존 클래스를 수정하지 않고도 새로운 기능을 추가할 수 있습니다.
- **계층적 구조 형성**  
클래스 간의 계층적 관계를 구성할 수 있습니다. 이러한 구조는 코드의 구조화를 돕고, 관리와 유지보수를 용이하게 만듭니다. 
- **다형성 구현**  
상속으로 오버라이딩과 오버로딩, 업캐스팅 등으로 인해 하나의 객체가 여러 가지 형태를 이루는 다형성을 구현할 수 있습니다. 

## 자바에서의 상속 구현 방법 (extends 키워드)

상속은 상속을 받을 클래스에 `extends`를 붙여 상속받을 클래스 명을 같이 작성해주면 됩니다. 
```java
class 자식클래스 extend 부모클래스 {...}
```

```java
class Parent{ // 부모 클래스
    
    ....
    ....
}
class Child extends Parent{ // 자식 클래스
    ....
    ....
}
```

![oop_inheritance.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/oop_inheritance.png?raw=true)

위 그림은 부모 클래스와 자식 클래스 간의 포함 관계를 나타낸 그림입니다. 그림과 같이 부모 클래스는 자식 클래스에 포함된 것으로 볼 수 있습니다. 부모 클래스에서 필드나 메서드를 추가하면 자식 클래스에서 기능이 추가됩니다. 자식은 물려받은 메서드를 오버라이딩과 오버로딩을 통하여 자신에 맞는 기능으로 수정할 수 있습니다.

> 왼쪽 그림은 UML 클래스 다이어그램에서 나타내는 상속 관계입니다. 실선에 비어있는 삼각형 화살표를 사용하며, 화살표는 자식 클래스에서 부모 클래스를 향하도록 그립니다.

## 상속의 주요 특징

자바의 상속에는 다음과 같은 특징을 가집니다.

- 자식 클래스에는 부모 클래스의 필드와 메소드만이 상속됩니다. 생성자와 초기화 블록은 상속되지 않습니다. 
- 부모 클래스의 접근 제어자가 `private`, `default`로 설정된 멤버 변수는 자식 클래스에서 상속받지만 접근할 수 없습니다.
- 단일 상속: 자바에서는 한 클래스가 오직 하나의 부모 클래스만을 상속받을 수 있습니다. 
    - 클래스 간의 관계가 더 명확해집니다.
    - 상속 구조가 단순해져 코드의 복잡성이 줄어듭니다.
    - 다중 상속으로 인한 모호성 문제를 방지할 수 있습니다.
- 다중 상속 금지: 자바에서는 다음과 같은 이유 때문에 다중 상속을 허용하지 않습니다.
    - **다이아몬드 문제**를 방지합니다. 여러 부모 클래스에서 동일한 이름의 메소드가 존재할 경우 발생하는 모호성을 피할 수 있습니다.
    - 클래스 간의 관계를 더 단순하고 명확하게 유지할 수 있습니다. 

> 자바에서는 클래스의 다중 상속을 직접적으로 지원하지 않지만, 다른 방법을 통해서 다중 상속과 유사한 효과를 얻을 수 있습니다. 이에는 인터페이스, 합성, 내부 클래스를 활용하는 방법이 있습니다.

- Object 클래스: 자바에소 모든 클래스의 최상위 부모 클래스입니다. 
    - 모든 클래스는 명시적으로 다른 클래스를 상속하지 않더라도 자동적으로 Object 클래스를 상속받습니다.
    - Object 클래스는 모든 객체가 공통으로 가져야 할 기본 기능을 제공합니다. 
    - toString(), equals(), hashCode() 등의 메소드를 포함하고 있어, 모든 클래스에서 이 메서드들을 사용하거나 오버라이딩할 수 있습니다. 

## 상속 관계에서의 클래스

```java
// 부모 클래스
public class Parent {
    public String name;
    public int publicField = 1;
    protected int protectedField = 2;
    private int privateField = 3;

    public Parent(String name) {
        this.name = name;
    }

    public void parentMethod(){
        System.out.println("Parent method");
    }
}
// 자식 클래스
public class Child extends Parent{
    public int childField = 4;

    public void childMethod(){
        System.out.println("Child method");
    }
    // 부모 생성자 블록 상속x (따로 선언)
    public Child(String name) {
        super(name);
    }
    public void accessParentMembers(){
        // public 부모 멤버 접근 가능
        System.out.println(publicField);
        // protected 부모 멤버 접근 가능
        System.out.println(protectedField);
        // private 부모 멤버 접근 불가
        // System.out.println(privateField);

        // 접근 가능
        parentMethod();
    }
}
```
이 예시에서 자식 클래스는 부모 클래스의 `public`와 `protected` 멤버에 접근할 수 있지만, `private` 멤버에는 접근할 수 없습니다. 또한 생성자를 상속받지 않기 때문에 자식 클래스에서 `super`로 부모 생성자를 부른 생성자를 따로 작성한 모습입니다. `super`에 대해서는 아래에서 다루겠습니다.

```java 
public class Main {
    public static void main(String[] args) {
        Parent parent = new Parent("Parent");
        Child child = new Child("Child");

        // 부모 멤버와 메서드 접근
        System.out.println(parent.name);
        System.out.println(parent.publicField);
        System.out.println(parent.protectedField);
        // private 접근 불가
        //System.out.println(parent.privateField);
        parent.parentMethod();

        // 자식 멤버와 메서드 접근
        System.out.println(child.name);
        System.out.println(child.childField);
        child.accessParentMembers();
        // 상속받은 멤버와 메서드 접근
        System.out.println(child.publicField);
        System.out.println(child.protectedField);
        child.parentMethod();
        // 부모의 private 접근 불가
        //System.out.println(child.privateField);
    }
}
```

위 예시를 보시면 접근 제어자의 영향 없는 곳은 자식 클래스에서 모두 사용할 수 있습니다. 이렇게 상속을 통하여 자식 클래스는 부모의 멤버와 메서드를 포함해 자신의 고유의 필드와 메서드도 사용할 수 있습니다.

## 메소드 오버라이딩

위에서 상속을 통하여 부모 클래스의 메서드를 자식 클래스에서 사용할 수 있는 모습을 보여드렸습니다. 그러나 만약 자식 클래스에서 부모 클래스의 메서드를 수정해서 사용하고 싶다면 어떻게 해야 할까요? 이 문제를 해결해줄 수 있는 것이 `메소드 오버라이딩(Method Overriding)`입니다. 즉, 메소드 오버라이딩을 통하여 자식 클래스가 부모 클래스의 메소드를 재정의할 수 있습니다. 

```java
public class Parent {
    String name;

    void display(){
        System.out.println("부모 클래스의 display() 메소드");
    }
    void introduce(String name){
        System.out.println("부모입니다.");
    }
}
public class Child extends Parent{
    @Override
    void display(){
        System.out.println("자식 클래스의 display() 메소드");
    }
    @Override
    void introduce(String name) {
        System.out.println(name + "입니다.");
    }
}
public class Main {
    public static void main(String[] args) {
        Parent parent = new Parent();
        Child child = new Child();

        parent.display();
        // 부모 클래스의 display() 메소드
        parent.introduce("아무개");
        // 부모입니다.

        child.display();
        // 자식 클래스의 display() 메소드
        child.introduce("아무개");
        // 아무개입니다.
    }
}
```

위 예시에서 Child 클래스는 display(), introduce() 메서드를 오버라이딩하고 있습니다. 객체의 실제 타입에 따라 오버라이딩된 메서드가 호출됩니다. 이렇게 메서드 오버라이딩을 통해 상속받은 메서드의 기능을 확장하거나 변경할 수 있습니다.

### 오버라이딩 규칙 

메서드 오버라이딩은 객체 지향 프로그래밍에서 중요한 개념이며, 다음과 같은 규칙을 따라야 합니다. 

**📋기본 규칙**

1. **메서드 시그니처**: 오버라이딩 메서드는 부모 클래스의 메서드와 동일한 이름, 매개변수 목록, 반환 타입을 가져야 합니다. 위 예시에서 `introduce()` 메서드의 경우 `void(반환 타입)`, `introduce(메서드 이름)`, `(String name) 동일한 매개변수 목록`을 가집니다.
2. **접근 제어자**: 오버라이딩 메서드의 접근 제어자는 부모 클래스의 메서드보다 더 제한적이어서는 안 됩니다.
3. **예외 처리**: 오버라이딩 메서드는 부모 클래스의 메서드보다 더 넓은 범위의 예외를 던질 수 없습니다.

**✚추가규칙**

1. **static, final 메서드**: static, final로 선언된 메서드는 오버라이딩할 수 없습니다.
2. **private 메서드**: private 메서드는 오버라이딩할 수 없습니다.
3. **생성자**: 생성자는 오버라이딩할 수 없습니다.
4. **super 키워드**: 오버라이딩 메서드 내에서 `super` 키워드를 사용하여 부모 클래스의 메서드를 호출할 수 있습니다. 

**⚠️주의사항**

- 오버라이딩은 런타임 다형성을 구현하는 데 사용되며, 상속 관계에서만 적용됩니다.
- 메서드 오버라이딩은 추상 메서드에 대해 반드시 이루어져야 합니다.
- 반환 타입이 객체인 경우, 오버라이딩 메서드는 원래 반환 타입의 하위 클래스를 반환할 수 있습니다.

## super 키워드

`super` 키워드와 `super()` 메서드는 자식 클래스에서 부모 클래스의 멤버에 접근하거나 생성자를 호출할 때 사용됩니다. 

### super 키워드의 용도



### super()를 이용한 부모 클래스 생성자 호출

## 상속과 생성자

- 자식 클래스 생성 시 부모 클래스 생성자 호출 순서
- 부모 클래스의 매개변수 있는 생성자 호출

## 상속과 접근 제어자

- public, protected, default, private 접근 제어자
- 상속 관계에서의 접근 제어자 동작

## 상속의 업캐스팅과 다운캐스팅

- 업캐스팅
- 다운 캐스팅

## 상속의 활용

- 코드 재사용성 증가
- 다형성 구현
- 계층적 구조 설계

## 상속의 주의사항

- 과도한 상속의 문제점
- 상속 vs 컴포지션

## 마무리

- 상속의 중요성
- 객체지향 프로그래밍에서의 상속의 역할
