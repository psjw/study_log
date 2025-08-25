- [[#1. 파티셔닝|1. 파티셔닝]]
- [[#2. Vertical Partitioning vs Horizontal Partitioning|2. Vertical Partitioning vs Horizontal Partitioning]]
- [[#3. 파티션 종류|3. 파티션 종류]]


## 1. 파티셔닝
- 대용량 데이터를 여러 파티션으로 분할하여 성능과 관리 효율성을 높이는 기법  
- 메모리에 인덱스를 다 담기 어려운 경우나 특정 쿼리 성능을 높이기 위해 사용
## 2. Vertical Partitioning vs Horizontal Partitioning
- Vertical Partitioning : 컬럼 단위 분할 (예: 자주 쓰는 컬럼과 잘 안 쓰는 컬럼 분리)  
- Horizontal Partitioning : 로우 단위 분할 (예: 날짜, 지역별로 레코드 나눔)

## 3. 파티션 종류
- 리스트 파티셔닝 : 특정 값 집합으로 구분 (예: 지역코드별)  
- 해시 파티셔닝 : 해시 함수(mod 연산) 기반으로 분산 (균등 분산 목적)  
- 레인지 파티셔닝 : 범위 조건으로 분할 (예: 날짜 단위, 월별 로그 테이블)  
- 키 파티셔닝 : 내부적으로 해시, 주로 기본키나 특정 키 기반 분산