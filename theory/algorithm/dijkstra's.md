#  🧠 알고리즘  - 다익스트라 알고리즘 (Dijkstra's algorithm)


## ✅ 핵심 개념 요약


- **다익스트라 알고리즘**은 **가중치가 양수인 그래프**에서 단일 시작 노드로부터 모든 노드까지의 **최단 경로**를 구하는 알고리즘
- 일반적으로 **우선순위 큐(Heap)** 와 함께 사용되며, 음수 간선이 존재하면 사용할 수 없음

---


## 🔎 상세 개념 정리
| 항목 | 설명 |
|------|------|
| **그래프 구현 방식** | 인접 리스트(Adjacency List) |
| **초기 설정** | 출발 노드만 거리 0, 나머지 노드는 무한대 (INF) |
| **우선순위 큐 사용 이유** | 매 반복마다 가장 거리가 짧은 노드를 빠르게 선택하기 위함 |
| **시간 복잡도** | O(E log V) (V: 노드 수, E: 간선 수) |
| **적용 조건** | 모든 간선의 가중치가 **양수**일 때만 가능 |

---



## 💻 실습 예시 코드 및 시각화

## 1.  예시
```java
public static void dijkstra(int start, List<Node>[] graph, int[] dist) {
    PriorityQueue<Node> pq = new PriorityQueue<>();
    boolean[] visited = new boolean[graph.length];

    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[start] = 0;
    pq.offer(new Node(start, 0));

    while (!pq.isEmpty()) {
        Node now = pq.poll();

        if (visited[now.idx]) continue;
        visited[now.idx] = true;

        for (Node next : graph[now.idx]) {
            if (dist[next.idx] > dist[now.idx] + next.cost) {
                dist[next.idx] = dist[now.idx] + next.cost;
                pq.offer(new Node(next.idx, dist[next.idx]));
            }
        }
    }
}

static class Node implements Comparable<Node> {
    int idx, cost;
    Node(int idx, int cost) {
        this.idx = idx;
        this.cost = cost;
    }
    public int compareTo(Node o) {
        return this.cost - o.cost;
    }
}
```


## 📌 2. 그래프 시각화 예시

### 📌 1. 그래프 구조

```text
1 → [2, 8], [3, 3]  
2 → [4, 4], [5, 15]  
3 → [4, 13]  
4 → [5, 2]  
5  
```


### 📌 2. 최단 거리 배열 초기화

| **노드 번호** | **1** | **2** | **3** | **4** | **5** |
|:-:|:-:|:-:|:-:|:-:|:-:|
| 거리 D[] | 0 | ∞ | ∞ | ∞ | ∞ |


## #📌 3. 1번 노드 선택 → 인접 노드 거리 업데이트

- 1 → 2 (8) → D[2] = 8
- 1 → 3 (3) → D[3] = 3


| **노드 번호** | **1** | **2** | **3** | **4** | **5** |
|:-:|:-:|:-:|:-:|:-:|:-:|
| 거리 D[] | 0 | 8 | 3 | ∞ | ∞ |


→ 1번 선택



## 📌 4. 3번 노드 선택 → 인접 노드 4 업데이트

- 3 → 4 (13) → D[4] = min(∞, 3 + 13) = 16

| **노드 번호** | **1** | **2** | **3** | **4** | **5** |
|:-:|:-:|:-:|:-:|:-:|:-:|
| 거리 D[] | 0 | 8 | 3 | 16 | ∞ |


## 📌 5. 2번 노드 선택 → 4, 5 갱신 시도

- 2 → 4 (4) → D[4] = min(16, 8 + 4) = 12
- 2 → 5 (15) → D[5] = 8 + 15 = 23

| **노드 번호** | **1** | **2** | **3** | **4** | **5** |
|:-:|:-:|:-:|:-:|:-:|:-:|
| 거리 D[] | 0 | 8 | 3 | 12 | 23 |




## 📌 6.  4번 노드 선택 → 5 갱신 시도

| **노드 번호** | **1** | **2** | **3** | **4** | **5** |
|:-:|:-:|:-:|:-:|:-:|:-:|
| 거리 D[] | 0 | 8 | 3 | 12 | 14 |


---

## 🔁 회고
- 다익스트라는 양의 가중치를 가지는 그래프에서만 사용 가능하며, 우선순위 큐를 통해 효율적으로 최단 거리를 갱신함
- 음수 간선이 포함된 경우에는 벨만-포드(Bellman-Ford) 알고리즘 사용 필요

---
