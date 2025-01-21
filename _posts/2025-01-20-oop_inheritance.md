---
title: "[Java] OOP Part5. 상속에 대하여"
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
- `final` 키워드를 통해서 상속을 허용하지 않는 멤버와 메서드를 지정할 수 있습니다. 다만 멤버를 final로 선언되면 상수로 취급되어 초기화 이후에는 값을 수정할 수 없습니다.

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

📋먼저, 부모클래스의 멤버에 접근할 수 있습니다. `super` 키워드를 사용하여 부모 클래스의 필드나 메서드에 접근할 수 있습니다. 자식 클래스에서 부모 클래스의 멤버와 이름이 같은 멤버를 정의했을 때 구분하기 위해 사용합니다. 

```java
public class Animal {
    String name = "동물";
}
public class Dog extends Animal{
    String name = "강아지";

    void print(){
        System.out.println("부모: "+ super.name +
                ", 자식: " + name);
    }
}
public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.print();
    }
}
```

```text
실행결과: 부모: 동물, 자식: 강아지
```

📋 두 번째로, 자식 클래스에서 부모 클래스의 메서드를 오버라이딩한 경우, `super` 키워드를 사용하여 부모 클래스의 원래 메서드를 호출할 수 있습니다.

```java
public class Animal {
    String name = "동물";
    void print(){
        System.out.println("부모이름: " + name);
    }
}
public class Dog extends Animal{
    String name = "강아지";

    @Override
    void print(){
        super.print();
        System.out.println("자식이름: " + name);
    }
}
public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.print();
    }
}
```

### super()를 이용한 부모 클래스 생성자 호출

상속 관계에서 생성자는 상속되지 않는다고 설명드렸습니다. 생성자는 클래스와 동일한 이름을 가지기에 상속 시 자식 클래스의 이름과 충돌이 발생할 수 있고, 각 클래스의 고유한 초기화 요구사항들을 만족시킬 수 없기 때문에 생성자는 상속되지 않습니다. 생성자를 상속하지 않음으로써 각 클래스는 자신만의 초기화 로직을 정의할 수 있고, 객체 생성 과정의 명확성과 안정성을 보장할 수 있습니다. 대신에 `super()`와 `base()` 같은 키워드를 통해 부모 클래스의 생성자를 호출하여 초기화 로직을 재사용할 수 있습니다.

```java
public class Animal {
    String name;
    String species;

    public Animal(String name, String species) {
        this.name = name;
        this.species = species;
    }
    void print(){
        System.out.print(this.name + " ");
    }
}
public class Dog extends Animal{
    String breed;

    Dog(String name, String breed) {
        super(name, "Dog");
        this.breed = breed;
    }
    @Override
    void print(){
        System.out.println("품종: " + this.breed +
                ", 종: " + this.species +
                ", 이름: " + this.name);
    }
}
public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog("몽이", "허스키");
        dog.print();
    }
}
```

```text
실행결과: 품종: 허스키, 종: Dog, 이름: 몽이
```

위 예시에서 Animal 부모 클래스에서는 이름과 종 멤버를 가지고 있고, Dog 클래스에서는 품종을 가지고 있습니다. Dog 클래스에서 `super(name,"Dog")`를 호출하여 부모 클래스인 Animal의 생성자를 호출합니다. 이를 통해 부모 클래스의 초기화 로직을 재사용하고, Dog 클래스는 `breed` 필드만 초기화하면 됩니다. 

## 상속과 생성자

상속 관계에서 자식이 객체를 생성하면 부모의 생성자를 기본적으로 먼저 호출합니다. 이 과정은 다음과 같습니다. 

1. 자식 클래스 생성자 호출
2. 자식 클래스 생성자의 첫 줄에서 부모 클래스의 생성자를 호출합니다. 
    - 이때 super()로 명시적인 표현이 있다면 작성한 super()를 호출합니다.
    - 명시적인 표현이 없다면 컴파일러가 자동으로 부모의 기본 생성자를 호출합니다.
