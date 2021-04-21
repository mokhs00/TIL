# 플로이드 워셜 알고리즘(Floyd-Warshall Algorithm)

# 개요
- 그래프에서 모든 노드에서 모든 노드로 가는 최단 거리를 구하는 알고리즘
- 노드의 수가 적을 때 사용하면 빠른 구현이 가능함.
- 시간 복잡도 O(n^3)

### 예시를 하나 들어보자
(A -> B : 3)를 "A에서 B로 가는 비용이 3이다"로 정의하고 시작하자

- (A -> B : 3), (A -> C : 10), (B -> C : 3) 일때
- A에서 C로 가는 경우는 (A -> B -> C) 혹은 (A -> C)가 된다.
- 이 중 "A에서 바로 C로 가는 경우", 비용은 10
- "A에서 B를 거쳐 C로 가는 경우", 비용은 8이므로 아래의 경우가 최적의 비용으로 이동할 수 있는 경우이다.
- 따라서 "A에서 C로 가는 최적의 비용은 8"을 기록한다.

### 이는 [다이나믹 프로그래밍](https://github.com/mokhs00/TIL/blob/main/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98/%EB%8B%A4%EC%9D%B4%EB%82%98%EB%AF%B9%20%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D(DP).md)에도 속한다
위 과정을 정리해보면 아래와 같은 점화식을 얻을 수 있음
 
a노드에서 c노드로 가는 비용의 최소값 = min(a노드에서 바로 c노드로 가는 비용, a노드에서 b노드를 거쳐 c노드로 가는 비용)
 
> **dp[a][c] = min(dp[a][c], dp[a][b] + dp[b][c])**

# 구현

- graph를 INF(연결되어 있지 않음)으로 채운다.
- graph에 각 노드 간 연결 정보와 비용을 기록한다.
- 3중 반복문을 돌린다(현재노드 a, 다음노드 b, 거쳐갈 노드 c)
- 매 순간 dp[a][c] = min(dp[a][c], dp[a][b] + dp[b][c])를 수행한다.


# 구현 코드

https://github.com/mokhs00/CodingTest/blob/b662d7a15d2ab3ca9bab26c11aa3f81ac8171331/src/main/java/Algorithm/FloydWarshall.java

