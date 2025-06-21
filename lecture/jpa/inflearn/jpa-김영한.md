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

