---
title: "[Java] OOP Part3. 생성자"
date: 2025-01-10 13:39 +0900
categories: [Java]
tags: [Java, OOP, Constructor, 생성자]
---

> [저번 글](https://euihyunee.github.io/posts/oop_obj_class/)에서는 객체와 클래스에 대해 살펴보았습니다. 이번 글에서는 클래스에서 객체를 생성하기 위한 생성자에 대해 알아보겠습니다. 

## 생성자란? 

Java 생성자는 클래스로부터 객체를 만들 때 객체를 초기화하는 데 사용되는 메서드입니다. 클래스와 이름이 같으며, 메서드와 유사한 형태입니다. 그러나 생성자는 메서드와 달리 반환 타입이 없습니다. 

## 생성자 생성 규칙

Java 생성자를 만들 때는 다음과 같은 규칙을 따라야 합니다.

- 생성자의 이름은 클래스 이름과 같아야 한다.
- Java 생성자는 반환 타입이 없다. 반환 타입을 작성하지 말아야 한다.
- 같은 클래스 내에는 여러 생성자가 있을 수 있으며, 생성자 오버로딩(Overloading)이라 한다.
- 접근 제한자를 생성자와 함께 사용할 수 있습니다. 
- 객체 생성 할 때 호출되는 기본 생성자를 제공합니다. 생성자를 작성한 경우 기본 생성자를 제공하지 않습니다. 

## 생성자 유형

Java의 생성자에는 다음과 같은 세 가지 유형이 있습니다. 

- 기본 생성자 : 클래스에 생성자가 없는 경우 객체를 초기화하는 기본 생성자를 제공합니다.

```java
class People{
    // 기본 생성자(작성하지 않아도 사용가능)
    People() {}
}
```
```java
People people = new People();
```

- No-Args Constructor : 매개변수가 없는 생성자를 말한다. 형태는 생성자와 유사합니다. 멤버 변수를 초기화하고 객체 생성 시 원하는 로직을 수행할 수 있다는 점이 기본 생성자와의 차이점이다.

```java 
class People{
    String name;
    
    People(){
        String name = "초기값";
        System.out.println("객체 생성");
    }
}
```

```java
People people = new People();
// 출력 : 객체 생성
System.out.println(people.name);
// 출력 : 초기값
```

- 매개변수 생성자 : 하나 이상의 매개변수를 갖는 생성자를 말합니다. 값을 받아서 객체를 초기화하고 로직을 작성할 수 있습니다.

```java
class People{
    String name; 

    People(String name){
        this.name = name;
        System.out.println(name + "객체 생성");
    }
}
```

> `this`는 현재 객체의 인스턴스 변수를 가리키는 데 사용됩니다. 주로 매개변수와 인스턴스 변수의 이름이 같을 때 구분하기 위해 사용합니다. 

```java
People people = new People("John");
// 출력 : John객체 생성

System.out.println(people.name);
// 출력 : John
```

## 생성자 오버로딩(Overloading)

생성자 오버로딩은 클래스에 여러 생성자가 있는 것을 의미합니다. 클래스에는 여러 개의 생성자를 작성할 수 있으며, 서로 다른 매개변수를 가집니다. 

```java
// 기본 생성자
class People{
    String name;
    int age;

    People(){ 
        System.out.println("초기 객체 생성");
    }
    People(String name){
        this.name = name;
        this.age = 0;
    }
    People(String name, int age){
        this.name = name;
        this.age = age;
    }
}
```
다음과 같이 생성자를 여러 개 사용할 수 있습니다. 객체를 초기화할 때 필요한 매개변수에 따라 호출하는 방법을 선택할 수 있게 해주는게 생성자 오버로딩입니다.

```java
// no-arg 생성자 호출
People people1 = new People();
// 출력: 초기 객체 생성

// 매개변수 name 생성자 호출
People people2 = new People("John");
// 매개변수 name, age 생성자 호출
People people3 = new People("Dobby", 37);
```

> 생성자 매개변수에는 객체를 넣어도 됩니다. 객체를 넣는 것을 적절히 사용하면 좋은 설계 방식이 될 수 있지만, 과도하게 사용하면 코드의 복잡성을 증가시킬 수 있으므로 상황에 맞게 적절히 사용하는 것이 중요합니다.

## 마치며 

오늘은 생성자에 대해 알아보았습니다. 정리하자면 생성자는 객체를 초기화하기 위한 일종의 메서드입니다. 접근 제한자를 가질 수 있으며 이는 객체 생성을 제한하기 위함입니다. 생성자의 종류에는 기본 생성자, 매개변수가 있는 생성자, no-args 생성자가 있습니다.  

생성자는 오버로딩을 통해 여러 개의 매개변수가 다른 생성자를 정의할 수 있습니다. 또한 객체 생성의 유연성을 높이고, 코드의 가독성과 유지보수성을 개선할 수 있습니다. 

생성자는 자바에서 객체 지향 프로그래밍의 핵심 요소로, 다양한 형태의 생성자를 정의하고 적절한 접근 제한자를 설정함으로써 코드의 유연성과 안정성을 높일 수 있기에 Java 개발자는 생성자에 대한 높은 이해가 필요합니다.