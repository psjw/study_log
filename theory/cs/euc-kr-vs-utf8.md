#  🧠 EUC-KR VS UTF-8

### ✅ 핵심 개념 요약
- 한글 인코딩 방식에는 EUC-KR, CP949, UTF-8 등이 있음
- 인코딩 방식은 완성형(EUC-KR, CP949)과 조합형(UTF-8)으로 나뉨
- 유니코드(UTF-8)는 모든 언어를 표현 가능하도록 설계되어 국제화에 적합

### 🔎 상세 개념 정리
| 항목                  | 설명                                                                 |
|-----------------------|----------------------------------------------------------------------|
| 완성형 vs 조합형     | 완성형: 글자마다 고유 코드값 부여 / 조합형: 초성, 중성, 종성을 조합하여 문자 구성             |
| EUC-KR            | 완성형 ,2,350자 표현 가능, KS X 1001 기준, 2바이트 고정                                |
| CP949             | 완성형, EUC-KR 확장형, 11,000자 표현 가능, 2바이트 고정                                  |
| UTF-8             | 조합형 ,유니코드 인코딩 방식, 한글 1자 = 3바이트, 글로벌 환경에 적합 (가변 바이트: 1~4바이트) |

### 🔁 회고
- 과거 메시징 솔루션에서 EUC-KR이 기본 설정되어 있었음
- 이로 인해 ‘뷁’과 같은 글자 전송  시 ‘�’, '?' 과 같은 깨짐 현상 발생
- 특히 EUC-KR과 UTF-8을 혼용했을 때 시스템 간 호환 문제 빈번