# 🧪 JVM GC 실험 정리

## 1. 실험 환경

- **JDK 버전**: 17
- **실행 옵션**: `-Xmx128m -Xms128m -XX:+UseG1GC -XX:+PrintGCDetails`
- **컴파일 명령**: `javac -d src src/com/psjw/gc/GCPractice.java`
- **실행 명령**:  
```bash
  java -Xmx128m -Xms128m -XX:+UseG1GC -XX:+PrintGCDetails -Xlog:gc*:file=gc.log -cp src com.psjw.gc.GCPractice
```
 


## 2. 실험 코드

```java
List<byte[]> list = new ArrayList<>();  
for (int i = 0; i < 1000; i++) {  
    list.add(new byte[1024 * 1024]); // 1MB 객체 할당  
    if (i % 10 == 0) Thread.sleep(100);  
}
```
> G1GC에서 Humongous 객체가 1MB heap에서 얼마나 빨리 누적되고 어떻게 GC가 반응하는지 관찰


## 3. 실험 목표
- Eden → Survivor → Old로의 객체 이동 관찰
- Humongous 객체가 어떻게 할당되고 GC가 어떻게 반응하는지 확인
- Full GC 및 Compaction 동작 유도 및 확인

## 4. 로그 기반 주요 분석 포인트
### 4-1. GC 로그 상세 라인별 해석
```text
[0.009s][info   ][gc,heap     ] Heap Region Size: 1M
```
- **시점**: 프로그램 시작 0.009초 후
- **의미**: G1GC는 heap을 동일 크기의 region으로 나누며, 각 region의 크기는 1MB로 설정됨
	- G1GC는 메모리를 2048로 나눈 것이 region size가 됨
	- 128M/2048 = 64KB로 최소 region인 1MB로 상승
- **실무 포인트**: 작은 heap(128MB)에선 1MB region이 기본, 더 큰 heap에선 자동으로 커질 수 있음

```text
[0.351s][info   ][gc,start    ] GC(0) Pause Young (Concurrent Start) (G1 Humongous Allocation)
```
- **GC 번호**: GC(0) → 첫 번째 GC 사이클
- **GC 타입**: Young GC, Humongous 객체로 인한 발생
- **의미**: 큰 객체(> region의 50%)가 Eden에 할당되어 Humongous region에 저장됨 → GC 유도
- **실무 포인트**: 큰 배열, 이미지 데이터 등을 자주 다룰 땐 Humongous allocation 발생률 주의

```text
[0.352s][info   ][gc,heap     ] GC(0) Eden regions: 1->0(28)
```
- **해석**: GC 전 Eden에 region 1개 → GC 후 0개, 최대 28개까지 사용 가능
- **실무 포인트**: (28)은 해당 시점의 Eden 영역 용량 한계 = 28MB
- **추가 정보**: 객체가 Survivor나 Old로 이동했음을 의미

```text
[0.352s][info   ][gc,heap     ] GC(0) Survivor regions: 0->1(3)
```
- **해석**: GC 전 Suvivor에 region 1개 → GC 후 1개, 최대 3개까지 사용 가능
- **실무 포인트**: (3)은 해당 시점의 Survivor 영역 용량 한계 = 3MB
- **추가 정보**: GC를 통해 Eden영역에서 Survivor영역으로 이동했음을 의미

```text
[0.352s][info   ][gc,heap     ] GC(0) Old regions: 2->2
```
- **해석**: Old 영역이 GC 후에도 줄지 않음 = 대부분 아직 reachable
- **실무 포인트**: Old 객체가 살아있으면 Full GC나 Compaction 대상 가능성 높음

```text
[0.352s][info   ][gc,heap     ] GC(0) Humongous regions: 56->56
```
- **해석**: Humongous 영역이 GC 후에도 줄지 않음 = 대부분 아직 reachable
- **실무 포인트**: Humongous 객체가 살아있으면 Full GC나 Compaction 대상 가능성 높음

```text
[0.779s][info   ][gc          ] GC(2) Pause Young (Prepare Mixed) (G1 Humongous Allocation) (Evacuation Failure: Allocation) 125M->125M(128M)
```
- **이벤트**: GC(2)는 Evacuation 실패 → 공간 부족(Eden에서 Survivor 또는 Old로 객체 이동이 실패함)
- **현상**: GC 후에도 heap 크기 변화 없음
- **실무 포인트**: G1GC의 Eden→Survivor→Old 이동이 실패하여 GC가 **Full GC**로 넘어가게 됨

