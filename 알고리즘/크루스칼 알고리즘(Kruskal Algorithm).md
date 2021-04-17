# 크루스칼 알고리즘(Kruskal Algorithm)

# 개요
- 가장 적은 비용으로 모든 노드를 연결하기 위한 알고리즘
- 최소 비용 신장 트리([MST](https://en.wikipedia.org/wiki/Minimum_spanning_tree))를 만들기 위한 대표적인 알고리즘
- 모든 노드를 최소 비용으로 연결하면 되므로 비용이 적은 간선 순으로 그래프에 포함시키면 됨

# 구현
간선의 정보(노드1, 노드2, 비용)가 주어진다고 하자
1. 연결할 수 있는 간선의 정보를 간선 비용 오름차순으로 정렬한다
2. 정렬된 간선을 순회하며 다음을 수행한다
    1. 노드1과 노드2가 연결되어 있지 않다면,
    간선 정보에 맞게 그래프에 노드를 추가한다(노드1, 노드2)
    2. 노드1과 노드2가 연결되어 있다면, continue;(다음 간선으로 넘어간다)

# 구현 코드 및 예제
[구현코드](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Algorithm/Kruskal.java)


[예제1 : 프로그래머스 - 섬 연결하기](https://programmers.co.kr/learn/courses/30/lessons/42861?language=java#)



# 중요
구현 2번 과정에서 각 노드가 연결되어 있지을 확인하기 위해서
[Union-Find](https://github.com/mokhs00/TIL/blob/main/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98/Union-Find(%ED%95%A9%EC%A7%91%ED%95%A9%20%EC%B0%BE%EA%B8%B0).md) 알고리즘을 사용한다
