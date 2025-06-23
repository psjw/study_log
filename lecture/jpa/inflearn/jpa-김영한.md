---
title: 김영한 JPA 강의 통합 정리
topic: JPA
tags: [JPA, ORM, Entity, Hibernate, Spring Data JPA]
---

# 📘 인프런 - 실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발 (김영한)

---



## 📅 2025-06-19 - 도메인 분석 설계

### 💡 학습 주제
- 엔티티 설계 시 주의사항
- 테이블/컬럼명 자동 생성 전략

---

### 🧠 주요 개념 요약

| 항목                          | 설명                                                                 |
|-----------------------------|----------------------------------------------------------------------|
| 엔티티에는 Setter 사용 지양     | 변경 포인트가 많아져 추적이 어렵고 유지보수성이 떨어짐                                       |
| 연관관계는 지연 로딩(LAZY)       | `EAGER`는 JPQL과 충돌 시 예측 불가능한 SQL + N+1 문제 발생 위험                             |
| 컬렉션은 필드에서 초기화         | Hibernate는 프록시로 대체하므로 NPE 방지 위해 초기화 필요 (`new ArrayList<>()`)              |
| Naming 전략                    | `SpringPhysicalNamingStrategy` → 카멜 케이스 → 스네이크 케이스 변환 등 자동 규칙 적용         |

---

### 🧪 실습 코드

```java
@Entity
public class Member {

    @Id @GeneratedValue
    private Long id;

    private String name;

    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();

}

Member member = new Member();

// 영속화 전: 필드 초기화된 컬렉션
System.out.println(member.getOrders().getClass()); 
// → class java.util.ArrayList

em.persist(member);

// 영속화 후: Hibernate 프록시 컬렉션
System.out.println(member.getOrders().getClass());
// → class org.hibernate.collection.internal.PersistentBag
```
---



## 📅 2025-06-21 - 웹 계층 개발

### 💡 학습 주제
- 준영속 엔티티를 수정하는 2가지 방법
- 변경 감지와 병합(merge) 차이

---

### 🧠 주요 개념 요약

| 항목 | 설명 |
|------|------|
| 변경 감지 (Dirty Checking) | 영속성 컨텍스트에서 엔티티를 조회한 후 데이터를 수정하여 커밋 시점에 UPDATE SQL 실행 |
| 병합 (Merge) | 준영속 상태의 엔티티를 영속 상태로 변경할 때 사용. 다만 **모든 필드를 덮어쓰기** 때문에 `null` 값이 반영될 위험 존재 |
| 변경 시 고려사항 | Dirty Checking 방식이 부분 변경이 가능하고, 실수 가능성이 적으므로 권장됨 |

---

### 🧪 실습 코드

```java
@Entity
public class Member {

    @Id @GeneratedValue
    private Long id;

    private String name;

    private String address;

}

//변경 감지 -> 이름 만 변경됨
Member member1 = em.find(Member.class, memberId);
member1.setName("AA");

//병합 사용 ->  새로 생성된 회원의 모든 필드로 변경 하므로 Address가 null로 변경된다.
Member member2 = new Member();
member2.setName("AA");
 em.merge(memer2);

```
---


# 📘 인프런 - 실전! 스프링 부트와 JPA 활용2 -  API 개발과 성능 최적화 (김영한)

## 📅 2025-06-23 - API개발 고급 - 지연로딩과 조회 성능 최적화

### 💡 학습 주제
- API개발시 성능 최적화
- xToOne(@ManyToOne, @OneToOne)의 관계에서의 조회 성능 최적화 방법

---

### 🧠 주요 개념 요약

| 항목 | 설명 |
|------|------|
| **InvalidDefinitionException** | `com.fasterxml.jackson.databind.exc.InvalidDefinitionException: No serializer found for class org.hibernate.proxy.pojo.bytebuddy.ByteBuddyInterceptor` → LAZY 로딩으로 인해 Jackson이 Proxy 객체를 직렬화하려다 실패함 |
| **@JsonIgnore** | Entity 반환 시 양방향 매핑이 있을 경우, 순환 참조로 인한 무한 루프 방지를 위해 필드에 `@JsonIgnore`를 반드시 설정해야 함 |
| **N+1 문제 원인** | 예: Order 2건 조회 시, 각 Order의 연관된 Member/Delivery가 LAZY 로딩으로 각각 추가 쿼리를 유발함. EAGER 사용 시 더 많은 예측 불가능한 쿼리 발생 가능 |
| **N+1 해결 방법** | 대부분은 **fetch join**으로 해결. 성능 병목 발생 시 Dto 조회 고려. 최후의 수단으로 Native SQL, `JdbcTemplate` 활용 가능 |

---



### 🧪 실습 코드
#### 📌 1. 엔티티 설정

```java
@Entity
public class Order {

    @Id @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "MEMBER_ID")
    private Member member;

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "DELIVERY_ID")
    private Delivery delivery;
}

@Entity
public class Member {

    @Id @GeneratedValue
    private Long id;

    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();
}

```
📌 2. Fetch Join 사용 예시
```java
List<Order> orders = em.createQuery(
    "select o from Order o " +
    "join fetch o.member m " +
    "join fetch o.delivery d", Order.class)
.getResultList();
```

📌 3. DTO 직접 조회 예시 (필요한 필드만 조회)
```java
List<OrderSimpleQueryDto> result = em.createQuery(
    "select new jpabook.jpashop.repository.ordersimpequery.OrderSimpleQueryDto(" +
    "o.id, m.name, o.orderDate, o.status, d.address) " +
    "from Order o " +
    "join o.member m " +
    "join o.delivery d", OrderSimpleQueryDto.class)
.getResultList();
```
---
