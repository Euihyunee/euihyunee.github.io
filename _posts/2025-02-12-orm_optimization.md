---
title: "📋 ORM의 개념 & 성능 최적화 방법"
date: 2025-02-12 12:10 +0900
categories: [Database]
tags: [ORM]
---

![orm.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/orm.png?raw=true)


## ORM이란?

ORM(Object-Realational Mapping)은 객체 지향 프로그래밍 언어와 관계형 데이터베이스 간의 데이터 변환을 자동화하는 기술입니다. 객체 모델과 데이터베이스 테이블을 매핑함으로써 SQL 쿼리 대신 메서드 호출로 데이터 조작이 가능하며, 객체-관계 간의 불일치 문제를 해결합니다.

개발자가 객체 지향 언어로 설계한 데이터 모델을 데이터베이스의 데이터를 더 쉽게 조작할 수 있습니다. ORM을 사용하면 SQL 쿼리 없이 데이터를 데이터베이스에 저장하고 관리할 수 있기 때문에 개발자는 객체 지향 프로그래밍에 더욱 집중할 수 있으며, 가독성과 유지 보수성 등을 향상시켜 효율적인 개발이 가능합니다. 

## ORM이 나오게 된 이유

그동안 개발자는 데이터 모델의 관계를 JDBC와 같은 데이터베이스 접근 기술을 사용하여 관계형 데이터베이스(이하 RDB)와 상호작용했습니다. 데이터베이스 작업을 위한 많은 양의 코드가 필요했으며, 데이터베이스 스키마 변경 시 관련 코드를 모두 수정해하는 단점이 있었습니다. 

ORM의 등장으로 개발자들은 객체 지향적인 방식으로 데이터베이스를 다룰 수 있게 되었고, 생산성과 유지보수성이 크게 향상되었습니다. 그러나 여전히 복잡한 쿼리나 성능이 중요한 경우에는 JDBC를 직접 사용하는 경우도 있습니다.

## ORM을 지원하는 기술 

ORM은 다양한 프로그래밍 언어와 프레임워크에서 지원되고 있습니다. 주요 언어별로 널리 사용되는 ORM 기술들은 다음과 같습니다.