```text
[0.783s][info   ][gc          ] GC(3) Pause Full (G1 Compaction Pause) 125M->125M(128M)
```
- **GC 타입**: Full GC 발생, 압축(compaction) 수행
- **효과**: 메모리는 줄지 않았으나 파편화 해소 시도됨(**단편화 해소**와 **빈 공간 확보**가 목적이지만, 많은 객체가 살아있어서 효과는 적음)
- **실무 포인트**: Full GC가 자주 일어나면 GC 튜닝/heap 증설 고려 필요

```text
[0.802s][info   ][gc          ] GC(11) Pause Full (G1 Compaction Pause) 127M->1M(128M)
```
- **결과**: GC(11)에서 대부분의 객체가 정리되어 heap 사용량 급감
- **의미**: Humongous 객체가 모두 unreachable 되어 제거
- **실무 포인트**: 급격한 GC 효율 상승은 불필요한 객체 대량 제거가 성공했음을 의미

```text
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
```
- **의미**: - 객체를 너무 많이 만들어서 GC가 따라가지 못함 → JVM이 강제 종료됨
- **실무 포인트**: Humongous 객체가 많거나 Survivor/Old에 압력 높을 경우 자주 발생함

## 4. 로그 기반 주요 분석 포인트
| **GC ID** | **종류**           | **주요 이벤트**                        |
| --------- | ---------------- | --------------------------------- |
| GC(0)     | Young GC         | Humongous 객체 → Eden → Survivor 이동 |
| GC(1)     | Concurrent Start | 루트 마킹 및 Preclean 시작               |
| GC(2)     | Young GC         | Evacuation 실패, 공간 부족              |
| GC(3)     | Full GC          | Compaction 수행 (효과 미미)             |
| GC(4~10)  | Full GC 반복       | 메모리 부족 지속. Humongous 객체 여전히 살아있음  |
| GC(11)    | Full GC          | 대부분 객체 회수 성공 → Heap 사용량 급감        |
| 종료        | OOM 발생           | 메모리 한계 도달 → 프로그램 강제 종료            |

## 5. 실험 결과 요약
- **Humongous Allocation**이 반복되며 힙이 빠르게 포화됨
- **Evacuation Failure**가 발생하여 G1GC가 Full GC를 유도함
- **Full GC** (Compaction)을 여러 차례 수행했으나 일부 객체가 살아 있어 해제되지 않음
- **GC**(11)에서 대부분의 Humongous 객체가 제거되어 메모리 압박이 일시적으로 해소됨
- 그러나 이후 객체 생성이 계속되며 **힙 한계를 초과**, 최종적으로 OOM 발생

## 6. 배운 점 정리
### 6-1. GC 동작 원리 체감
- Region 기반 구조, Eden → Survivor → Old → Humongous 흐름 확인
- Humongous 객체가 많으면 G1GC도 **감당 불가**
- **Evacuation Failure**는 위험 신호 → Full GC 유도

### 6-2. 실무 적용 학습
| **상황**     | **대응 전략**                                                              |
| ---------- | ---------------------------------------------------------------------- |
| 큰 객체 할당 많음 | 객체 크기 줄이거나 Stream으로 처리 (byte[], 이미지 등)                                 |
| Full GC 잦음 | Heap 용량 증가 or GC 튜닝 (MaxGCPauseMillis, InitiatingHeapOccupancyPercent) |
| 병목 발생      | GC 로그 + Heap Dump 병행 분석으로 원인 진단                                        |

## 7. 회고
- G1GC는 힙을 Region으로 쪼개어 효율적으로 관리하지만, **큰 객체**(Humongous)가 쌓이면 빠르게 압박이 온다는 점을 실감
- GC 로그 해석을 통해 어떤 시점에 어떤 문제가 생기는지 **정량적으로 확인 가능**
- Full GC는 시스템 전체 성능에 큰 영향을 줄 수 있으며, GC 로그를 **미리 분석하고 대응하는 습관이 중요함**
- GC 로그, Heap Dump, VisualVM, GCViewer 등을 **실무에서 병행 활용하는 능력**이 핵심이다