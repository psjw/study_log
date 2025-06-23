#  🧠 JPA  - 사용이유

### ✅ 핵심 개념 요약
- SQL 결과에 따라 객체의 존재 여부가 결정되므로, 객체를 신뢰하기 어려운 구조에서 벗어나기 어렵다 (SQL 중심 개발 방식)
- 객체지향과 관계형 DB 간의 패러다임 불일치 (상속, 연관관계, 객체그래프 탐색 등)
- 1차캐시, 쓰기지연, 지연로딩 등으로 성능최적화
- 유지보수성 증가


### 🔎 상세 개념 정리
| 개념 항목       | 설명                                                                |
|----------------|---------------------------------------------------------------------|
| 객체 신뢰 문제   | 객체를 신뢰하지 못하고 항상 SQL로 검증해야 하는 구조 (ex. join, null 확인 등)                       |
| 패러다임 불일치 | 객체의 상속관계, 연관관계, 객체그래프 탐색 등이 관계형 DB와 맞지 않아 JPA가 이를  보장  |
| 성능최적화   | 1차 캐시를 통한 중복 조회 제거, 쓰기 지연 저장소로 커밋 시점에 일괄 SQL 전송, 지연 로딩을 통해 필요한 시점에 DB 접근  |
| JPA의 장점      | 생산성, 유지보수, 패러다임 일치, 성능 최적화, 데이터 접근 추상화, 벤더 독립성, 표준 API 사용 |

### 💻 실습 예시 코드

```java
//유지보수
Member member = new Member("A");
jpa.persist(member);//저장
Member member = jpa.find(Member.class, memberId); //조회
member.setName("이름변경"); //수정
jpa.remove(member); //삭제

/*
    상속 Album -> Item
   JPA가 처리
   Insert into item ...
   Insert into Album ...
 */
jpa.persist(album); 

//연관관계 저장
Team  team = new Team("Ateam");
Member member = new Member("A");
member.setTeam(team);
jpa.persist(member);

//객체 그래프 탐색 & 신뢰할수 있는 엔티티
Member member = jpa.find(Member.class, memberId); //1차 캐시 or DB에서 조회
Team team = member.getTeam(); // 지연 로딩 시, 실제 조회 발생. 항상 값을 신뢰할 수 있음

//동등성 & 1차캐시 
Long memberId = 11;
Member member1 = jpa.find(Member.class, memberId); //SQL 조회
Member member2 = jpa.find(Member.class, memberId); //1차캐시 조회
member1 == member2; //true 같음 

//쓰기 지연 - commit 시점에 insert/update 쿼리 실행됨
transaction.commit();

```



### 🔁 회고
- 기존 MyBatis 기반에서는 참조 객체에 대한 조회와 필드를 모두 SQL을 확인이 필요하지만 JPA는 모든 값이 존재함을 보장
- 다만 JPA는 SQL을 할 수 있으면 거의 대부분 활용 가능한 MyBatis보다는 진입장벽이 조금 존재
- 연관관계를 표현함에 있어 객체와 DB가 거의 동일한 관계가 성립함
