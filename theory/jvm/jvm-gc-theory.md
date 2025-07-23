#  🧠 GC 이론

### ✅ 핵심 개념 요약

- GC(Garbage Collection)는 객체의 생명주기를 관리하여 불필요한 객체를 회수하고 메모리를 회복하는 JVM의 핵심 기능
- GC 발생 시 **Stop-the-World**(STW)가 동반되어 모든 스레드가 일시 정지
- GC는 **Minor GC, Major GC, Full GC**로 구분
- GC가 접근하는 JVM 메모리 영역은 **Young, Old, Metaspace**로 나뉨
- Young 영역은 **Eden, Survivor 0 (S0), Survivor 1 (S1)** 세 영역으로 구성
- GC는 **Mark → Sweep → Compact** 과정으로 동작
- Minor GC는 일반적으로 수십 ms, Full GC는 수 초 이상 걸릴 수 있으며, 이로 인해 시스템 지연 또는 애플리케이션 장애(DB 타임아웃 등)가 발생할 수 있음


### 🔎 상세 개념 정리
| **항목** | **설명** |
|:-:|:-:|
| **Minor GC** | Young(Eden) 영역에서 객체를 Survivor 영역(S0/S1)으로 복사하며 age 증가. Swap과 Copy 후 Compact 발생 |
| **Major GC** | Old Generation 영역 대상. Mark → Sweep → Compact 수행. Young 영역에서 Promotion된 객체 회수 |
| **Full GC** | Heap 전체(Young + Old)와 Metaspace까지 모두 포함한 GC. 비용이 매우 큼 |
| **Stop-the-world** | GC 시 JVM의 모든 스레드를 일시 정지시킴. GC 시간 동안 애플리케이션이 멈춤 |
| **System.gc()** | 명시적으로 Full GC를 요청하지만, JVM이 반드시 실행하지는 않음 |
| **Young Generation** | Eden에 생성된 객체가 S0/S1로 이동하며 age 증가. 일정 age 이상이 되면 Old 영역으로 승격 |
| **Old Generation** | age가 일정 이상인 객체가 저장됨. Major/Full GC 대상 |
| **Mark** | GC Root에서 도달 가능한 객체를 탐색하고 표시함 |
| **Sweep** | Mark되지 않은 객체를 Heap에서 제거하여 메모리 회수 |
| **Compact** | 남아 있는 객체를 연속된 블록으로 재배치해 단편화를 해소하고 성능 향상 |
| **GC Root 조건** | 지역 변수, static 필드, JNI 참조, JVM 내부 시스템 객체 등에서 출발하는 참조 |
| **Metaspace** | 클래스 메타 정보 저장 영역. PermGen을 대체하며 Native 메모리 사용 |


### 🔁 GC 프로세스 단계 (Mark → Sweep → Compact)

#### 1. Mark
- GC Root에서 도달 가능한 객체를 탐색
- 참조 체인을 따라가며 유효한 객체에 표시

#### 2. Sweep
- Mark되지 않은 객체를 메모리에서 제거
- 이 단계는 실제로 객체를 회수하고 가용 공간을 확보

#### 3. Compact
- 남아 있는 객체를 한쪽으로 몰아 연속된 메모리 블록을 형성
- 단편화를 줄이고 새로운 객체의 할당 효율을 높임

### 🔁 회고

- GC의 동작 흐름(Mark → Sweep → Compact)을 이론적으로 학습하며, JVM 메모리 구조와 객체 생존 주기의 관계를 체계적으로 이해할 수 있었음  
- Stop-the-World로 인해 전체 애플리케이션 스레드가 일시 정지된다는 개념이 성능 병목의 핵심이라는 점을 이론을 통해 인식함  
- GC의 목적은 단순한 메모리 정리가 아니라, 성능 유지와 직접적으로 연결된 요소라는 사실을 배우며, GC 튜닝의 중요성을 개념적으로 파악하게 되었음  
- 추후 실습을 통해 G1GC, ZGC 등의 알고리즘을 비교하고, GC 로그 및 도구 분석을 통해 실전 대응력을 높일 계획