# **[프로그래머스 | 스택/큐 Lv.2] 다리를 지나는 트럭**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42583?language=java)**

[문제 설명 이미지]

## **🤔문제 풀이 방식**
``` js
`모든 트럭이 다리를 건너는데 걸리는 최소 시간을 구해라!`
*`트럭은 1초에 1만큼 움직임.`
다리의 길이 : bridge_length
다리가 견딜 수 있는 무게 : weight

`*트럭이 다리에 완전히 오르지 않은 경우 이 트럭의 무게는 고려하지 않음.`

1. `대기 큐`, `다리 큐`를 각각 두고 이용하자!
다리 큐는 다리의 각 위치에 올라가 있는 무게를 기록함
`0은 그 위치에 트럭이 없음을 의미한다.`
ex) 0 0 0 7

2. 다리 위 무게 계산을 위한 `totalWeight` 를 둔다.

대기 큐가 없을 때까지 트럭 이동 및 트럭 추가를 반복하고
대기 큐가 비어있다면,
다리 위에 트럭이 존재하는 동안(totalWeight > 0)동안 같은 트럭을 이동시킨다.

정리하면 다음과 같다

로직 :
- `다리 큐.poll`한 값이 0이 아니라면 `트럭이 다리 위에서 빠져나감을 의미`하므로 `totalWeight`에서 뺀다. 
- 대기 트럭의 맨 앞에 있는 트럭을 다리 위에 올릴 수 있다면 올리고 그 무게를 `totalWeight에` 추가한다.
- 올릴 수 없다면 `다리 큐에 0을 추가`
- 이후 시간을 증가시켜준다 `time++;` (아래 코드에선 answer 변수를 사용한다.)
```
# 구현 코드

**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/%EB%8B%A4%EB%A6%AC%EB%A5%BC_%EC%A7%80%EB%82%98%EB%8A%94_%ED%8A%B8%EB%9F%AD.java)**

``` java

package Programmers;

import java.util.LinkedList;
import java.util.Queue;

// https://programmers.co.kr/learn/courses/30/lessons/42583
public class 다리를_지나는_트럭 {
    
    public int solution(int bridge_length, int weight, int[] truck_weights) {
        int answer = 0; // = time
        Queue<Integer> readyQueue = new LinkedList<>();
        Queue<Integer> bridgeQueue = new LinkedList<>();

        for (int truck_weight : truck_weights) {
            readyQueue.offer(truck_weight);
        }

        for (int i = 0; i < bridge_length; i++) {
            bridgeQueue.offer(0);
        }

        int totalWeight = 0;

        while (!readyQueue.isEmpty()) {
            answer++;
            Integer bridgeLast = bridgeQueue.poll();
            if (bridgeLast > 0) {
                totalWeight -= bridgeLast;
            }

            Integer nextTruckWeight = readyQueue.peek();

            if (totalWeight + nextTruckWeight <= weight) {
                readyQueue.poll();
                bridgeQueue.offer(nextTruckWeight);
                totalWeight += nextTruckWeight;
            } else {
                bridgeQueue.offer(0);
            }
        }

        while (totalWeight > 0) {
            answer++;
            Integer bridgeLast = bridgeQueue.poll();
            totalWeight -= bridgeLast;

        }


        return answer;
    }
}


```

## **✔제출 결과**

[제출 이미지]
