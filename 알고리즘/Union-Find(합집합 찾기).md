# Union-Find 알고리즘

# 개요
- 대표적인 그래프 알고리즘
- '합집합 찾기'에 사용됨
- 서로소 집합(Disjoint-Set) 알고리즘이라고 불리기도 함.
- 여러 개의 노드가 존재할 때 두 개의 노드를 선택해서, 두 노드가 서로 같은 그래프에 속하는 지(=탐색할 수 있는지)판별하는 알고리즘

# 구현 방법
1. 크기가 n인 부모 노드를 가리킬 배열(parents[])를 선언
2. parents[i]의 값을 i로 초기화(자기 자신으로 초기화)
3. 노드의 연결 정보를 확인하여 a번 노드와 b번 노드가 연결되어 있다면
    1. a > b라면 parents[b]에 parents[a]를 대입
    2. b < a라면 parents[a]에 parents[b]를 대입
4. 이제 parents[]를 이용하면 됨

# 구현 코드
https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Algorithm/UnionFind.java


# 중요
다른 그래프 탐색 알고리즘에서 응용될 수 있음

ex(크루스칼 알고리즘 : Kruskal Algorithm)