# 깊이 우선 탐색(DFS)


# 개요 
- 그래프 탐색 알고리즘의 대표적인 예
- 특정 노드에서 시작하여 깊이(Depth)를 우선으로 탐색하는 알고리즘
- 스택을 이용하거나 재귀 함수를 이용하여 구현(*재귀 호출은 스택과 유사한 구조를 가짐)

# 구현

- 인접 리스트로 연결 관계가 표현된 그래프가 주어진 상황(구현 코드 내 주석에 그래프 모양을 적어둠)
- 방문처리를 할 배열이 필요하다.

1. 시작 노드를 스택에 넣는다.
2. 스택에서 노드를 pop하여 해당 노드를 방문처리하고 해당 노드와 연결되어 있고 방문되지 않은 노드를 스택에 add
3. 스택이 비어있지 않을 때까지 2번을 반복

# 구현 코드

https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Algorithm/DFS.java