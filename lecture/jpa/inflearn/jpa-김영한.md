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
- XToOne 관계는 fetch join으로 1회에 조회
- XToMany 관계는:
  - 1) `@BatchSize` or `default_batch_fetch_size`로 IN 절 최적화
  - 2) DTO를 루트/서브 쿼리 방식으로 나누어 조회
  - 3) Flat DTO로 join 후 메모리에서 groupBy 재조합

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
#### 📌 2. Fetch Join 사용 예시
```java
List<Order> orders = em.createQuery(
    "select o from Order o " +
    "join fetch o.member m " +
    "join fetch o.delivery d", Order.class)
.getResultList();
```

#### 📌 3. DTO 직접 조회 예시 (필요한 필드만 조회)
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




# 📘 인프런 - 실전! 스프링 부트와 JPA 활용2 -  API 개발과 성능 최적화 (김영한)

## 📅 2025-06-29 - API개발 고급 - 컬렉션 조회 최적화

### 💡 학습 주제

- API 성능 최적화를 위한 엔티티 조회 전략
- @OneToMany 관계에서 발생하는 N+1 문제 해결 방법
- 컬렉션 페이징 최적화 전략 (@BatchSize, DTO 분할 조회 등)
  
---

### 🧠 주요 개념 요약

| 항목 | 설명 |
|------|------|
| **@OneToMany + LAZY** | LAZY 설정 시 연관 엔티티를 순차적으로 조회하며 N+1 문제가 발생 |
| **@JsonIgnore 주의** | 양방향 참조로 인해 무한 루프 방지에 사용되나, Entity 직접 반환은 API 스펙이 불안정해지므로 DTO 사용을 권장 |
| **Fetch Join + distinct** | SQL은 1번만 실행되지만 페이징이 불가능 (컬렉션 join이므로 중복 row 발생) |
| **페이징 최적화 방법 #1** | XToOne(@ManyToOne, @OneToOne)은 fetch join으로 조회하고, 컬렉션은 LAZY로 두고 `@BatchSize`나 `hibernate.default_batch_fetch_size`로 in절 처리 |
| **BatchSize 한계** | DB의 in절 제한을 초과하면 오류 발생 (예: Oracle은 1000개 제한) |
| **페이징 최적화 방법 #2** | DTO로 루트 조회 후, 컬렉션을 개별 쿼리로 조회하여 매핑. 또는 flat DTO로 조회 후 메모리에서 그룹핑 |
| **실무 권장 순서** | 1) 엔티티 조회로 접근 → 2) BatchSize 최적화 → 3) DTO 분할 조회 → 4) Flat DTO → 5) Native SQL or JdbcTemplate


---



### 🧪 실습 코드
#### 1. DTO 직접 조회 - N+1 방식

```java
List<OrderQueryDto> result = findOrders();

result.forEach(o -> {
    List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());
    o.setOrderItems(orderItems);
});

private List<OrderQueryDto> findOrders() {
    return em.createQuery(
        "select new jpabook.jpashop.repository.order.query.OrderQueryDto(" +
        "o.id, m.name, o.orderDate, o.status, d.address)" +
        " from Order o" +
        " join o.member m" +
        " join o.delivery d", OrderQueryDto.class)
    .getResultList();
}

private List<OrderItemQueryDto> findOrderItems(Long orderId) {
    return em.createQuery(
        "select new jpabook.jpashop.repository.order.query.OrderItemQueryDto(" +
        "oi.order.id, i.name, oi.orderPrice, oi.count)" +
        " from OrderItem oi" +
        " join oi.item i" +
        " where oi.order.id = :orderId", OrderItemQueryDto.class)
    .setParameter("orderId", orderId)
    .getResultList();
}

```
#### 📌 2. DTO 직접 조회 - 컬렉션 일괄 조회 및 매핑
- 	쿼리 수 최소화 (N+1 → 2회)
- 페이징 불가하지만 성능과 단순성은 우수
```java
List<OrderQueryDto> result = findOrders();

Map<Long, List<OrderItemQueryDto>> orderItemMap =
    findOrderItemMap(toOrderIds(result));

result.forEach(o -> o.setOrderItems(orderItemMap.get(o.getOrderId())));

private List<Long> toOrderIds(List<OrderQueryDto> result) {
    return result.stream()
        .map(OrderQueryDto::getOrderId)
        .collect(Collectors.toList());
}

private Map<Long, List<OrderItemQueryDto>> findOrderItemMap(List<Long> orderIds) {
    List<OrderItemQueryDto> orderItems = em.createQuery(
        "select new jpabook.jpashop.repository.order.query.OrderItemQueryDto(" +
        "oi.order.id, i.name, oi.orderPrice, oi.count)" +
        " from OrderItem oi" +
        " join oi.item i" +
        " where oi.order.id in :orderIds", OrderItemQueryDto.class)
    .setParameter("orderIds", orderIds)
    .getResultList();

    return orderItems.stream()
        .collect(Collectors.groupingBy(OrderItemQueryDto::getOrderId));
}
```

