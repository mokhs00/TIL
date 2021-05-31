# **[프로그래머스 | 힙(Heap) Lv.2] 더 맵게**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

<U>**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42626?language=java)**</U>

[문제 설명 이미지]

## **🤔문제 풀이 방식**
``` js

모든 음식의 스코빌 지수를 K 이상을 만들어야한다.

그 방법은 스코빌 지수가 가장 낮은 두 음식을 섞는 것이다.
섞을 때는 아래와 같은 방법으로 섞는다.
`섞은 음식의 스코빌 지수` = `가장 맵지 않은 음식의 스코빌 지수 + 두번째로 맵지 않은 음식의 스코빌 지수 * 2`

모든 음식의 스코빌 지수가 K 이상이 될 때까지 반복한다.

음식을 섞는 최소 횟수를 return

* 만약, 모두 탐색한 후에 스코빌 지수가 가장 낮은 음식이 K보다 작다면 -1을 return

PriorityQueue 오름차순 정렬을 이용해서 풀자.



```
# 구현 코드

<U>**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/%EB%8D%94_%EB%A7%B5%EA%B2%8C.java)**</U>

``` java
import java.util.PriorityQueue;

public class 더_맵게 {
    
    public int solution(int[] scoville, int K) {
        int answer = 0;

        PriorityQueue<Integer> pq = new PriorityQueue<>();

        for (int i = 0; i < scoville.length; i++) {
            pq.offer(scoville[i]);
        }

        while (pq.size() > 1) {
            Integer firstPeek = pq.peek();

            // 만약 가장 스코빌 지수가 낮은 음식이 K보다 작다면 그 다음으로 작은 음식을 섞는다.
            if (firstPeek < K) {
                Integer first = pq.poll();
                Integer second = pq.poll();

                pq.offer(first + second * 2);
                answer++;
            } else {
                break;
            }
        }

        // 모두 탐색한 후에 스코빌 지수가 가장 낮은 음식이 K보다 작다면 -1을 return
        if (pq.peek() < K) {
            return -1;
        }


        return answer;
    }
}
```

## **✔제출 결과**

[제출 이미지]
