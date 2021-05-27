# **[프로그래머스 | 스택/큐 Lv.2] 프린터**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42587)**

[문제 설명 이미지]

## **🤔문제 풀이 방식**
``` js
구현문제.

중요도가 높은 문서를 먼저 인쇄.
1. 인쇄 대기 목록의 가장 앞에 있는 문서(J)를 대기목록에서 꺼냄
2. 나머지 인쇄 대기목록에서 J보다 중요도가 높은 문서가 한 개라도 존재하면 J를 인쇄 대기 목록 가장 마지막에 넣음
3. 그렇지 않으면 인쇄함.
내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 return

문서 중요도를 각각 Queue와 PriorityQueue에 넣는다.
`Queue`는 문서의 `대기목록`에 해당하고
`PriorityQueue`는 문서 대기목록을 탐색할 때 `현재 선택된 문서의 중요도`가 대기목록에 남은 문서들 중에서 가장 높은 지 판단하기위한 용도로 사용.

```
# 구현 코드

**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/%ED%94%84%EB%A6%B0%ED%84%B0.java)**

``` java
import java.util.Comparator;
import java.util.LinkedList;
import java.util.PriorityQueue;
import java.util.Queue;

// https://programmers.co.kr/learn/courses/30/lessons/42587?language=java
public class 프린터 {
    
    public int solution(int[] priorities, int location) {
        int answer = 0;

        Queue<Document> readyQueue = new LinkedList<>();

        // 문서의 중요도 최대 값을 얻기 위한 pq 내림차순 정렬
        PriorityQueue<Integer> pq = new PriorityQueue<>(Comparator.reverseOrder());

        for (int i = 0; i < priorities.length; i++) {
            readyQueue.offer(new Document(i, priorities[i]));
            pq.offer(priorities[i]);
        }


        while (!readyQueue.isEmpty()) {
            Document poll = readyQueue.poll();

            // 우선 순위가 가장 높다면 출력하고 출력 count++;
            if (poll.priority == pq.peek()) {
                pq.poll();
                answer++;
                // location이 일치하면 반복 종료
                if (poll.location == location) {
                    break;
                }
            } else {
                // 아니면 맨 뒤에넣어줌
                readyQueue.offer(poll);
            }
        }


        return answer;
    }

    class Document {
        int location;
        int priority;

        public Document(int location, int priority) {
            this.location = location;
            this.priority = priority;
        }
    }
}
```

## **✔제출 결과**

[제출 이미지]
