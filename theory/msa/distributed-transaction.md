- [[#1. 2PC|1. 2PC]]
- [[#2. TCC|2. TCC]]
- [[#3. SAGA|3. SAGA]]
	- [[#3. SAGA#3-1. Saga - Orchestration|3-1. Saga - Orchestration]]
	- [[#3. SAGA#3-2. Saga - Choreography|3-2. Saga - Choreography]]

## 1. 2PC
- Two-Phase Commit Protocol의 약자로 분산시스템에서 트랜잭션의 원자성을 보장하기 위해 사용하는 프로토콜
- 트랜잭션을 두단계로 나누어 처리함
	- Prepare 단계 : 트랜잭션 매니저가 참여자에게 작업 준비가 가능하는지 묻는다.
	- Commit 단계 : Prepare 단계에서 모든 참여자가 작업이 가능하다고 응답하면 실제로 커밋을 수행한다.
- 대표적인 구현으로는 XA 트랜잭션이 존재
- 1번
```sql
create database 2pc1;
       
create table product (
    id INT PRIMARY KEY,
    quantity INT);

insert into product values(1,1000);

xa start 'product_1';
update product set quantity=900 where id =1;
xa end 'product_1';
   
xa prepare 'product_1';
xa commit 'product_1'; 
```
- 2번
```sql
create database 2pc2;
       
create table point (
    id INT PRIMARY KEY,
    amount INT);


xa start 'point_1';
insert into point values(1,1000);
xa end 'point_1'
```
- 3번
```sql
update product set quantity=900 where id =1; --락 걸림 xa commit 'product_1'; 이후 수행됨
```

- 2PC의 장단점
	- 장점
		- 강력한 정합성을 보장
		- 사용하는 데이터베이스가 XA를 지원한다면 구현 난이도가 낮음
	- 단점
		- 사용하는 데이터베이스가 XA를 지원하지 않는다면 구현하기 어렵습니다.
		- Prepare 단계 이후 Commit이 완료될때까지 lock을 유지하기 때문에 가용성이 낮아집니다.
		- 장애복구시 수동으로 개입하여 해결해야 합니다.
		- 실용성이 낮습니다.
	- 실무에서는?
		- 2PC보다 다른 방법을 사용하여 분산트랜잭션을 구현
## 2. TCC
- TCC(Try-Confirm-Cancel)는 분산 시스템에서 데이터 정합성을 보장하기 위해 사용하는 분산 트랜잭션 처리방식
- 세단계로 나누어 트랜잭션을 관리
	- Try : 필요한 리소스를 점유할 수 있는지 검사하고 임시로 예약합니다.
	- Confirm : 실제 리소스를 점유할 수 있는지를 검사하고 임시로 예약합니다.
	- Cancel : 문제가 생긴 경우, 예약 상태를 취소하여 원복합니다.
- Try, Confirm, Cancel 단계는 멱등하게 설계가 되어야 합니다.
- TCC의 장단점
	- 장점
		- 확장성과 성능에 유리
			- 2PC에 비해 데이터베이스의 Lock 점유시간이 짧음
			- 2PC에 비해 Long Transaction에 덜 취약
		- 장애 복구와 재시도 처리에 유연
			- 비즈니스 정책에 따라 전략을 정할 수 있음
	- 단점
		- 기존 시스템에 비해 설계와 구현이 복잡하다.
			- 모든 단계 (Try, Confirm, Cancel)는 멱등적으로 설계되어야 합니다.
			- 네트워크 오류, 재시도 시나리오를 고려한 복잡한 로직 구현이 필요합니다.
- Pending 상태 대처
	- Confirm 단계 실패
		- 특정시간이 지난 경우 Admin에서 확인
	- Cancel 단계 실패
		- Order의 상태에 맞춰 자동으로 취소 처리
## 3. SAGA
### 3-1. Saga - Orchestration
- Coodinator(또는 Orchestrator)가 각 참여 서비스들을 순차적으로 호출하며 전체 트랜잭션의 흐름을 제어
- 장점
	- 구현 난이도와 유지보수 난이도가 낮음
- 단점
	- 시간이 지날수록 Coorniator(Ochestrator)가 복잡해 질 수 있음
	- 서비스간 결합도가 증가함

### 3-2. Saga - Choreography
- Coordinator 없이 각 서비스가 이벤트를 발행, 구독하며 이를 통해 전체 트랜잭션의 흐름을 제어하는 방식
- 장점
	- Event 기반으로 동작을 하다보니 서비스간 결합도가 낮음
- 단점
	- 구현난이도 상승
	- 흐름 파악이 어려움
		- Coordinator가 없다보니 한눈에 파악하기 어려움
