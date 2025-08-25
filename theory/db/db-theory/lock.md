---
aliases:
  - "\bLock"
---
- [[#1. Optimistic Lock vs Pessimistic Lock|1. Optimistic Lock vs Pessimistic Lock]]
- [[#2. DeadLock|2. DeadLock]]

---

## 1. Optimistic Lock vs Pessimistic Lock
### 1-1. 낙관적락(Optimistic Lock)
-  충돌이 드물다고 가정
- 보통 버전 번호 또는 업데이트 시점의 값 비교로 동시성 제어
- 충돌 발생 시 트랜잭션을 롤백하고 재시도
- 장점: 락을 실제로 걸지 않으므로 동시성 ↑
- 단점: 충돌 발생 시 재시도 비용
```SQL
    UPDATE coupon
    SET stock = stock - 1,
        version = version + 1
    WHERE id = #{id}
      AND version = #{version}
      AND stock > 0
```
### 1-2. 비관적락(Pessimistic Lock)
- 충돌이 자주 발생한다고 가정
- 데이터 조회 시점에 행에 Lock을 걸어 다른 트랜잭션이 접근하지 못하게 함
- 장점: 충돌 확실히 방지
- 단점: 동시성 ↓, Deadlock 발생 가능
```SQL
SELECT * 
FROM user_event_wallet
WHERE user_id = 1 AND event_id = 100
FOR UPDATE NOWAIT; -- NOWAIT를 통해 기다리지않고 바로 팅김
```

---

## 2. DeadLock
### 2-1. 원인
- 두 개 이상의 트랜잭션이 서로가 가진 자원을 기다리며 무한 대기하는 상황
    - 예:
        - T1이 A → B 순서로 Lock
        - T2가 B → A 순서로 Lock
        - 서로 상대방이 가진 자원을 기다리며 무한 대기
### 2-2. 해결방법
- 트랜잭션 순서 통일
- Lock Timeout 설정