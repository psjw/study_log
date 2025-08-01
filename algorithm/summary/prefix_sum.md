#  🧠 알고리즘  - 구간합

## ✅ 핵심 개념 요약

- 구간합은 **합 배열(Prefix Sum Array)** 을 미리 만들어, 특정 범위의 합을 **O(1)** 시간에 구할 수 있도록 하는 알고리즘
- 데이터가 자주 변경된다면 **세그먼트 트리(또는 펜윅 트리)** 를 사용해야 효율적

---

## 🔎 상세 개념 정리

| 항목 | 설명 |
|------|------|
| **합 배열 S의 정의** | `S[i] = A[0] + A[1] + ... + A[i]` |
| **합 배열 S를 만드는 공식** | `S[i] = S[i-1] + A[i]` (`S[0] = A[0]`) |
| **i~j까지 구간합 공식** | `S[j] - S[i-1]` (`i > 0`인 경우) / `S[j]` (`i == 0`인 경우) |
| **주의할 점** | 인덱스를 0-based로 처리하는지 1-based로 처리하는지 명확히 해야 함 |

---

## 💻 실습 예시 코드 및 시각화

### 📌 1.  예제

```java
int[] A = {15, 13, 10, 7, 3, 12};
int[] S = new int[A.length];
S[0] = A[0];
for (int i = 1; i < A.length; i++) {
    S[i] = S[i - 1] + A[i];
}
```

| **인덱스 (i)** | **A[i]** | **S[i] 계산식** | **S[i] 결과** |
|:-:|:-:|:-:|:-:|
| 0 | 15 | 15 | 15 |
| 1 | 13 | 15 + 13 | 28 |
| 2 | 10 | 28 + 10 | 38 |
| 3 | 7 | 38 + 7 | 45 |
| 4 | 3 | 45 + 3 | 48 |
| 5 | 12 | 48 + 12 | 60 |

➡️ 예시: A[1] ~ A[3] 구간합
S[3] - S[0] = 45 - 15 = 30

---




## 🔁 회고
- 구간합 문제는 핵심 개념만 익히면 쉽게 구현 가능하며, 반복적인 질의에 매우 효율적
- 하지만 배열이 자주 수정되는 경우에는 단순한 구간합 방식이 비효율적이므로, 세그먼트 트리나 펜윅 트리로 전환

---
