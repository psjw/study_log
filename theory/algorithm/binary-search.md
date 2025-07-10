#  🧠 알고리즘  - 이진탐색 (Binary Search)

## ✅ 핵심 개념 요약

- 이진 탐색은 **정렬된 배열**을 대상으로 원하는 값을 **O(log N)** 시간에 탐색하는 알고리즘
- 중간 값을 기준으로 좌우를 나누어 탐색 범위를 반씩 줄여 나감
- 정렬된 데이터만 사용 가능하다는 전제가 반드시 필요

---

## 🔎 상세 개념 정리

| 항목 | 설명 |
|------|------|
| **이진 탐색 조건** | 데이터가 반드시 정렬되어 있어야 함 |
| **탐색 방향** | 중간값과 target을 비교하여 왼쪽 또는 오른쪽으로 탐색 범위 이동 |
| **시간 복잡도** | O(log N) |
| **활용 예시** | 특정 값 찾기, 최적화 문제에서 탐색 범위 줄이기 |

---

## 💻 실습 예시 코드 및 시각화

### 📌 1.  샘플 코드

```java
int binarySearch(int[] arr, int target) {
    int start = 0;
    int end = arr.length - 1;

    while (start <= end) {
        int mid = (start + end) / 2;

        if (arr[mid] == target) {
            return mid; // 값의 위치 반환
        } else if (arr[mid] < target) {
            start = mid + 1; // 오른쪽 절반으로 이동
        } else {
            end = mid - 1; // 왼쪽 절반으로 이동
        }
    }

    return -1; // 찾는 값이 없는 경우
}
```

---
## 🧠 이진 탐색 동작 흐름 요약

1. 배열 정렬
2. start, end 설정
3. mid = ( start + end ) / 2 계산
4. arr[mid] == target 이면 성공
5. 작으면 오르쪽, 크면 왼쪽으로 범위 조정
6.  start > end가 되면 탐색 실패 

---

## 🧪 예시 시각화

```text
target = 10
arr = [2, 4, 6, 8, 10, 12, 14]

1) start = 0, end = 6 → mid = 3 → arr[3] = 8 → 8 < 10 → start = 4
2) start = 4, end = 6 → mid = 5 → arr[5] = 12 → 12 > 10 → end = 4
3) start = 4, end = 4 → mid = 4 → arr[4] = 10 → 찾음!
```

---

## 🔁 회고
- 이진 탐색은 단순 탐색뿐 아니라, 답이 특정 조건을 만족하는 최솟값/최댓값일 때도 활용됨
- 정렬 여부를 반드시 확인하고, 중간값 계산에서 overflow를 피하기 위해 mid = start + (end - start) / 2 도 자주 사용함

---