3. 부모 클래스의 생성자가 실행을 마치면 자식 클래스의 생성자로 돌아와 나머지 초기화 작업을 수행합니다.

> 단, 자식 클래스의 생성자에 `super()`를 명시적으로 호출할 경우, 반드시 생성자의 첫 줄에 위치해야 합니다. 또한 부모 클래스에 기본 생성자가 없고 매개변수가 있는 생성자만 있는 경우, 자식 클래스의 생성자에서 반드시 `super(매개변수)`를 사용하여 명시적으로 부모 생성자를 호출해야 합니다. 
{: .prompt-info }

## 상속과 접근 제어자

상속에서 접근 제어자에 따라 상속의 동작이 달라지기에, 이를 잘 이해하는 것이 중요합니다. 아래에서 설명하겠습니다.

1. **public**: 가장 개방적인 접근 수준으로 상속 시 자식 클래스에서도 public으로 유지되며, 어디서든 접근이 가능합니다. 
2. **protected**: 같은 패키지 내의 클래스와 상속받은 자식 클래스 내에서 접근하며, 상속 시 자식 클래스에서 protected 또는 public 으로 사용할 수 있습니다. 
3. **default**: 같은 패키지 내에서만 접근이 가능하며, 다른 패키지에서 상속을 했더라도 부모 클래스에 접근할 수 없습니다. 
4. **private**: 가장 제한적인 접근이며 해당 클래스 내에서만 접근이 가능합니다. 상속 시 자식 클래스에서 직접 접근할 수 없지만, 부모 클래스의 public 또는 protected 메서드를 통해 간접적으로 접근할 수 있습니다.

### ⚠️ 주의사항

1. 자식 클래스에서 부모 클래스의 멤버에 대한 접근 제어자를 더 제한적으로 변경할 수 없습니다. 예를 들어, 부모 클래스의 public 메서드를 자식 클래스에서 protected나 private으로 변경할 수 없습니다. 
2. private 멤버는 자식 클래스에 상속되지만 직접 접근할 수 없습니다. 이로 인해 캡슐화를 유지하면서 상속을 통한 코드의 재사용을 가능하게 합니다. 

## 상속의 업캐스팅과 다운캐스팅

업캐스팅과 다운캐스팅은 상속 관계에 있는 클래스 간의 타입 변환입니다. 이를 통해서 한 객체가 여러 가지 형태를 이룰 수 있는 다형성을 가지게 됩니다. 

### 업캐스팅(Upcasting)

업캐스팅은 자식 클래스의 객체를 부모 클래스의 타입으로 변환하는 것을 말합니다. 

```java
Animal animal = new Dog();
```

```java
class Animal {
    public void makeSound() {
        System.out.println("동물이 소리를 냅니다");
    }
}
class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("고양이가 야옹합니다");
    }
}
class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("개가 짖습니다");
    }
}
public class Main {
    public static void main(String[] args) {
        Animal dog = new Dog(); // 업캐스팅
        Animal cat = new Cat(); // 업캐스팅

        dog.makeSound(); // "개가 짖습니다" 출력
        cat.makeSound(); // "고양이가 야옹합니다" 출력
    }
}
```

업캐스팅은 자식 클래스와 부모 클래스 간에 별도의 캐스팅 연산자가 필요 없어 묵시적으로 수행될 수 있습니다. 또한 업캐스팅은 자식 클래스의 객체를 부모 클래스 타입으로 다룰 수 있어 위의 예시처럼 다양한 동물 클래스들을 하나의 Animal 타입으로 처리할 수 있습니다. 

하지만 상속의 업캐스팅은 자식 클래스의 고유한 멤버나 메서드에 접근할 수 없습니다. 오로지 부모 클래스에서만 정의된 멤버와 메서드에만 접근할 수 있으며, 자식 클래스에서만 정의된 멤버와 메서드는 사용할 수 없습니다. 

또한, 업캐스팅된 객체에서 오버라이딩된 메서드를 호출할 경우, 부모 클래스의 메서드가 아닌 자식 클래스에서 오버라이딩된 메서드가 실행됩니다. 

### 다운캐스팅(Downcasting)