#### 📌 3. Flat DTO → 메모리 그룹핑 방식
- 중복 row로 인해 메모리 비용 증가
-  페이징 불가능 (모든 조인 결과를 메모리에 올린 뒤 재구성)
- 정렬과 필터링이 제한적
```java
List<OrderFlatDto> flats = orderQueryRepository.findAllByDto_flat();

List<OrderQueryDto> result = flats.stream()
    .collect(Collectors.groupingBy(
        o -> new OrderQueryDto(o.getOrderId(), o.getName(), o.getOrderDate(),
                               o.getOrderStatus(), o.getAddress()),
        Collectors.mapping(
            o -> new OrderItemQueryDto(o.getOrderId(), o.getItemName(),
                                       o.getOrderPrice(), o.getCount()),
            Collectors.toList()
        )
    ))
    .entrySet().stream()
    .map(e -> new OrderQueryDto(
        e.getKey().getOrderId(), e.getKey().getName(), e.getKey().getOrderDate(),
        e.getKey().getOrderStatus(), e.getKey().getAddress(), e.getValue()))
    .collect(Collectors.toList());

public List<OrderFlatDto> findAllByDto_flat() {
    return em.createQuery(
        "select new jpabook.jpashop.repository.order.query.OrderFlatDto(" +
        "o.id, m.name, o.orderDate, o.status, d.address, " +
        "i.name, oi.orderPrice, oi.count)" +
        " from Order o" +
        " join o.member m" +
        " join o.delivery d" +
        " join o.orderItems oi" +
        " join oi.item i", OrderFlatDto.class)
    .getResultList();
}
```
---


### 🧾 마무리
- JPA는 1:N 페치 조인 시 페이징이 불가하므로, 상황에 맞는 전략을 선택하는 것이 중요
- 실무에서는 다음의 우선순위를 따라야 함:
```txt
엔티티 조회 (Fetch Join XToOne) → 
@BatchSize 컬렉션 조회 → 
DTO 분할 조회 → 
Flat DTO → 
Native SQL / JdbcTemplate
```

---




# 📘 인프런 - 실전! 스프링 부트와 JPA 활용2 -  API 개발과 성능 최적화 (김영한)

## 📅 2025-06-29 - API개발 고급 - 실무 필수 최적화

### 💡 학습 주제


- OSIV(Open Session In View) 개념 이해
- 실무 환경에서의 운영 전략 및 설정 방법
  
---

### 🧠 주요 개념 요약


| 항목 | 설명 |
|------|------|
| **Open Session In View (OSIV)** | Hibernate의 OSIV와 JPA의 Open EntityManager In View는 사실상 동일한 개념으로, 트랜잭션 범위 외에도 영속성 컨텍스트(EntityManager)를 유지해 뷰 렌더링 시 LAZY 로딩을 허용함 |
| **`spring.jpa.open-in-view` 옵션** | `true`(기본값): request ~ response 전체 사이클 동안 DB 커넥션과 영속성 컨텍스트 유지<br>`false`: 트랜잭션 종료 시점(@Transactional 종료)과 함께 DB 커넥션 반납 |
| **기본 경고 로그** | `open-in-view` 옵션을 설정하지 않으면 스프링 부트 실행 시 기본값 `true`로 동작하며 다음과 같은 경고 로그 출력됨:<br> `WARN ... spring.jpa.open-in-view is enabled by default.` |
| **Fetch Join + distinct의 부작용** | 컬렉션 fetch join 시 중복 row가 발생하여 페이징이 불가능해짐. 이는 OSIV와는 별개 이슈지만 자주 연계되므로 주의 |
| **Service 분리 전략 (Command-Query Responsibility)** | 핵심 도메인 로직은 `XxxService`, 화면 조회/쿼리 최적화는 `XxxQueryService` 등으로 분리하여 책임을 명확히 함 (CQRS-like 분리)



---



### 🧪 실습 코드
#### 📄 `application.yml` 설정

```yaml
spring:
  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
        format_sql: true
        default_batch_fetch_size: 100
    open-in-view: false
```
✔️ open-in-view: false 설정 시, 서비스 계층 내부에서 필요한 모든 데이터를 미리 조회(fetch)해야 함
✔️ 이후 컨트롤러/뷰 렌더링 시에는 더 이상 LAZY 로딩이 동작하지 않음

### ✅ 운영 전략 가이드
| **시스템 유형** | **OSIV 권장 설정** | **이유** |
|:-:|:-:|:-:|
| **B2C (고객-facing)** | false | 커넥션 자원 낭비 방지, 성능 이슈 방지 |
| **Admin/내부 백오피스** | true 또는 선택적 | 데이터량 적고 빠른 개발이 필요할 경우 허용 |


### 🔧 실무 팁
- open-in-view: false 설정 시 모든 쿼리는 서비스 계층 내부에서 명시적으로 처리되어야 함
- 컨트롤러에서 LAZY 로딩을 발생시키면 LazyInitializationException 예외 발생 가능
- 반드시 DTO 변환을 트랜잭션 내부에서 완료해야 함

---