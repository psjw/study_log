---
aliases:
  - "\bacid-cap"
---
- [[#1. ACID|1. ACID]]
- [[#2. CAP 이론|2. CAP 이론]]

---

## 1. ACID
- Atomic(원자성) :  트랜잭션 단위의 작업은 모두 성공 또는 실패
- Consitency(일관성) : 일관된 데이터를 저장
- Isolation(격리성) : 트랜잭션을 독립적으로 실행
- Durablity(지속성) : 저장된 데이터는 사라져서는 안됨

---

## 2. CAP 이론
- Consistency(일관성) : 모든 노드에서 동일한 데이터가 조회
- Availability(가용성) : 모든 수신 응답이 유효함 (실패시 에러 메시지)
- Partition Tolerance(분할내성) : 네트워크 분할이 발생해도 시스템이 계속 동작

---