다운캐스팅은 부모 클래스 타입으로 업캐스팅된 객체를 다시 자식 클래스 타입으로 변환하는 것을 말합니다. 이는 업캐스팅과 달리 묵시적으로 수행할 수 없기에 명시적인 캐스팅이 필요합니다.  
다운캐스팅은 업캐스팅된 객체에선 사용하지 못했던 자식 클래스의 고유한 멤버와 메서드에 접근하기 위해 사용됩니다. 

```java
Animal animal = new Dog();
Dog dog = (dog) animal;
```

이렇게 다운캐스팅을 통하여 부모 클래스의 타입으로 다뤘던 자식 클래스 객체들을 필요할 때 원래 타입으로 복원하여 자식 클래스의 고유 멤버와 메서드를 호출할 수 있게 됩니다. 

하지만, 다운캐스팅은 컴파일 시점에서 오류를 잡아내지 못하고 런타임에 `ClassCastException`을 발생시킬 수 있기에 다운캐스팅 전에 `instanceof` 연산자를 활용해 객체의 실제 타입을 확인해야 합니다.

```java
class Animal {
    void makeSound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Bark");
    }

    void wagTail() {
        System.out.println("Tail wagging");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal animal = new Dog(); // 업캐스팅
        animal.makeSound(); // "Bark" 출력 (다형성)

        // 안전한 다운캐스팅
        if (animal instanceof Dog) {
            Dog dog = (Dog) animal; // 다운캐스팅
            dog.wagTail(); // "Tail wagging" 출력
        }

        // 잘못된 다운캐스팅 예시
        Animal genericAnimal = new Animal();
        try {
            Dog wrongDog = (Dog) genericAnimal; // 런타임 오류 발생
        } catch (ClassCastException e) {
            System.out.println("잘못된 다운캐스팅: " + e.getMessage());
        }
    }
}

```


## 상속의 주의사항

이렇게 자바에서의 상속은 코드 재사용성과 다형성을 구현할 수 있는 장점이 있지만 주의해야할 몇 가지 사항들이 있습니다.

- 상속은 "is-a(~은 ~이다)" 관계가 명확한 경우에만 사용해야 합니다. 단순히 코드 재사용을 위해 상속을 하는 것은 옳지 않습니다. 이때는 상속 대신 컴포지션을 고려해보는 것이 좋습니다.
- 부모 클래스의 생성자에서 오버라이드 가능한 메서드를 호출하면 예기치 않은 문제가 발생할 수 있습니다. 자식 클래스보다 부모 클래스의 생성자가 먼저 실행되므로, 오버라이드된 메서드가 예상치 못한 동작을 할 수 있습니다.
- 자바의 상속은 다이아몬드 문제, 즉 중복된 메서드 이름이 호출되어 다르게 구현하는 것을 막기 위해 다중 상속을 금지합니다. 
- 너무 깊은 상속 계층은 코드를 복잡하게 만들고 유지보수를 어렵게 합니다. 이런 경우 상속보다 인터페이스를 사용하는 것이 더 유연한 설계를 가능하게 합니다. 

## 마무리

상속은 객체 지향 프로그래밍의 핵심 개념 중 하나로, 코드의 **재사용성과 확장성**을 크게 향상시키는 강력한 도구입니다. 이 글에서 우리는 자바에서의 상속의 기본 개념부터 구현 방법, 주요 특징, 그리고 주의해야할 점들까지 폭넓게 살펴보았습니다. 상속을 통해 개발자는 기존 클래스의 속성과 메서드를 새로운 클래스에서 활용할 수 있어, 효율적이고 체계적인 코드 구조를 만들 수 있습니다.

하지만, 상속의 무분별한 사용은 마이너스입니다. 경우에 따라 상속을 사용하는 것보다 컴포지션(합성)이나 인터페이스 사용이 더 나은 선택일 수 있습니다. 그렇기에 자바 개발자는 상속에 대한 이해를 길러 적절히 사용함으로써, 더 효율적이고 유지보수가 용이한 소프트웨어를 개발할 수 있습니다.
