#  📘 01. JPA 소개 및 ORM 이해

## 🎯 학습 목표

- 기존 SQL 중심 개발의 한계를 이해하고 ORM이 왜 필요한지 설명할 수 있음
- 객체와 관계형 데이터베이스 간의 패러다임 불일치를 인식하고 해결 방법을 이해함
- JPA의 성능 최적화 기능(1차 캐시, 쓰기 지연, 지연 로딩 등)을 설명하고 실습


## 🧩 배경 및 도입

- 기존 JDBC, MyBatis 같은 SQL 중심 개발은 객체를 신뢰하지 못하고 항상 SQL을 통해 검증하는 구조를 낳음
- 객체지향 언어(Java)와 관계형 데이터베이스(RDB) 사이에는 패러다임 불일치 문제가 존재
- 이러한 문제를 해결하기 위해 ORM(Object Relational Mapping)이 등장했으며, Java 진영의 표준 ORM이 JPA

## 🧠 핵심 개념 요약

| 항목 | 설명 |
|------|------|
| 객체 신뢰 문제 | SQL 결과에 따라 객체 존재 여부가 결정되어 항상 SQL로 검증해야 하는 구조 |
| 패러다임 불일치 | 객체의 상속, 연관관계, 객체 그래프 탐색 등을 관계형 DB에서 직접 표현하기 어려움 |
| 성능 최적화 기능 | 1차 캐시, 쓰기 지연, 지연 로딩 등 성능 개선을 위한 다양한 기능 제공 |
| 유지보수 향상 | 도메인 중심 설계가 가능하고, 변경 시 재사용 및 유지보수가 용이 |
| 벤더 독립성 | 특정 DB에 종속되지 않도록 추상화된 API 제공 (Hibernate, EclipseLink 등 구현체 사용 가능) |

## ⚙️ 주요 기능 및 작동 방식

- **영속성 컨텍스트**: 엔티티를 관리하는 1차 캐시. 동일성(==)을 보장.
- **변경 감지 (Dirty Checking)**: setter만으로 SQL UPDATE 자동 감지.
- **지연 로딩 (Lazy Loading)**: 연관 객체는 접근하는 시점에 쿼리 실행.
- **쓰기 지연 (Write-Behind)**: persist 호출 시 바로 INSERT하지 않고, 트랜잭션 커밋 시 일괄 실행.



## 💻 실습 코드 예시

```java
// ✅ 기본 CRUD
Member member = new Member("A");
jpa.persist(member);                          // INSERT 지연됨 (쓰기 지연)

Member findMember = jpa.find(Member.class, member.getId()); // 1차 캐시 or DB 조회
findMember.setName("변경");                   // Dirty Checking에 의해 UPDATE 감지
jpa.remove(findMember);                       // DELETE 실행 예약

// ✅ 상속 매핑 예시
Album album = new Album();
album.setName("음반");
jpa.persist(album); // INSERT into Item → Album (전략에 따라)

// ✅ 연관관계 매핑
Team team = new Team("A팀");
Member member = new Member("A");
member.setTeam(team);
jpa.persist(member);

// ✅ 지연 로딩 및 그래프 탐색
Member member = jpa.find(Member.class, memberId); // 캐시 or DB
Team team = member.getTeam(); // 지연 로딩 → SELECT 실행

// ✅ 동일성 보장
Member m1 = jpa.find(Member.class, memberId);     // DB 접근
Member m2 = jpa.find(Member.class, memberId);     // 캐시 조회
System.out.println(m1 == m2);                     // true

// ✅ 쓰기 지연 예시
transaction.commit();  // INSERT/UPDATE 쿼리 일괄 전송

```

## 🚨 실무 고려사항
- **무한 참조**: @ToString.Exclude, @JsonIgnore 등으로 해결
- **N+1 문제**: fetch join 또는 @BatchSize 전략으로 대응
- **엔티티 직접 반환 금지**: DTO로 변환하여 컨트롤러에 전달



## 🔁 회고
- 기존 MyBatis 기반에서는 참조 객체에 대한 조회와 필드를 모두 SQL을 확인이 필요하지만 JPA는 모든 값이 존재함을 보장
- 다만 JPA는 SQL을 할 수 있으면 거의 대부분 활용 가능한 MyBatis보다는 진입장벽이 조금 존재
- 연관관계를 표현함에 있어 객체와 DB가 거의 동일한 관계가 성립함



## 🆚 JPA vs MyBatis 비교 요약
| 항목 | JPA | MyBatis |
|---|---|---|
| 개발 방식 | 객체 중심 | SQL 중심 |
| 쿼리 작성 | JPQL 또는 생략 가능 | SQL 직접 작성 필요 |
| 연관관계 처리 | 객체 참조 기반 | JOIN SQL 직접 작성 |
| 성능 최적화 | 1차 캐시, 지연 로딩, 벌크 연산 | 수동 SQL 튜닝 필요 |
| 생산성 | 높음 (자동 처리) | 복잡 로직에 더 유리 |
| 학습 난이도 | 비교적 높음 | 비교적 낮음 |
| 유지보수성 | 높은 수준의 도메인 재사용 가능 | SQL 변경 시 DAO 전체 영향 |

## 📚 참고 자료
-  인프런 - 김영한 JPA강의
-  책 : 자바 ORM 표준 JPA 프로그래밍
  
---