| 언어/플랫폼 | ORM 기술 | 특징 |
|-------------|----------|------|
| Java | Hibernate | 가장 널리 사용되는 Java ORM, 고성능 |
| | JPA (Java Persistence API) | Java EE 표준 ORM 명세 |
| | EclipseLink | JPA 구현체, 다양한 데이터 소스 지원 |
| Python | SQLAlchemy | 강력한 쿼리 기능, 유연성 |
| | Django ORM | Django 웹 프레임워크 내장 ORM |
| | Peewee | 경량화된 ORM, 간단한 사용법 |
| .NET (C#) | Entity Framework | Microsoft 공식 ORM, LINQ 지원 |
| | Dapper | 마이크로 ORM, 높은 성능 |
| | NHibernate | 다양한 플러그인 지원 |
| JavaScript/Node.js | Sequelize | 다중 DB 지원, Promise 기반 |
| | TypeORM | TypeScript 친화적, 데코레이터 문법 |
| | Prisma | 현대적 ORM, 강력한 타입 안전성 |
| Ruby | ActiveRecord | Rails 기본 ORM, 직관적 API |
| | Sequel | 유연한 도메인 특화 언어(DSL) 제공 |
| PHP | Eloquent | Laravel 프레임워크 ORM |
| | Doctrine | 독립적 ORM, 다양한 DB 지원 |
| Go | GORM | Go 언어를 위한 완전한 기능의 ORM |
| | SQLBoiler | 코드 생성 기반 ORM |

## ORM 장단점 

ORM은 객체와 관계형 데이터베이스를 쉽게 연결해주고 많은 이점을 제공하지만 몇 가지 단점도 가지고 있습니다. ORM의 주요 장단점은 다음과 같습니다.

### ✅장점

1. ***개발 생산성 향상***
    - 객체 지향적인 코드로 직관적이고 비즈니스 로직에 더 집중할 수 있습니다.
    - SQL 쿼리를 직접 작성할 필요가 없어 개발 시간과 비용이 감소합니다.
    - 코드 재사용성이 높아져 유지보수가 용이해집니다.
2. ***데이터베이스 독립성 ***
    - 특정 데이터베이스에 종속되지 않아 다양한 데이터베이스 시스템을 쉽게 전화할 수 있습니다.
    - 데이터베이스 변경 시 최소한의 코드 수정으로 대응할 수 있습니다.
3. ***객체-관계 불일치 해소***
    - 객체 모델과 관계형 데이터베이스 사이의 패러다임 차이를 줄여줍니다.
    - 개발자가 객체 지향적 사고에 집중할 수 있게 해줍니다.

### ✅단점

1. ***성능 이슈*** 
    - 복잡한 쿼리나 대량의 데이터를 처리할 때 성능 저하가 발생할 수 있습니다.
    - ORM이 생성한 쿼리가 최적화되지 않을 수 있어 직접 작성한 SQL보다 비효율적일 수 있습니다.
2. ***학습 곡선***
    - ORM 프레임워크를 효과적으로 사용하기 위해 추가적인 학습이 필요합니다.
    - 복잡한 기능을 사용할 때 ORM의 내부 동작을 이해해야 할 수 있습니다.
3. ***유연성 제한*** 
    - 데이터베이스의 고급 기능이나 특정 최적화 기법을 사용하기 어려울 수 있습니다.
    - 매우 복잡한 쿼리의 경우 ORM으로 표현하기 어려워 결국 SQL을 직접 작성해야 할 수 있습니다.
4. ***디버깅 어려움*** 
    - ORM이 생성한 SQL 쿼리를 직접 제어하기 어려워 성능 문제 해결이 복잡해질 수 있습니다.
    - 데이터베이스 작업의 세부 사항이 추상화되어 문제 진단이 어려울 수 있습니다.

## ORM 성능을 최적화하는 방법

ORM은 단순한 CRUD 작업에는 효율적이지만, 복잡한 데이터베이스 구조와 쿼리에서는 성능 문제가 발생할 수 있습니다. 특히 엔티티 수가 많고 데이터 양이 큰 경우, 자동 생성된 쿼리가 최적화된 네이티브 SQL에 비해 성능이 떨어질 수 있습니다. 

따라서 ORM의 성능을 최적화함으로써 문제들을 해결하고, 애플리케이션의 응답과 확장성을 개선할 수 있습니다. 최적화하는 방법들을 자바 언어에 기초하여 아래에서 다루겠습니다.

### N+1 문제 해결

N+1 문제는 연관된 엔티티를 조회할 때 발생하는 성능 이슈입니다. 이를 해결하는 방법에는 `페치 조인`과 Spring Data JPA의 `@EntityGraph`가 있습니다.

> N+1문제는 하나의 쿼리로 N개의 데이터를 조회했을 때, 연관된 엔티티를 가져오기 위해 추가로 N번의 쿼리가 실행되는 현상을 말합니다.
{: .prompt-info }

#### 페치 조인 사용 

JPQL에서 페치 조인을 사용하여 연관된 엔티티를 한 번에 조회하는 방법입니다. 여러 번의 데이터베이스 조회를 단일 쿼리로 대체하여 불러들이는 방법입니다. 

> 단, 페치 조인을 과도하게 사용하면 메모리 사용량이 증가할 수 있으므로, 필요한 경우에만 적절히 사용해야 합니다.
{: .prompt-warning }

```java
String sql = "SELECT p FROM Order o JOIN FETCH o.member m JOIN FETCH o.delivery d";
List<Order> orders = em.createQuery(sql, Order.class).getResultList();
```

이 코드는 JPA를 사용하여 데이터베이스에 주문(Order) 정보를 조회하는 JPQL 쿼리입니다.  
- 이 쿼리는 Order 엔티티를 기준으로 연관된 Member와 Delivery 엔티티를 함께 조회합니다.
- `JOIN FETCH`는 연관 엔티티를 함께 조회하여 지연 로딩으로 인한 추가 쿼리 발생을 방지합니다.
- `em.createQuery()`를 사용하여 JPQL 쿼리를 생성합니다.
- `getResultList()`를 호출하여 쿼리를 실행하고 결과를 List<Order>로 받습니다.

#### @EntityGraph 사용

Spring Data JPA에서 `@EntityGraph`를 사용하여 연관 엔티티를 함께 조회할 수 있습니다.

```java
@EntityGraph(attributePaths = {"member", "delivery"})
List<Order> findAll();
```

이 메서드가 호출되면, Order 엔티티를 조회할 때 member와 delivery 연관 엔티티도 함께 즉시 로딩됩니다. 어노테이션(@EntityGraph)는 기본적으로 지연 로딩으로 설정한 연관 관계라 해도 즉시 로딩으로 동작합니다.  
결과적으로 단일 쿼리로 Order, Member, Delivery 엔티티를 모두 조회하여 N+1 문제를 방지할 수 있습니다.

### 지연 로딩 활용

연관 엔티티를 즉시 로딩하지 않고 필요할 때만 로딩하도록 설정합니다. 필요한 시점에만 데이터를 로드하여 초기 로딩 시간과 메모리 사용을 줄일 수 있습니다. 따라서 유연하게 데이터를 로드할 지 결정할 수 있습니다.

> 📋지연 로딩(Lazy Loading)과 즉시 로딩(Eager Loading)은 데이터베이스에서 데이터를 조회하는 두 가지 주요 방식입니다. 간략하게 지연 로딩은 연관된 엔티티를 사용할 때까지 조회하지 않고, 즉시 로딩은 연관된 엔티티도 함께 조회하는 방식입니다.
{: .prompt-info }

```java
@Entity
public class Order{
    @ManyToOne(fetch = FetchType.LAZY)
    private Member member;
}
```

- `@Entity`는 해당 클래스가 JPA 엔티티임을 나타냅니다.
- `@ManyToOne`은 다대일 관계를 정의합니다.
- `FetchType.LAZY`는 이 관계에 대해 지연 로딩을 사용하도록 지정합니다. 따라서 Order 엔티티를 조회할 때 연관 엔티티인 Member 엔티티는 즉시 로드되지 않습니다.

### 벌크 연산 사용

대량의 데이터를 처리할 때는 벌크 연산을 사용하여 성능을 향상시킬 수 있습니다.

> 벌크 연산(Bulk Operation)은 데이터베이스에서 대량의 레코드를 단일 작업으로 처리하는 기술입니다. 주로 **성능 최적화**를 위해 사용되며, 여러 건의 데이터를 한 번에 수정/삽입/삭제할 때 효과적입니다.
{: .prompt-info }

```java
String sql = "UPDATE Product p SET p.price = 
            p.price * 1.1
            WHERE p.category = :category";
int updatedCount = em.createQuery(sql)
                    .setParmeter("category", "ELECTRONICS")
                    .executeUpdate();
```

- 이 JPQL 쿼리는 Product 엔티티의 price 필드를 10% 인상하는 업데이트 작업을 수행합니다.
- `p.price * 1.1`은 현재 가격에 1.1을 곱하여 10% 인상된 가격을 계산합니다.
- `:category`는 파라미터 바인딩을 위한 플레이스홀더입니다.
- `em.createQuery(sql)`은 EntityManager를 사용하여 JPQL 쿼리 객체를 생성합니다.
- `.setParameter("category", "ELECTRONICS")`는 쿼리의 :category 파라미터에 "ELECTRONICS" 값을 바인딩합니다.
- `.executeUpdate()`는 업데이트 쿼리를 실행하고 영향받은 엔티티의 수를 반환합니다.
- updatedCount 변수에는 업데이트된 Product 엔티티의 수가 저장됩니다.

### 읽기 전용 쿼리 최적화 

변경 작업 필요없이 조회만 필요한 경우 읽기 전용 쿼리를 사용하여 메모리 사용량을 줄일 수 있습니다.

- 읽기 전용 쿼리를 사용하여 조회된 엔티티의 데이터 변경을 방지할 수 있습니다. 
- 영속성 컨텍스트에서 스냅샷 생성을 생략하기 때문에 메모리를 20~30% 절약할 수 있습니다.
- ***Dirty Checking(변경 감지)*** 로직을 생략하여 CPU의 리소스를 절약할 수 있습니다.

```java
TypedQuery<Order> query = em.createQuery(
    "SELECT o FROM Order o", Order.class);
query.setHint("org.hibernate.readOnly", true);
List<Order> orderList = query.getResultList();
```

- `em.createQuery()`를 사용하여 JPQL 쿼리를 생성합니다.
- "SELECT o FROM Order o"는 모든 Order 엔티티를 조회합니다.
- `setHint()` 메서드를 사용하여 쿼리에 "org.hibernate.readOnly"라는 힌트를 추가하여 조회된 엔티티들이 읽기 전용 모드로 관리됩니다.


### 페이징 처리

대량의 데이터를 조회할 때는 페이징 처리를 통해 성능을 개선할 수 있습니다.

- 성능 최적화: 필요한 데이터만 조회하여 메모리 사용량과 처리 시간을 줄입니다.
- 유연성: 페이지 크기와 시작 위치를 쉽게 조정할 수 있습니다.

> 페이징 처리(Paging)는 대량의 데이터를 작은 단위로 나누어 조회하는 기술로, 데이터베이스 조회 성능과 사용자 경험을 개선합니다.
{: .prompt-info }

```java
TypedQuery<Product> query = em.createQuery(
        "SELECT p FROM Product p". Product.class);
query.setFirstResult(0);
query.setMaxResult(10);
List<Product>  products = query.getResultList();
```

- `setFirstResult(0)`는 결과 집합의 시작 위치를 지정합니다. 0은 첫 번째 레코드부터 시작함을 의미합니다.
- `setMaxResults(10)`는 최대 반환 레코드 수를 10개로 제한합니다. 
- 두 메서드를 조합하여 첫 번째 페이지(10개 항목)를 조회하도록 설정합니다.


## 마치며

이상으로 ORM의 개념과 성능 최적화 방법에 대해 살펴보았습니다. ORM은 객체 지향 프로그래밍과 관계형 데이터베이스 간의 간극을 효과적으로 해소하며, 개발 생산성을 크게 향상시킵니다. 그러나 성능 이슈와 같은 단점도 존재하므로, 이를 적절히 관리하고 최적화하는 것이 중요합니다.

성능 최적화를 위해 N+1 문제 해결, 지연 로딩 활용, 벌크 연산 사용, 읽기 전용 쿼리 최적화, 페이징 처리 등의 기법을 적용할 수 있습니다. 이러한 기법들을 상황에 맞게 적절히 활용하면 ORM의 장점을 최대한 살리면서 성능 문제를 최소화할 수 있습니다.

ORM을 효과적으로 사용하기 위해서는 지속적인 학습과 실제 프로젝트에서의 경험이 필요합니다. 데이터베이스의 특성과 애플리케이션의 요구사항을 잘 이해하고, 적절한 ORM 기술과 최적화 전략을 선택하는 것이 중요합니다. 이를 통해 개발 생산성과 애플리케이션 성능을 모두 높일 수 있을 것입니다.