#  🧠 알고리즘  - 슬라이딩 윈도우

## ✅ 핵심 개념 요약


- 슬라이딩 윈도우는 **배열이나 문자열에서 연속된 구간(윈도우)** 을 유지하며 이동하는 기법
- 주로 **고정된 크기 또는 조건을 만족하는 연속 부분**을 효율적으로 탐색할 때 사용
- 시간 복잡도는 O(N)으로, **모든 요소를 한 번씩만 방문**
  
## 🔎 상세 개념 정리

| 항목 | 설명 |
|------|------|
| **핵심 포인터** | 일반적으로 start, end  사용 |
| **시간 복잡도** | O(N) - 모든 원소를 한 번씩만 처리 |
| **적용 유형** | 합, 평균, 최대/최소, 중복 여부, 정렬 여부 등 |
| **윈도우 형태** | 고정 길이 또는 조건 기반 가변 길이 |
| **투 포인터와 차이점** | 슬라이딩 윈도우는 **"연속된 구간 유지"**, 투 포인터는 **"조건 만족 범위 탐색"** 이 목적 |

---

## 💻 실습 예시 코드 및 시각화

### 📌 1.  예시: 고정 크기 윈도우로 최대 합 구하기 (크기 K)
```java
int maxSum(int[] arr, int k) {
    int windowSum = 0, maxSum = 0;

    for (int i = 0; i < k; i++) {
        windowSum += arr[i];
    }
    maxSum = windowSum;

    for (int end = k; end < arr.length; end++) {
        windowSum += arr[end] - arr[end - k];
        maxSum = Math.max(maxSum, windowSum);
    }

    return maxSum;
}
```


### 📌 2.  슬라이딩 윈도우 핵심 원칙 요약

| **유형** | **조건** | **동작** |
|:-:|:-:|:-:|
| 고정 윈도우 | 고정된 길이 K | sum += new, sum -= old 반복 |
| 가변 윈도우 | 조건 만족할 때까지 확장 후 조건 불만족 시 축소 | while문으로 반복 제어 |

---

## 🔁 회고
- 슬라이딩 윈도우는 연속된 범위를 유지하면서 계산해야 하는 경우에 매우 유용
- 윈도우를 확장하고 축소하는 시점 제어가 핵심이며, 조건 기반 문제에서는 투 포인터와 같이 사용
- 배열 변경이 잦거나 비연속적 접근이 필요한 경우에는 다른 자료구조(예: 세그먼트 트리, 큐, 덱 등)를 활용하는 것이 더 적합


---
