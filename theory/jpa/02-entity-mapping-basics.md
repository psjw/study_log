#  📘 02. 엔티티 매핑 기본

## 🎯 학습 목표

- 엔티티 매핑 관련 주요 어노테이션을 이해
- 다양한 기본키 생성 전략과 그 작동 방식을 학습
- 실습 코드를 통해 매핑 어노테이션의 사용법을 학습


## 🧩 배경 및 도입

- 객체와 테이블 간 매핑을 명확히 하기 위해 어노테이션을 활용
- 데이터베이스의 기본키 생성 방식에 유연하게 대응할 수 있어야 함
- 기본키의 성능 최적화는 대규모 시스템에서 중요

## 🧠 핵심 개념 요약

| **항목** | **설명** |
|:-:|:-:|
| @Entity | 테이블과 매핑할 클래스에 적용, JPA가 관리하며 필수 어노테이션임 |
| @Table | 매핑할 테이블 명과 스키마 등을 지정 |
| 데이터베이스 스키마 자동 생성 | create, create-drop, update는 운영 환경에서 사용 금지 |
| @Column | 컬럼 속성 지정 (제약조건, 길이, 기본값 등) |
| @Temporal | 날짜 타입 매핑 (LocalDate, LocalDateTime 사용 시 생략 가능) |
| @Enumerated | enum 타입 매핑, EnumType.STRING 사용 권장 |
| @Lob | CLOB 또는 BLOB으로 매핑됨 |
| @Transient | 해당 필드는 매핑/저장/조회 대상에서 제외 |
| @Id | 테이블의 기본키(PK)를 지정 |
| @GeneratedValue | PK 생성 전략 지정 (IDENTITY, SEQUENCE, TABLE, AUTO) |


## ⚙️ 주요 기능 및 작동 방식

### ✅ @Entity 주의사항
- 기본 생성자가 필수 (`public` 또는 `protected`)
- `enum`, `interface`, `final class`, `inner class`는 사용 불가
- 저장할 필드에는 `final` 사용 불가

### ✅ 데이터베이스 스키마 자동 생성 전략
- `create` : 기존 테이블 삭제 후 새로 생성 (테스트 용도)
- `create-drop` : 종료 시 테이블 제거
- `update` : 변경된 부분만 반영 (개발 초기)
- `validate` : 매핑 오류 확인용 (운영 추천)
- `none` : 아무 동작 없음

### ✅ @GeneratedValue 기본키 전략
- `IDENTITY` : DB에 위임, 즉시 insert SQL 실행 (MySQL 등)
- `SEQUENCE` : DB 시퀀스 사용, `allocationSize`로 성능 개선 (Oracle 등)
- `TABLE` : 키 생성용 별도 테이블 사용
- `AUTO` : 데이터베이스 Dialect 기준으로 자동 결정

## 💻 실습 코드 예시

### 📌 1. @Entity  구조
  
```java
@Entity(name = "ORDERS")
public class Order { ... }
```

### 📌 2. @Table  구조

```java
@Table(
    name = "ORDERS",  //테이블명
    schema = "public",  //RDB 스키마 지정
    catalog = "my_catalog" //catalog명 (MySQL 등)
)
```

### 📌 3. @Column  구조

```java
@Column(
    name = "Name", //컬럼명
    insertable = true, // 등록 여부
    updatable = true, //업데이트 여부
    nullable = false, //not null
    length = 10, //컬럼 길이(String 만 가능) 
    columnDefinition = "varchar(100) default 'Empty'", //컬럼정보를 직접 지정
    precision = 19, //소숫점을 포함한 전체 자릿수
    scale = 2 //소숫점 자릿수
)
```

### 📌 4. @Enumerated  구조

```java
@Enumerated(EnumType.STRING)
private OrderStatus status;
```

## 🚨 실무 고려사항
- `EnumType.STRING`을 명시하지 않으면 기본값은 ORDINAL로, enum 순서 변경 시 문제 발생 가능
- 자동 스키마 생성 옵션은 운영 환경에서 절대 사용 금지 (`validate` 또는 `none` 사용 권장)
- SEQUENCE 전략 시 `allocationSize` 조정으로 성능 최적화 가능



## 🔁 회고
- 어노테이션 기반의 명확한 매핑은 유지보수성과 가독성을 높여줌
- 초반 설정 실수는 운영 시 큰 문제가 되므로 반드시 생성 전략과 enum 저장 방식은 명확히 지정


## 📚 참고 자료
-  인프런 - 김영한 JPA강의
-  책 : 자바 ORM 표준 JPA 프로그래밍
  
---